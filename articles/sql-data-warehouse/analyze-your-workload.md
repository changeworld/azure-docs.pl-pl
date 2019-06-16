---
title: Analizowanie obciążenia w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Techniki do analizowania priorytetyzacji zapytania dla obciążenia w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: f470670ae3d526f3b66badf219a01a471c24db0d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242239"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Analizowanie obciążenia w usłudze Azure SQL Data Warehouse

Techniki do analizowania obciążenia w usłudze Azure SQL Data Warehouse.

## <a name="resource-classes"></a>Klasy zasobów

Usługa SQL Data Warehouse udostępnia klasy zasobów do przypisanych zasobów do zapytań.  Aby uzyskać więcej informacji na temat klas zasobów, zobacz [zarządzanie zasobami w klasach i obciążeniem](resource-classes-for-workload-management.md).  Zapytań będzie czekać, jeśli klasy zasobów przypisanej zapytanie wymaga więcej zasobów niż są obecnie dostępne.

## <a name="queued-query-detection-and-other-dmvs"></a>Wykrywanie umieszczonych w kolejce zapytań i innych widoków DMV

Możesz użyć `sys.dm_pdw_exec_requests` DMV, aby identyfikować zapytania, które oczekują w kolejce współbieżności. Zapytań oczekujących dla miejsca współbieżności ma stan inny niż **zawieszone**.

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

Role związane z zarządzaniem obciążenia mogą być wyświetlane ze `sys.database_principals`.

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

Usługa SQL Data Warehouse ma czekać typów:

* **LocalQueriesConcurrencyResourceType**: Zapytania, które znajdują się poza szablonem miejsca współbieżności. Zapytania DMV i systemu przez funkcje, takie jak `SELECT @@VERSION` są przykłady kwerend lokalnych.
* **UserConcurrencyResourceType**: Zapytania, które znajdują się w strukturze gniazdo współbieżności. Zapytania dotyczące tabel przez użytkownika końcowego reprezentują przykłady używających tego typu zasobu.
* **DmsConcurrencyResourceType**: W tym czasie czeka wynikające z operacje przenoszenia danych.
* **BackupConcurrencyResourceType**: Ta oczekiwania wskazuje, że bazy danych jest tworzona kopia zapasowa. Maksymalna wartość dla tego typu zasobu to 1. Jeśli zażądano wielu kopii zapasowych, w tym samym czasie inne kolejki. Ogólnie rzecz biorąc zaleca się minimalny czas między migawkami kolejne 10 minut. 

`sys.dm_pdw_waits` DMV może służyć do wyświetlania zasobów, które czeka na żądanie.

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

`sys.dm_pdw_resource_waits` DMV pokazuje informacje oczekiwania dla danego zapytania. Zasób poczekaj miar czasu czasu oczekiwania na zasoby, aby podać. Czas oczekiwania na sygnał jest czasu potrzebnego dla podstawowych serwerów SQL do zaplanowania zapytania na procesorze CPU.

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

Można również użyć `sys.dm_pdw_resource_waits` DMV obliczania liczby gniazd współbieżności zostały przyznane.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` DMV może służyć do analizy trendów historycznych czeka.

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

Aby uzyskać więcej informacji o zarządzaniu bazy danych użytkowników i zabezpieczeń, zobacz [Zabezpieczanie bazy danych w usłudze SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md). Aby uzyskać więcej informacji na temat sposobu większych klas zasobów może zwiększyć jakość indeksu klastrowanego magazynu kolumn, zobacz [ponowne tworzenie indeksów w celu zwiększenia jakości segmentów](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
