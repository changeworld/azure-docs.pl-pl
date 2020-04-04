---
title: Analizowanie obciążenia
description: Techniki analizowania priorytetów zapytań dla obciążenia w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 6a38fe65b4aedf4f594531f5e9cd8cf9b5dfaac7
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631237"
---
# <a name="analyze-your-workload-in-azure-synapse-analytics"></a>Analizowanie obciążenia w usłudze Azure Synapse Analytics

Techniki analizowania obciążenia usługi Synapse SQL w usłudze Azure Synapse Analytics.

## <a name="resource-classes"></a>Klasy zasobów

Synapse SQL udostępnia klasy zasobów do przypisywania zasobów systemowych do kwerend.  Aby uzyskać więcej informacji na temat klas zasobów, zobacz [Klasy zasobów & zarządzanie obciążeniem .](resource-classes-for-workload-management.md)  Kwerendy będą czekać, jeśli klasa zasobów przypisana do kwerendy potrzebuje więcej zasobów niż są obecnie dostępne.

## <a name="queued-query-detection-and-other-dmvs"></a>Wykrywanie zapytań w kolejce i inne dmvs

DmV służy `sys.dm_pdw_exec_requests` do identyfikowania kwerend, które czekają w kolejce współbieżności. Zapytania oczekujące na gniazdo współbieżności mają stan **zawieszone**.

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

Role zarządzania obciążeniem `sys.database_principals`można wyświetlać za pomocą pliku .

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Poniższa kwerenda pokazuje, do której roli jest przypisany każdy użytkownik.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

Synapse SQL ma następujące typy oczekiwania:

* **LocalQueriesConcurrencyResourceType:** Zapytania, które znajdują się poza ramą gniazda współbieżności. Zapytania DMV i funkcje `SELECT @@VERSION` systemowe, takie jak przykłady zapytań lokalnych.
* **UserConcurrencyResourceType:** Zapytania, które znajdują się wewnątrz struktury gniazda współbieżności. Kwerendy względem tabel użytkowników końcowych reprezentują przykłady, które mogłyby używać tego typu zasobu.
* **DmsConcurrencyResourceType**: Oczekiwania wynikające z operacji przenoszenia danych.
* **BackupConcurrencyResourceType:** Ten czas oczekiwania wskazuje, że kopia zapasowa bazy danych jest kopią zapasową. Maksymalna wartość dla tego typu zasobu wynosi 1. Jeśli w tym samym czasie zażądano wielu kopii zapasowych, pozostałe kolejki. Ogólnie zaleca się minimalny czas między kolejnymi migawkami 10 minut.

DMV `sys.dm_pdw_waits` można użyć, aby zobaczyć, które zasoby żądanie oczekuje na.

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

DMV `sys.dm_pdw_resource_waits` pokazuje informacje oczekiwania dla danej kwerendy. Czas oczekiwania zasobów mierzy czas oczekiwania na zasoby, które mają być dostarczone. Czas oczekiwania na sygnał to czas potrzebny na zaplanowanie zapytania na procesor CPU przez podstawowe serwery SQL.

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

Można również użyć `sys.dm_pdw_resource_waits` DMV obliczyć, ile gniazd współbieżności zostały przyznane.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` DMV może być używany do historycznej analizy trendów czeka.

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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania użytkownikami bazy danych i zabezpieczeniami, zobacz [Zabezpieczanie bazy danych w synapse SQL](sql-data-warehouse-overview-manage-security.md). Aby uzyskać więcej informacji o tym, jak większe klasy zasobów mogą poprawić jakość indeksu magazynu kolumn klastrowanych, zobacz [Odbudowanie indeksów w celu poprawy jakości segmentu](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
