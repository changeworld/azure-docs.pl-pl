---
title: Monitorowanie wydajności usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykrywać i diagnozować typowych problemów z wydajnością za pomocą dynamicznych widoków zarządzania do monitorowania Microsoft Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 371632a28d22583f8b206e4d8b9d2b6b4e510ab0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103767"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Monitorowanie wydajności usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania

Microsoft Azure SQL Database umożliwia podzbiór dynamicznych widoków zarządzania do diagnozowania problemów z wydajnością, których przyczyną może być zablokowany lub długotrwałe zapytania, zasobów, wąskich gardeł, planów zapytań niską i tak dalej. Ten temat zawiera informacje na temat sposobu wykrywania typowych problemów z wydajnością za pomocą dynamicznych widoków zarządzania.

Usługa SQL Database obsługuje częściowo trzy kategorie dynamicznych widoków zarządzania:

- Związane z bazy danych dynamicznych widoków zarządzania.
- Powiązane z wykonywaniem dynamicznych widoków zarządzania.
- Transakcji powiązanych z dynamicznych widoków zarządzania.

Aby uzyskać szczegółowe informacje o dynamicznych widoków zarządzania, zobacz [dynamicznych widoków zarządzania i funkcje (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) w dokumentacji SQL Server — książki Online.

## <a name="permissions"></a>Uprawnienia

W usłudze SQL Database zapytań dynamicznych widoków zarządzania wymaga **VIEW DATABASE STATE** uprawnienia. **VIEW DATABASE STATE** uprawnienie zwraca informacje o wszystkich obiektach w bieżącej bazie danych.
Aby udzielić **VIEW DATABASE STATE** uprawnienie do użytkownika konkretnej bazy danych, uruchom następujące zapytanie:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

W wystąpieniu programu SQL Server w środowisku lokalnym dynamicznych widoków zarządzania zwraca informacje o stanie serwera. W usłudze SQL Database zwracają informacje dotyczące bieżącej logicznej bazy danych tylko.

## <a name="identify-cpu-performance-issues"></a>Identyfikowanie problemów z wydajnością procesora CPU

Jeśli użycie procesora CPU przekracza 80% przez dłuższy czas, należy wziąć pod uwagę następujące kroki:

### <a name="the-cpu-issue-is-occurring-now"></a>Procesor CPU się problem teraz

Jeśli problem występuje teraz, istnieją dwa możliwe scenariusze:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Wiele pojedynczych zapytań, które łącznie zużywają wysokie użycie procesora CPU

Użyj następującego zapytania, aby zidentyfikować zapytania dotyczącego początkowych wartości skrótów:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM(SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
     FROM(SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
               CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
     GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Długotrwałe zapytania, których wartość użycia procesora CPU nadal są uruchomione.

Aby zidentyfikować te zapytania, należy użyć następującej kwerendy:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
     CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>Problem z procesora CPU wystąpiły w przeszłości

W przypadku wystąpienia problemu w przeszłości, a użytkownik chce główny ustalić przyczynę problemu, należy użyć [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Użytkownicy mający dostęp do bazy danych umożliwia języka T-SQL Query Store zapytania o dane.  Query Store domyślne konfiguracje Użyj szczegółowości równej 1 godz.  Użyj następującego zapytania do wyszukania w działanie zapytań zużywających najwięcej zasobów wysokie użycie procesora CPU. Ta kwerenda zwraca najpopularniejsze 15 zapytania zużywające procesora CPU.  Pamiętaj, aby zmienić `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Po zidentyfikowaniu problematycznych zapytań, nadszedł czas na dostosowywanie te zapytania, aby zmniejszyć wykorzystanie procesora CPU.  Jeśli nie masz czasu na dostosowywanie zapytania można również uaktualnić poziomu usługi bazy danych, aby obejść ten problem.

## <a name="identify-io-performance-issues"></a>Identyfikowanie problemów z wydajnością we/wy

Po określeniu problemów z wydajnością we/wy, są następujące typy najważniejsze oczekiwania związane z problemami z operacji We/Wy:

- `PAGEIOLATCH_*`

  W przypadku problemów we/wy pliku danych (w tym `PAGEIOLATCH_SH`, `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP`).  Jeśli nazwa typu oczekiwania ma **We/Wy** , wskazuje na problem we/wy. Jeśli ma nie **We/Wy** w nazwie oczekiwania zatrzaśnięcia strony, wskazuje na inny typ problemu (na przykład tempdb rywalizacji o zasoby).

- `WRITE_LOG`

  W przypadku problemów we/wy dziennika transakcji.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Jeśli występuje teraz problem we/wy

Użyj [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) lub [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) się `wait_type` i `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Identyfikowanie danych i rejestrować użycie operacji We/Wy

Użyj następującego zapytania, aby zidentyfikować dane i rejestrować użycie operacji We/Wy. Jeśli we/wy danych lub dziennika przekracza 80%, oznacza to, że użytkownicy korzystający dostępne we/wy dla warstwy usług bazy danych SQL.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Jeśli został osiągnięty limit operacji We/Wy, masz dwie opcje:

- Opcja 1: Uaktualnij rozmiar obliczeń lub warstwę usługi
- Opcja 2: Identyfikowanie i dostosowywanie zapytania zużywające większość we/wy.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Wyświetl związanych bufor We/Wy przy użyciu Store zapytania

Dla opcji 2 możesz używać następujące zapytanie względem Store zapytań dla operacji We/Wy związanych bufor, aby wyświetlić śledzonych aktywności z ostatnich dwóch godzin:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Wyświetl dziennik łączna liczba operacji We/Wy dla WRITELOG oczekuje

Jeśli typ oczekiwania `WRITELOG`, użyj następującego zapytania, aby wyświetlić dziennik łączna liczba operacji We/Wy przez instrukcję:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identyfikowanie `tempdb` problemy z wydajnością

Po określeniu problemów z wydajnością we/wy, u góry oczekiwania typy związane z `tempdb` problemów jest `PAGELATCH_*` (nie `PAGEIOLATCH_*`). Jednak `PAGELATCH_*` czeka nie zawsze oznacza, że masz `tempdb` rywalizacji o zasoby.  Tego oczekiwania może również oznaczać, że istnieje obiekt użytkownika danych strony rywalizacji o zasoby z powodu równoczesnych żądań dla tej samej strony danych. Aby upewnić się, dalsze `tempdb` rywalizacji o zasoby, użyj [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) aby upewnić się, że wartość wait_resource rozpoczyna się od `2:x:y` gdzie 2 jest `tempdb` to identyfikator bazy danych `x` identyfikator pliku i `y` jest identyfikator strony.  

Dla bazy danych tempdb rywalizacji o zasoby, powszechnie używaną metodą jest zmniejszenie i ponownego pisania kodu aplikacji, która opiera się na `tempdb`.  Typowe `tempdb` użycie obszarów, należą:

- Tabele tymczasowe
- zmienne tabeli
- Parametry z wartościami przechowywanymi w tabeli
- Użycie magazynu wersji (w szczególności skojarzone z długotrwałych transakcji)
- Zapytania, które mają plany zapytań, które używają sortowania, sprzężenia wyznaczania wartości skrótu i buforowanie

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Najpopularniejsze zapytania, używających zmiennych tabel i tabele tymczasowe

Aby zidentyfikować najpopularniejsze zapytania, używających zmiennych tabel i tabelach tymczasowych, należy użyć następującej kwerendy:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Identyfikowanie długotrwałej transakcji

Użyj następujące zapytanie, aby zidentyfikować długie uruchomione transakcje. Długotrwałe transakcje uniemożliwiają Oczyszczanie magazynu wersji.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identyfikowanie pamięci grant oczekiwania problemy z wydajnością

Jeśli najwyższego oczekiwania typu `RESOURCE_SEMAHPORE` i nie masz problemem wysokiego użycia procesora CPU, może być pamięci udzielić problem oczekiwania.

### <a name="determine-if-a-resourcesemahpore-wait-is-a-top-wait"></a>Sprawdza, czy `RESOURCE_SEMAHPORE` oczekiwania jest najważniejsze oczekiwania

Użyj następującego zapytania, aby określić, czy `RESOURCE_SEMAHPORE` oczekiwania jest najważniejsze oczekiwania

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>Wysokiej instrukcje używania pamięci tożsamości

Aby zidentyfikować wysokiej instrukcje używania pamięci, należy użyć następującej kwerendy:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Identyfikowanie przyznaje aktywnej pamięci pierwszych 10

Użyj następującego zapytania, aby zidentyfikować najważniejsze 10 przyznaje aktywnej pamięci:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Obliczanie rozmiaru bazy danych i obiektów

Następujące zapytanie zwraca rozmiar bazy danych (w megabajtach):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

Następujące zapytanie zwraca rozmiar poszczególnych obiektów (w MB) w bazie danych:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Monitorowanie połączeń

Możesz użyć [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) widok, aby pobrać informacje o połączeniach nawiązać z określonego serwera usługi Azure SQL Database i szczegółów dotyczących każdego połączenia. Ponadto [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) widok faktycznie jest pomocny podczas pobierania informacji o wszystkich aktywnych sesji użytkowników, jak i wewnętrznych zadaniach.
Następujące zapytanie pobiera informacji na temat bieżącego połączenia:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Podczas wykonywania **sys.dm_exec_requests** i **widoków sys.dm_exec_sessions**, jeśli masz **VIEW DATABASE STATE** uprawnień w bazie danych, zostanie wyświetlony, wykonywanie wszystkich Sesje w bazie danych. w przeciwnym razie zostanie wyświetlony w bieżącej sesji.

## <a name="monitor-resource-use"></a>Monitorowanie wykorzystania zasobów

Można monitorować za pomocą użycia zasobów [SQL Database Query Performance Insight](sql-database-query-performance.md) i [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Można również monitorować użycie za pomocą te dwa widoki:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats

Możesz użyć [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) widoku w każdej bazie danych SQL. **Sys.dm_db_resource_stats** widok pokazuje ostatnie dane wykorzystania zasobów względem warstwy usług. Średni procent procesora CPU, we/wy danych, zapisuje dziennik i pamięci są rejestrowane co 15 sekund i są przechowywane przez 1 godzinę.

Ponieważ ten widok zawiera bardziej szczegółowe Sprawdź wykorzystanie zasobów, użyj **sys.dm_db_resource_stats** pierwszy do dowolnej analizy bieżący stan lub dotyczącymi rozwiązywania problemów. Na przykład to zapytanie Wyświetla średniego i maksymalnego wykorzystanie w bieżącej bazie danych w ciągu ostatniej godziny:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

W przypadku innych kwerend, zobacz przykłady w [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats

[Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) wyświetlać w **wzorca** bazy danych zawiera dodatkowe informacje, które mogą ułatwić monitorowanie wydajności usługi SQL database w warstwie jego określonej usługi i obliczenia rozmiaru. Dane są zbierane, co 5 minut i jest zachowywana na potrzeby około 14 dni. Ten widok jest przydatne w przypadku dłuższy okres historycznej analizy używaniu zasobów w bazie danych SQL.

Poniższy wykres pokazuje CPU wykorzystanie zasobów bazy danych Premium o rozmiarze obliczeń P2 za każdą godzinę w ciągu tygodnia. Ten wykres jest uruchamiany w każdy poniedziałek, pokazuje 5 dni roboczych i następnie wyświetla weekendy, znacznie mniej sytuacji w aplikacji.

![Wykorzystanie zasobów bazy danych SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Dane, ta baza danych ma obecnie szczytowego obciążenia procesora CPU nieco ponad 50 procent użycia Procesora względem P2 obliczenia rozmiaru (południe we wtorek). Jeśli procesora CPU jest dominującym czynnikiem profil zasobów aplikacji, następnie można zdecydować, czy P2 jest rozmiar obliczeń prawo, aby zagwarantować, że obciążenie zawsze pasuje do. Jeśli spodziewasz się aplikacji z upływem czasu, to dobry pomysł, aby mieć buforu dodatkowych zasobów, aby aplikacja nigdy nie dociera do poziomu wydajności limit. Zwiększenie rozmiaru obliczeń może pomóc uniknąć błędów widoczne dla klientów, które mogą wystąpić, gdy baza danych nie ma wystarczająco dużo mocy do przetwarzania żądań, szczególnie w środowiskach wrażliwy na opóźnienia. Przykładem jest bazy danych, który obsługuje aplikację, która umożliwia malowanie stron sieci Web na podstawie wyników wywołań bazy danych.

Pozostałe typy aplikacji może interpretować tego samego wykresu. Na przykład jeśli aplikacja podejmuje próbę przetwarzania listy płac danych każdego dnia i ma ten sam wykres, tego rodzaju "zadania usługi batch" model może poradzić w rozmiarze obliczeniowe P1. Rozmiar obliczeniowe P1 ma 100 jednostek Dtu w porównaniu do 200 jednostek Dtu w P2 obliczenia rozmiaru. Rozmiar obliczeniowe P1 zapewnia połowa wydajności P2 obliczenia rozmiaru. Dlatego 50 procent użycia procesora CPU w P2 jest równa 100 procent użycia procesora CPU w P1. Aplikacja nie ma limitów czasu, może być niezależnie od tego, jeżeli wykonanie zadania trwa 2 godziny, czyli 2,5 godzin, jeśli zostanie wykonana już dziś. Prawdopodobnie aplikację z tej kategorii można użyć rozmiaru obliczeń P1. Możesz korzystać z zalet fakt, że są okresy w ciągu dnia, podczas wykorzystania zasobów jest krótszy, dzięki czemu wszelkie "duże szczytowe" może być rozlane dane za pośrednictwem w jednym z koryta w dalszej części tego dnia. Rozmiar obliczeniowe P1 może być dobrym dla tego rodzaju aplikacji i oszczędzać pieniądze, tak długo, jak zakończyć zadania w czasie każdego dnia.

Usługa Azure SQL Database udostępnia używane informacje o zasobach dla każdej bazy danych dla aktywnej w **sys.resource_stats** widoku **wzorca** bazy danych na każdym serwerze. Dane w tabeli są agregowane dla 5-minutowych interwałach. Przy użyciu warstwy usług podstawowa, standardowa i Premium danych może zająć więcej niż 5 minut, aby są wyświetlane w tabeli, dzięki czemu te dane są bardziej użyteczna w przypadku analizy historycznej, a nie analizy prawie w czasie rzeczywistym. Zapytanie **sys.resource_stats** wyświetlania, aby wyświetlić najnowszą historię bazy danych i do sprawdzania, czy rezerwacja została wybrana opcja dostarczonych wydajności, chcesz, aby w razie.

> [!NOTE]
> Musi być podłączony do **wzorca** bazy danych serwera usługi SQL Database, aby wykonać zapytanie **sys.resource_stats** w poniższych przykładach.

Ten przykład pokazuje, jak dane w tym widoku jest uwidaczniany:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![W widoku wykazu sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

W kolejnym przykładzie pokazano różne sposoby, w którym można **sys.resource_stats** widoku, aby uzyskać informacje o tym, jak usługi SQL database korzysta z zasobów w katalogu:

1. Aby przyjrzeć się zasobu z ostatniego tygodnia dla userdb1 bazy danych, można uruchomić tego zapytania:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Aby ocenić, jak dobrze pasuje do rozmiaru obliczeń obciążenia, musisz przejść do każdego aspektu metryk zasobów: Procesora CPU, operacje odczytu, zapisu, liczba procesów roboczych i liczbę sesji. Poniżej przedstawiono poprawioną wykonywać zapytania za pomocą **sys.resource_stats** zgłosić średnie i maksymalne wartości tych metryk zasobów:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Dzięki tym informacjom o średnie i maksymalne wartości Wszystkie metryki zasobu można ocenić, jak dobrze obciążenia pasuje do rozmiaru obliczeń, który został wybrany. Zazwyczaj średnie wartości z **sys.resource_stats** zapewniają dobrą punktu odniesienia, aby używać względem rozmiar docelowy. Powinna to być Twoje hokejowego pomiaru podstawowego. Aby uzyskać przykład być może używasz warstwie usługi standardowa o rozmiarze obliczeń S2. Średniego użycia wartości procentowe dla procesora CPU i we/wy operacji odczytu i zapisu czy poniżej 40 procent, średnia liczba procesów roboczych jest poniżej 50 i średnia liczba sesji znajduje się poniżej 200. Obciążenie może pasuje do rozmiaru obliczeń S1. To łatwo sprawdzić, czy baza danych mieści się w granicach procesu roboczego i sesji. Aby zobaczyć, czy bazy danych jest dopasowywana do niższych rozmiaru obliczeń w odniesieniu do procesora CPU, operacji odczytu i zapisu, dzielenie liczby jednostek DTU niższym obliczenia rozmiaru przez liczbę jednostek DTU bieżącego rozmiaru obliczeń, a następnie pomnożyć wynik przez 100:

    ```S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40```

    Wynik jest względnej wydajności różnicy między tymi dwoma obliczenia rozmiarów w postaci wartości procentowej. Swoje użycie zasobów nie przekracza kwoty, obciążenie, może być mieści się w dolnym rozmiaru obliczeń. Jednak należy przyjrzeć się wszystkie zakresy wartości użycia zasobów w celu określenia według wartości procentowej, jak często obciążenia baz danych czy mieściły się w dolnym rozmiaru obliczeń. Następujące zapytanie Wyświetla Dopasuj procent na wymiarze zasobu, na podstawie progu o 40 procent, które firma Microsoft obliczona w tym przykładzie:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Oparte na warstwie usługi bazy danych, możesz zdecydować, czy obciążenie jest dopasowywana do niższych rozmiaru obliczeń. Jeśli poprzednie zapytanie zwraca wartości większe niż 99,9% dla wszystkich wymiarów zasobów celu obciążenie bazy danych to dostępność na poziomie 99,9 procent, obciążenie może pasuje do niższych rozmiaru obliczeń.

    Patrząc Dopasuj wartość procentową umożliwia również wgląd tego, czy należy przenieść do następnego większy rozmiar obliczeń celu usługi. Na przykład userdb1 przedstawia użycie procesora CPU dla zeszłego tygodnia:

   | Średni procent użycia Procesora | Maksymalny procent procesora CPU |
   | --- | --- |
   | 24.5 |100.00 |

    Średnie użycie procesora CPU jest o kwartał limit rozmiaru obliczeń, który będzie pasują do rozmiaru obliczeń bazy danych. Jednak wartość maksymalna pokazuje, że baza danych osiągnie limit rozmiaru obliczeń. Czy muszą przejść do następnego większy rozmiar obliczeniowych? Przyjrzyj się jak wiele razy Twoje obciążenie osiągnie 100 procent, a następnie porównaj ją z celu obciążenie bazy danych.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Jeśli to zapytanie zwraca wartość typu mniej niż 99,9% dla każdego zasobu trzech wymiarów, rozważ albo przeniesienie na następny większy rozmiar obliczeń lub zmniejsz obciążenie bazy danych SQL za pomocą technik Dostrajanie aplikacji.

4. To ćwiczenie uwzględnia również zwiększenie obciążenia przewidywany w przyszłości.

W przypadku pul elastycznych można monitorować pojedyncze bazy danych w puli za pomocą metod opisanych w tej sekcji. Można jednak również monitorować pulę jako całość. Informacje na ten temat znajdziesz w artykule [Monitor and manage an elastic pool](sql-database-elastic-pool-manage-portal.md) (Monitorowanie puli elastycznej i zarządzanie nią).

### <a name="maximum-concurrent-requests"></a>Maksymalna liczba równoczesnych żądań

Aby wyświetlić liczbę jednoczesnych żądań, należy uruchomić to zapytanie Transact-SQL w bazie danych SQL:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R;
    ```

Aby analizować obciążenia lokalnej bazy danych programu SQL Server, należy zmodyfikować tego zapytania do filtrowania konkretnej bazy danych, czy mają być analizowane. Na przykład w przypadku lokalnej bazy danych o nazwie mojabazadanych to zapytanie Transact-SQL zwraca liczbę jednoczesnych żądań w tej bazie danych:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase';
    ```

Jest to po prostu migawki w jednym punkcie w czasie. Aby lepiej zrozumieć swoje wymagania współbieżne żądania i obciążenia, należy zebrać wiele przykładów wraz z upływem czasu.

### <a name="maximum-concurrent-logins"></a>Maksymalna współbieżnych logowań

Możesz analizować Twoich wzorców użytkownika i aplikacji, aby poznać częstotliwość logowania. Można również uruchomić rzeczywistych warunkach w środowisku testowym, aby upewnić się, że nie występują to lub inne ograniczenia, które omówimy w tym artykule. Nie ma jednego zapytania lub dynamicznego widoku zarządzania (DMV), można wyświetlić równoczesnych czy zlicza logowania lub historii.

Jeśli wielu klientów używa tych samych parametrach połączenia, usługa uwierzytelnia każdego logowania. Jeśli 10 użytkowników jednocześnie połączyć się z bazą danych przy użyciu tej samej nazwy użytkownika i hasła, może to być 10 współbieżnych logowań. Ten limit dotyczy tylko czas trwania uwierzytelniania i logowania. Tych samych użytkowników 10 połączenia z bazą danych po kolei, liczba współbieżnych logowań nigdy nie będzie większa niż 1.

> [!NOTE]
> Obecnie ten limit nie ma zastosowania do baz danych w elastycznej puli.

### <a name="maximum-sessions"></a>Maksymalna liczba sesji

Aby wyświetlić liczbę bieżących aktywnych sesji, należy uruchomić to zapytanie Transact-SQL w bazie danych SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Jeśli masz analizowanie obciążeń programu SQL Server w środowisku lokalnym, należy zmodyfikować zapytanie, aby skoncentrować się na konkretnej bazy danych. To zapytanie zawarto informacje ułatwiające określenie potrzeb możliwe sesji dla bazy danych, jeżeli rozważasz przeniesienie ich do usługi Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Ponownie tych zapytań Zwróć liczba punktu w czasie. W przypadku zebrania wielu próbkach wraz z upływem czasu, będziesz mieć najlepsze zrozumienia sesji, użyj.

Analiza bazy danych SQL, statystyki historyczne można uzyskać w ramach sesji, badając [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) widoku i przeglądając **active_session_count** kolumny.

## <a name="monitoring-query-performance"></a>Monitorowanie wydajności zapytań

Wolno lub czas wykonywania kwerend mogą wykorzystywać zasoby systemowe znaczące. W tej sekcji pokazano, jak wykrywać kilka typowe problemy z wydajnością zapytań za pomocą dynamicznych widoków zarządzania. Odwołanie do starszych, ale nadal przydatne na potrzeby rozwiązywania problemów jest [Rozwiązywanie problemów z wydajnością programu SQL Server 2008](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) artykuł w witrynie Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Znajdowanie zapytań pierwszych N

Poniższy przykład zwraca informacje o najważniejszych pięciu zapytaniach uszeregowanych według średniego czasu procesora CPU. W tym przykładzie agreguje zapytania zgodnie z ich skrót zapytania, aby logicznie równoważne zapytania są pogrupowane według ich zużycia zasobów zbiorczej.

    ```sql
    SELECT TOP 5 query_stats.query_hash AS "Query Hash",
       SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
       MIN(query_stats.statement_text) AS "Statement Text"
    FROM
       (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
        ((CASE statement_end_offset
           WHEN -1 THEN DATALENGTH(ST.text)
           ELSE QS.statement_end_offset END
           - QS.statement_start_offset)/2) + 1) AS statement_text
    FROM sys.dm_exec_query_stats AS QS
    CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
    GROUP BY query_stats.query_hash
    ORDER BY 2 DESC;
    ```

### <a name="monitoring-blocked-queries"></a>Monitorowanie zablokowanych zapytania

Wolne lub długotrwałe zapytania może przyczynić się do nadmiernego wykorzystania zasobów i być konsekwencją zablokowanych zapytania. Przyczyna blokady może być niewłaściwy projekt aplikacji, plany zapytania, brak przydatnych indeksów i tak dalej. Widok sys.dm_tran_locks można użyć, aby uzyskać informacje dotyczące bieżącego działania blokowania w usłudze Azure SQL Database. Przykładowy kod, zobacz [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) w dokumentacji SQL Server — książki Online.

### <a name="monitoring-query-plans"></a>Monitorowanie planów zapytań

Plan zapytania nieefektywne może również zwiększyć użycie procesora CPU. W poniższym przykładzie użyto [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) widok, aby ustalić, które zapytanie używa najbardziej zbiorczą procesora CPU.

    ```sql
    SELECT
       highest_cpu_queries.plan_handle,
       highest_cpu_queries.total_worker_time,
       q.dbid,
       q.objectid,
       q.number,
       q.encrypted,
       q.[text]
    FROM
       (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
    ORDER BY highest_cpu_queries.total_worker_time DESC;
    ```

## <a name="see-also"></a>Zobacz także

[Wprowadzenie do usługi SQL Database](sql-database-technical-overview.md)
