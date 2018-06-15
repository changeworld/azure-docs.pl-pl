---
title: Analiza obciążenia - Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Techniki analizowanie priorytetyzacji zapytania dla obciążenia w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6b0d39b81b72615a9522e95558a59007b10bf109
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795361"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Analiza obciążenia w usłudze Azure SQL Data Warehouse
Techniki analizowanie priorytetyzacji zapytania dla obciążenia w usłudze Azure SQL Data Warehouse.

## <a name="workload-groups"></a>Grupy obciążenia 
Usługa SQL Data Warehouse implementuje klasy zasobów za pomocą grupy obciążenia. Składa się łącznie osiem grup obciążenia, które kontrolują zachowanie klasy zasobu w różnych rozmiarach DWU. Dla dowolnego DWU SQL Data Warehouse używa tylko czterech grup osiem obciążenia. Takie podejście ma sens, ponieważ każda grupa obciążenia jest przypisany do czterech grup zasobów: smallrc, mediumrc, largerc, lub xlargerc. Opis grupy obciążenia znaczenie jest, że niektóre z tych grup obciążenia są ustawione na wyższy *znaczenie*. Znaczenie jest używany dla Procesora planowania. Uruchom o wysokiej ważności zapytania uzyskać trzy razy więcej cykli Procesora niż kwerend o średnim znaczeniu. W związku z tym mapowania miejsca współbieżności również określić priorytet procesora CPU. Jeśli zapytanie wykorzystuje co najmniej 16 miejsc, jest on uruchamiany jako wysokiej ważności.

W poniższej tabeli przedstawiono znaczenie mapowania dla każdej grupy obciążenia.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapowania grupy obciążenia do gniazda współbieżności i ważność

| Grupy obciążenia | Mapowanie miejsca współbieżności | MB / dystrybucji (elastyczność) | MB / dystrybucji (obliczeniowe) | Mapowanie znaczenie |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | Medium             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | Medium             |
| SloDWGroupC02   | 4                        |    400                         | 1000                        | Medium             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | Medium             |
| SloDWGroupC04   | 16                       |  1,600                         | 4000                        | Wysoka               |
| SloDWGroupC05   | 32                       |  3,200                         | 8000                        | Wysoka               |
| SloDWGroupC06   | 64                       |  6,400                         | 16,000                      | Wysoka               |
| SloDWGroupC07   | 128                      | 12,800                         | 32,000                      | Wysoka               |
| SloDWGroupC08   | 256                      | 25,600                         | 64,000                      | Wysoka               |

<!-- where are the allocation and consumption of concurrency slots charts? -->
**Alokacja i użycie gniazd współbieżności** wykres pokazuje DW500 używa 1, 4, 8 i współbieżność 16 miejsc smallrc, mediumrc largerc i xlargerc, odpowiednio. Aby znaleźć znaczenie dla każdej klasy zasobu, można wyszukiwać tych wartości na wykresie.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mapowanie DW500 klasy zasobu znaczenia
| Klasa zasobów | Grupy obciążenia | Używać miejsc współbieżności | MB / dystrybucji | Ważność |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | Medium     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | Medium     |
| largerc        | SloDWGroupC03  | 8                      | 800               | Medium     |
| xlargerc       | SloDWGroupC04  | 16                     | 1,600             | Wysoka       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | Medium     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | Medium     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | Medium     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | Medium     |
| staticrc50     | SloDWGroupC03  | 16                     | 1,600             | Wysoka       |
| staticrc60     | SloDWGroupC03  | 16                     | 1,600             | Wysoka       |
| staticrc70     | SloDWGroupC03  | 16                     | 1,600             | Wysoka       |
| staticrc80     | SloDWGroupC03  | 16                     | 1,600             | Wysoka       |

## <a name="view-workload-groups"></a>Widok grupy obciążenia
Następujące zapytanie Wyświetla szczegóły alokacji zasobów pamięci z punktu widzenia zarządcy zasobów. Jest to przydatne do analizowania active i historyczne użycia grup obciążenia podczas rozwiązywania problemów.

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>Zapytania w kolejce wykrywania i innych widoków DMV
Można użyć `sys.dm_pdw_exec_requests` DMV do identyfikacji zapytania, które czekają w kolejce współbieżności. Oczekiwanie na gnieździe współbieżności zapytania będą miały stan **zawieszone**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Role związane z zarządzaniem obciążenia mogą być wyświetlane z `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Następujące zapytanie wyświetla każdy użytkownik jest przypisany do roli.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

Magazyn danych SQL zawiera następujące typy oczekiwania:

* **LocalQueriesConcurrencyResourceType**: zapytania, które znajdują się poza framework miejsca współbieżności. Zapytania DMV i systemu, takich jak funkcje `SELECT @@VERSION` przedstawiono przykłady lokalnego zapytań.
* **UserConcurrencyResourceType**: zapytania, które znajdują się w ramach miejsca współbieżności. Zapytania dotyczące tabel użytkownika końcowego przedstawiają przykłady używających tego typu zasobu.
* **DmsConcurrencyResourceType**: czeka, wynikające z operacji przenoszenia danych.
* **BackupConcurrencyResourceType**: ten oczekiwania wskazuje, że bazy danych jest tworzona kopia zapasowa. Maksymalna wartość dla tego typu zasobu jest 1. Jeśli zażądano wiele kopii zapasowych, w tym samym czasie, innych kolejki.

`sys.dm_pdw_waits` DMV może służyć do wyświetlania zasobów, które oczekuje żądanie.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

`sys.dm_pdw_resource_waits` DMV pokazuje tylko czeka zasobów, które są używane przez określonego zapytania. Czas oczekiwania zasobu tylko mierzy czas oczekiwania na zasoby wymagane do zrealizowania, a nie czas oczekiwania sygnał, który określa czas potrzebny dla podstawowych serwerów SQL, można zaplanować zapytania na Procesor.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```
Można również użyć `sys.dm_pdw_resource_waits` DMV obliczyć przyznano ilu miejsc współbieżności.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots 
FROM    sys.[dm_pdw_resource_waits] 
WHERE   [state]           = 'Granted' 
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` DMV może służyć do analizy trendów historycznych oczekiwania.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o zarządzaniu bazy danych użytkowników i zabezpieczeń, zobacz [Zabezpieczanie bazy danych w usłudze SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md). Aby uzyskać więcej informacji na temat sposobu większe grupy zasobów może zwiększyć jakość indeksu klastrowanego magazynu kolumn, zobacz [ponowne tworzenie indeksów do poprawy jakości segmentu](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).


