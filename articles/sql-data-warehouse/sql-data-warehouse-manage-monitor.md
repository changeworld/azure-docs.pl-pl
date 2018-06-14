---
title: Monitorowanie obciążenia przy użyciu widoków DMV | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować obciążenie przy użyciu widoków DMV.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 887fa4b9f950531438986269d041189d45cdecb2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31522476"
---
# <a name="monitor-your-workload-using-dmvs"></a>Monitor your workload using DMVs
W tym artykule opisano sposób użycia dynamicznych widoków zarządzania (widoków DMV) do monitorowania obciążenia. W tym badanie wykonywania zapytania w usłudze Azure SQL Data Warehouse.

## <a name="permissions"></a>Uprawnienia
Aby odpytać widoków DMV w tym artykule, wymagane jest uprawnienie do stanu bazy danych WIDOKU lub FORMANTU. Zazwyczaj udzielającym stan bazy danych w WIDOKU jest preferowany uprawnień, ponieważ jest bardziej restrykcyjne.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitor połączenia
Są rejestrowane wszystkie logowania do usługi SQL Data Warehouse [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Ten widok DMV zawiera ostatnie 10.000 logowania.  Session_id jest kluczem podstawowym i ma przypisany sekwencyjnie dla każdego nowego logowania.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Wykonywanie zapytania monitora
Wszystkie zapytania wykonywane w magazynie danych SQL są rejestrowane w [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  Ten widok DMV zawiera ostatnie 10.000 zapytania, które są wykonywane.  Request_id unikatowo identyfikuje każde zapytanie i jest to klucz podstawowy dla tego DMV.  Request_id przypisano sekwencyjnie dla każdego nowego zapytania i jest poprzedzony QID, który oznacza identyfikator zapytania.  Wykonywanie zapytania DMV ten dla danego session_id zawiera wszystkie zapytania dotyczące danego logowania.

> [!NOTE]
> Procedury składowane używać wielu identyfikatorów żądania.  Identyfikatory żądania są przypisywane w kolejności sekwencyjnej. 
> 
> 

Poniżej przedstawiono kroki do wykonania w celu zbadania planów wykonywania kwerend i godziny dla określonego zapytania.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>: Krok 1 zapytania, które chcesz zbadać
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

Z poprzednim wyników zapytania **należy pamiętać, identyfikator żądania** zapytania, które chcesz zbadać.

Zapytania w programie **zawieszone** stanu jest umieszczany w kolejce z powodu ograniczeń współbieżności. Te zapytania są również wyświetlane w zapytaniu czeka sys.dm_pdw_waits z typem UserConcurrencyResourceType. Uzyskać informacji na temat limitów współbieżności, zobacz [warstwy wydajności](performance-tiers.md) lub [klasy zasobów do zarządzania obciążenia](resource-classes-for-workload-management.md). Zapytania można również poczekać z innych powodów, takich jak uzyskać blokady obiektu.  Jeśli zapytanie oczekuje dla zasobu, zobacz [badanie zapytania oczekiwania na zasoby] [ Investigating queries waiting for resources] dalsze w dół w tym artykule.

Aby ułatwić wyszukiwanie zapytania w tabeli sys.dm_pdw_exec_requests, należy użyć [etykiety] [ LABEL] można przypisać do zapytania, które można przeszukiwać w widoku sys.dm_pdw_exec_requests komentarz.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>Krok 2: Sprawdź plan zapytania
Aby pobrać zapytania rozproszone planu SQL (DSQL) z Użyj identyfikator żądania [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Gdy planu DSQL trwa dłużej niż oczekiwano, przyczyną może być planu złożonych z wielu kroków DSQL lub tylko jeden krok zajmuje dużo czasu.  Jeśli plan wiele kroków z kilku operacji przenoszenia, należy rozważyć optymalizacji sieci dystrybucji tabeli celu ograniczenia przepływu danych. [Tabeli dystrybucji] [ Table distribution] artykule wyjaśniono, dlaczego dane muszą zostać przeniesione do rozwiązania kwerendy i opisano kilka strategii dystrybucji, aby zminimalizować przenoszenia danych.

Aby zbadać dokładnie szczegółowe informacje dotyczące jednego kroku, *operation_type* kolumny długotrwałe kroku zapytania i Uwaga **indeks kroku**:

* Kontynuuj krok 3a dla **operacji SQL**: OnOperation, RemoteOperation, ReturnOperation.
* Kontynuuj krok 3b dla **operacji przenoszenia danych**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>KROK 3a: badanie rozproszonych baz danych SQL
Umożliwia pobieranie szczegółów z identyfikator żądania i indeks kroku [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], który zawiera informacje o wykonanie kroku zapytania na wszystkie rozproszonej bazy danych.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Po uruchomieniu kroku zapytania [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] można pobrać planu szacowany programu SQL Server z pamięci podręcznej planu programu SQL Server dla kroku uruchomionych na konkretnym dystrybucji.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>KROK 3b: badanie przenoszenia danych w bazach danych rozproszonych
Użyj Identyfikatora żądania i indeks kroku można pobrać informacji o uruchomionych na poszczególnych dystrybucji z krokiem przepływu danych [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Sprawdź *total_elapsed_time* kolumny, aby zobaczyć, jeśli określonym dystrybucyjne trwa znacznie dłużej niż innych do przenoszenia danych.
* Długotrwałe dystrybucji, sprawdź *rows_processed* kolumnę, aby sprawdzić, czy liczba wierszy jest przenoszony z tą dystrybucją jest znacznie większe niż inne. Jeśli tak, to wyszukiwanie może wskazywać pochylenia danych podstawowych.

Jeśli zapytanie jest uruchomiona, [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] można pobrać planu szacowany programu SQL Server z pamięci podręcznej planu programu SQL Server dla aktualnie uruchomionych kroku SQL w ramach określonego dystrybucji.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitor oczekujących zapytań
Jeśli użytkownik stwierdzi, że zapytanie jest nie czyni postępy, ponieważ oczekuje dla zasobu, w tym miejscu jest kwerendę, która zawiera wszystkie zasoby kwerendy oczekuje na.

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

Jeśli zapytanie jest aktywnie oczekiwania na zasoby z innego zapytania, a następnie stan będzie **AcquireResources**.  Jeśli zapytanie ma wszystkie wymagane zasoby, a następnie stan będzie **przyznany**.

## <a name="monitor-tempdb"></a>Monitor tempdb
Tempdb wysokie wykorzystanie można przyczynę niską wydajnością i poza problemy z pamięcią. Należy rozważyć skalowania magazynu danych, jeśli okaże się osiągnięcia limitów jego podczas wykonywania kwerendy w bazie danych tempdb. Poniżej opisano sposób identyfikacji użycia bazy danych tempdb na zapytanie w każdym węźle. 

Utwórz następujący widok do skojarzenia identyfikator odpowiedniego węzła sys.dm_pdw_sql_requests. Identyfikator węzła o umożliwi do używania innych przekazujące widoków DMV i Dołącz do tych tabel z sys.dm_pdw_sql_requests.

```sql
-- sys.dm_pdw_sql_requests with the correct node id
CREATE VIEW sql_requests AS
(SELECT
       sr.request_id,
       sr.step_index,
       (CASE 
              WHEN (sr.distribution_id = -1 ) THEN 
              (SELECT pdw_node_id FROM sys.dm_pdw_nodes WHERE type = 'CONTROL') 
              ELSE d.pdw_node_id END) AS pdw_node_id,
       sr.distribution_id,
       sr.status,
       sr.error_id,
       sr.start_time,
       sr.end_time,
       sr.total_elapsed_time,
       sr.row_count,
       sr.spid,
       sr.command
FROM sys.pdw_distributions AS d
RIGHT JOIN sys.dm_pdw_sql_requests AS sr ON d.distribution_id = sr.distribution_id)
```
Aby monitorować bazy danych tempdb, uruchom następujące zapytanie:

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
    INNER JOIN sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```
## <a name="monitor-memory"></a>Monitor pamięci

Pamięć może być przyczynę niską wydajnością i poza problemy z pamięcią. Należy rozważyć skalowania magazynu danych, jeśli okaże się użycie pamięci programu SQL Server osiągnięcia limitów jego podczas wykonywania zapytania.

Następujące zapytanie zwraca programu SQL Server wykorzystania użycia i pamięci pamięci na węzeł:   
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
## <a name="monitor-transaction-log-size"></a>Rozmiar dziennika transakcji monitora
Następujące zapytanie zwraca rozmiar dziennika transakcji na poszczególnych dystrybucji. Jeśli jeden z plików dziennika wkrótce osiągnie 160 GB, należy rozważyć skalowaniu wystąpienia lub ograniczenie rozmiar transakcji. 
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
## <a name="monitor-transaction-log-rollback"></a>Monitorowanie wycofywania dziennika transakcji
Jeśli zapytania są niepowodzeniem lub zbyt długo, aby kontynuować, należy sprawdzić, a następnie monitorować, jeśli masz wszystkich wycofywanie transakcji.
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
Aby uzyskać więcej informacji na temat widoków DMV, zobacz [widoków systemowych][System views].


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
