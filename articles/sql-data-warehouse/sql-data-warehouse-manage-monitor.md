---
title: Monitorowanie obciążenia przy użyciu widoków DMV | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorowanie obciążenia przy użyciu dynamicznych widoków zarządzania.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
origin.date: 04/12/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: ff1f613dfdfb5c43b727bcc9c7f7a1f0afca0975
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748779"
---
# <a name="monitor-your-workload-using-dmvs"></a>Monitor your workload using DMVs
W tym artykule opisano, jak używać dynamicznych widoków zarządzania (DMV) do monitorowania obciążenia. Dotyczy to również badanie wykonywania zapytań w usłudze Azure SQL Data Warehouse.

## <a name="permissions"></a>Uprawnienia
Aby wysłać zapytanie DMV, w tym artykule, musisz mieć uprawnienie VIEW DATABASE STATE lub FORMANTU. Zazwyczaj musi udzielać VIEW DATABASE STATE jest preferowany uprawnień, ponieważ jest znacznie bardziej restrykcyjne.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitor połączeń
Wszystkie nazwy logowania w usłudze SQL Data Warehouse są rejestrowane w [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Ten widok DMV zawiera ostatnie 10.000 nazwy logowania.  Session_id jest kluczem podstawowym i jest sekwencyjnie przypisany do każdego nowego logowania.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Wykonywanie zapytania monitora
Wszystkie zapytania wykonywane w usłudze SQL Data Warehouse są rejestrowane w [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  Ten widok DMV zawiera ostatnie 10.000 wykonanych zapytań.  Request_id jednoznacznie identyfikuje każde zapytanie i jest klucz podstawowy dla tej DMV.  Request_id przypisano sekwencyjnie dla każdego nowego zapytania i jest poprzedzony znakiem QID, który oznacza identyfikator zapytania.  Ten widok DMV dla danego session_id zapytań zawiera wszystkie zapytania dla danego logowania.

> [!NOTE]
> Procedury składowane użyć wielu identyfikatorów żądań.  Identyfikatory żądania są przypisywane w kolejności sekwencyjnej. 
> 
> 

Poniżej przedstawiono kroki, aby wykonać, aby zbadać plany wykonywania zapytań i godziny dla określonej kwerendy.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>KROK 1: Identyfikować zapytania, które chcesz zbadać
```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Z poprzednich wyników kwerendy **należy pamiętać, identyfikator żądania** zapytania, które chcesz zbadać.

Zapytania w programie **zawieszone** stanu można umieścić w kolejce z powodu dużej liczby aktywnych zapytań. Te zapytania są również wyświetlane w [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql) czeka zapytania z typem UserConcurrencyResourceType. Aby uzyskać informacji na temat limitów współbieżności, zobacz [warstwy wydajności](performance-tiers.md) lub [klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md). Zapytania można także poczekać z innych powodów, takich jak uzyskać blokady obiektu.  Jeśli zapytanie jest oczekując na zasób, zobacz [badanie zapytań oczekujących dla zasobów] [ Investigating queries waiting for resources] dalej na dół w tym artykule.

Aby uprościć wyszukiwania zapytań w [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) tabeli, użyj [etykiety] [ LABEL] przypisać komentarz do Twojego zapytania, które mogą być wyszukiwane w sys.dm_pdw_exec_ Wyświetlanie żądań.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>KROK 2: Badanie w planie zapytania
Identyfikator żądania umożliwia pobieranie zapytania rozproszonego plan SQL (DSQL) z [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Gdy DSQL plan trwa dłużej niż oczekiwano, przyczyną może być planu złożone z wielu kroków DSQL lub tylko jeden krok zajmuje dużo czasu.  Jeśli planowane jest wielu kroków za pomocą kilku operacji przenoszenia, należy wziąć pod uwagę optymalizacji Twojej dystrybucji tabeli do zmniejszenia przenoszenia danych. [Dystrybucja tabel] [ Table distribution] artykule opisano, dlaczego dane należy przenieść do rozwiązania kwerendy i opisano kilka strategii dystrybucji, aby zminimalizować przenoszenia danych.

Zmuszony do dalszego badania szczegółowe informacje o jednym kroku *operation_type* kolumny długotrwałych krok zapytania i zwróć uwagę, **indeks kroku**:

* Wykonaj krok 3a dla **operacji SQL**: ReturnOperation OnOperation, RemoteOperation.
* Wykonaj krok 3b dla **operacje przenoszenia danych**: ShuffleMoveOperation BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>KROK 3a: Badanie SQL w rozproszonych bazach danych
Użyj Identyfikatora żądania i indeks kroku można pobrać szczegółów z [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], który zawiera informacje o wykonaniu tego kroku zapytania na wszystkich rozproszonych baz danych.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Gdy krok zapytania jest uruchomiona, [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] może służyć do pobierania plan szacowany programu SQL Server z pamięci podręcznej planu programu SQL Server dla kroku systemem dla określonych dystrybucji.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>KROK 3b: Badanie przenoszenia danych w rozproszonych bazach danych
Umożliwia pobieranie informacji o kroku przepływu danych, uruchomione na każdym dystrybucji z identyfikator żądania i indeks kroku [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Sprawdź *total_elapsed_time* kolumny, aby zobaczyć, jeśli określonych dystrybucji trwa znacznie dłużej niż inne w przypadku przenoszenia danych.
* Dystrybucji długotrwałych Sprawdź *rows_processed* kolumnę, aby sprawdzić, czy liczba wierszy jest przenoszony z tą dystrybucją jest znacznie większa niż inne. Jeśli tak, to wyszukiwanie może wskazywać niesymetryczności danych podstawowych.

Jeśli zapytanie jest uruchomione, [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] może służyć do pobierania plan szacowany programu SQL Server z pamięci podręcznej planu programu SQL Server dla aktualnie uruchomionych krok SQL, w ramach określonego Dystrybucja.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitor oczekiwania zapytań
Jeśli użytkownik zauważy, czy zapytanie nie jest czyni postępy, ponieważ oczekuje dla zasobu, w tym miejscu jest zapytanie, która zawiera wszystkie zasoby, że zapytanie oczekuje na.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Jeśli zapytanie jest aktywnie oczekujących na zasoby z innego zapytania, a następnie stan będzie **AcquireResources**.  Jeśli zapytanie ma wszystkie wymagane zasoby, a następnie stan będzie **przyznany**.

## <a name="monitor-tempdb"></a>Monitor bazy danych tempdb
Bazy danych tempdb jest używany do przechowywania wyników pośrednich podczas wykonywania zapytania. Wysokie wykorzystanie bazy danych tempdb może prowadzić do obniżyć wydajność zapytań. Każdy węzeł w usłudze Azure SQL Data Warehouse ma około 1 TB miejsca pierwotne bazy danych TempDB. Poniżej znajdują się wskazówki dotyczące monitorowania użycia bazy danych tempdb i zmniejsza użycie bazy danych tempdb w zapytaniach. 

### <a name="monitoring-tempdb-with-views"></a>Monitorowanie bazy danych tempdb z widokami
Aby monitorować użycie bazy danych tempdb, należy najpierw zainstalować [microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) z widoku [Microsoft Toolkit dla usługi SQL Data Warehouse](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Można następnie wykonaj następujące zapytanie, aby zobaczyć użycie bazy danych tempdb na węzeł dla wszystkich wykonywanych zapytań:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```

Jeśli masz kwerendę, która zużywa duże ilości pamięci lub odebrały komunikat o błędzie powiązany przydziału bazy danych tempdb, często jest ze względu na bardzo dużych [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) lub [INSERT SELECT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Instrukcja z systemem, która kończy się niepowodzeniem w ramach operacji przenoszenia danych końcowych. To zazwyczaj można zidentyfikować jako operacja ShuffleMove w ramach planu zapytania rozproszonego przed ostatnim INSERT SELECT.

Najbardziej typowe ograniczenia jest Podziel instrukcji CTAS lub INSERT SELECT na wiele instrukcji ładowania, więc wolumin danych nie może przekroczyć 1TB na limit liczby węzłów bazy danych tempdb. Możesz również skalować na większy rozmiar, który będzie rozkładają się rozmiar bazy danych tempdb na większą liczbę węzłów, zmniejszając bazy danych tempdb na poszczególnych węzłów klastra. 

## <a name="monitor-memory"></a>Monitorowanie pamięci

Pamięć może być głównej przyczyny niskiej wydajności i poza problemy z pamięcią. Należy rozważyć skalowanie magazynu danych, jeśli okaże się użycia pamięci programu SQL Server osiągnięcia zmuszenia podczas wykonywania zapytania.

Następujące zapytanie zwraca programu SQL Server użycia i pamięci dużego wykorzystania pamięci na węzeł:   
```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB, 
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2 
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-size"></a>Monitorowanie rozmiaru dziennika transakcji
Następujące zapytanie zwraca rozmiar dziennika transakcji na poszczególnych dystrybucji. Jeśli jeden z plików dziennika zbliża się do 160 GB, należy rozważyć skalowanie w górę wystąpienia lub ograniczanie rozmiar transakcji. 
```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id 
FROM sys.dm_pdw_nodes_os_performance_counters 
WHERE 
instance_name like 'Distribution_%' 
AND counter_name = 'Log File(s) Used Size (KB)'
```
## <a name="monitor-transaction-log-rollback"></a>Monitorowanie wycofanie dziennika transakcji
Jeśli zapytania są kończy się niepowodzeniem lub zajmuje dużo czasu, aby kontynuować, należy sprawdzić, a następnie monitorować w przypadku wszystkich transakcji wycofywanie.
```sql
-- Monitor rollback
SELECT 
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji dotyczących dynamicznych widoków zarządzania, zobacz [widoki systemowe][System views].


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx

<!-- Update_Description: update meta properties, wording update -->