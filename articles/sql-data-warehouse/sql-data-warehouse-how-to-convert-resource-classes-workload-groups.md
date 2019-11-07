---
title: Konwertowanie klasy zasobów na grupę obciążeń
description: Dowiedz się, jak utworzyć grupę obciążeń podobną do klasy zasobów w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5708dccce5f7cdcc33fe6bfca980126cd8b5ee7f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685705"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Konwertowanie klas zasobów na grupy obciążeń
Grupy obciążeń zapewniają mechanizm izolowania i zawierania zasobów systemowych.  Ponadto grupy obciążeń umożliwiają ustawianie reguł wykonywania dla żądań w nich uruchomionych.  Reguła wykonywania limitu czasu zapytania umożliwia anulowanie zapytań dotyczących przemijających bez interwencji użytkownika.  W tym artykule wyjaśniono, jak zastosować istniejącą klasę zasobów i utworzyć grupę obciążeń z podobną konfiguracją.  Dodatkowo dodawana jest opcjonalna reguła limitu czasu zapytania.

## <a name="understanding-the-existing-resource-class-configuration"></a>Zrozumienie istniejącej konfiguracji klasy zasobów
Grupy obciążeń wymagają parametru o nazwie `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, który określa procent łącznych zasobów systemowych przyznanych na żądanie.  Alokacja zasobów jest wykonywana dla [klas zasobów](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes) przez alokowanie miejsc współbieżności.  Aby określić wartość do określenia dla `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, użyj widoku sys. DM _workload_management_workload_groups_stats <link tbd> DMV.  Na przykład poniższe zapytanie zapytania zwraca wartość, której można użyć dla parametru `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, aby utworzyć grupę obciążeń podobną do staticrc40.   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Grupy obciążeń działają na podstawie wartości procentowej ogólnych zasobów systemowych.  

Ponieważ grupy obciążeń działają na podstawie wartości procentowej ogólnych zasobów systemowych, podczas skalowania w górę i w dół, procent zasobów przypisywanych do klas zasobów statycznych względem ogólnych zmian zasobów systemu.  Na przykład staticrc40 w DW1000c przydziela 9,6% ogólnych zasobów systemowych.  O godzinie DW2000c przydzielono 19,2%.  Ten model jest podobny, jeśli chcesz skalować w górę w celu zapewnienia współbieżności i przydzielania większej liczby zasobów na żądanie.   

## <a name="create-workload-group"></a>Utwórz grupę obciążeń
Ze znanymi `REQUEST_MIN_RESOURCE_GRANT_PERCENT`można użyć składni Utwórz grupę obciążeń <link>, aby utworzyć grupę obciążeń.  Opcjonalnie możesz określić `MIN_PERCENTAGE_RESOURCE`, która jest większa niż zero, aby izolować zasoby dla grupy obciążeń.  Ponadto można opcjonalnie określić `CAP_PERCENTAGE_RESOURCE` mniejszą niż 100, aby ograniczyć ilość zasobów, które może zużywać Grupa obciążeń.  

Poniższy przykład ustawia `MIN_PERCENTAGE_RESOURCE`, aby przeznaczyć 9,6% zasobów systemowych do `wgDataLoads` i zagwarantować, że jedno zapytanie będzie mogło działać przez cały czas.  Ponadto `CAP_PERCENTAGE_RESOURCE` jest ustawiony na 38,4% i ogranicza tę grupę obciążeń do czterech współbieżnych żądań.  Ustawienie parametru `QUERY_EXECUTION_TIMEOUT_SEC` na 3600, wszystkie zapytania, które są uruchamiane dłużej niż 1 godzina, zostaną automatycznie anulowane.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Tworzenie klasyfikatora
Poprzednio mapowanie zapytań do klas zasobów zostało wykonane z [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Aby osiągnąć te same funkcje i mapować żądania do grup obciążeń, użyj składni [KLASYFIKATORA obciążeń](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) .  Użycie sp_addrolemember umożliwia mapowanie zasobów na żądanie na podstawie nazwy logowania.  Klasyfikator zapewnia dodatkowe opcje oprócz logowania, takie jak:
    - label
    - session
    - czas w poniższym przykładzie przypisuje zapytania z `AdfLogin` nazwy logowania, która ma także ustawioną [etykietę opcji](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label) na `factloads` grupie obciążeń `wgDataLoads` utworzonej powyżej.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>Testowanie za pomocą przykładowego zapytania
Poniżej znajduje się przykładowe zapytanie i zapytanie DMV, aby upewnić się, że grupa obciążeń i klasyfikator są prawidłowo skonfigurowane.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 * 
  FROM nation 
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command 
  FROM sys.dm_pdw_exec_requests 
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Następne kroki

Izolacja obciążenia — Połącz się z tematem jak utworzyć grupę obciążeń — łączenie do opracowania później