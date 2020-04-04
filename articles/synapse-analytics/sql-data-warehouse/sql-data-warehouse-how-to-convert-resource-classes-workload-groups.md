---
title: Konwertowanie klasy zasobów na grupę obciążenia
description: Dowiedz się, jak utworzyć grupę obciążenia podobną do klasy zasobów w usłudze Azure SQL Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a249dfc4f04fbd7b6b73a0e9f37d53106bf82efd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633341"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Konwertuj klasy zasobów na grupy obciążeń

Grupy obciążenia zapewniają mechanizm izolowania i przechowywania zasobów systemowych.  Ponadto grupy obciążenia umożliwiają ustawianie reguł wykonywania dla uruchomionych w nich żądań.  Reguła wykonywania limitu czasu kwerendy umożliwia anulowanie kwerend ucieczki bez interwencji użytkownika.  W tym artykule wyjaśniono, jak wziąć istniejącą klasę zasobów i utworzyć grupę obciążenia o podobnej konfiguracji.  Ponadto zostanie dodana opcjonalna reguła limitu czasu kwerendy.

## <a name="understanding-the-existing-resource-class-configuration"></a>Opis istniejącej konfiguracji klasy zasobów

Grupy obciążenia wymagają `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parametru o nazwie, który określa procent całkowitych zasobów systemowych przydzielonych na żądanie.  Alokacja zasobów odbywa się dla [klas zasobów](resource-classes-for-workload-management.md#what-are-resource-classes) przez przydzielanie gniazd współbieżności.  Aby określić wartość, `REQUEST_MIN_RESOURCE_GRANT_PERCENT`dla której ma być określona <link tbd> wartość, należy użyć pliku sys.dm_workload_management_workload_groups_stats DMV.  Na przykład poniższa kwerenda zwraca wartość, która `REQUEST_MIN_RESOURCE_GRANT_PERCENT` może służyć parametrowi do utworzenia grupy obciążenia podobnej do staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Grupy obciążenia działają na podstawie procentu całkowitych zasobów systemowych.  

Ponieważ grupy obciążenia działają na podstawie procentu ogólnych zasobów systemowych, podczas skalowania w górę i w dół, procent zasobów przydzielonych do statycznych klas zasobów w stosunku do ogólnych zasobów systemowych zmienia się.  Na przykład staticrc40 w DW1000c przydziela 9,6% całkowitych zasobów systemowych.  Na DW2000c przeznacza się 19,2 proc.  Ten model jest podobny, jeśli chcesz skalować w górę dla współbieżności w porównaniu do przydzielania większej ilości zasobów na żądanie.

## <a name="create-workload-group"></a>Tworzenie grupy obciążeń

Za pomocą `REQUEST_MIN_RESOURCE_GRANT_PERCENT`znanej , można użyć <link> składni TWORZENIE GRUPY OBCIĄŻENIA, aby utworzyć grupę obciążenia.  Opcjonalnie można `MIN_PERCENTAGE_RESOURCE` określić, że jest większa niż zero, aby wyizolować zasoby dla grupy obciążenia.  Ponadto można opcjonalnie `CAP_PERCENTAGE_RESOURCE` określić mniej niż 100, aby ograniczyć ilość zasobów, które grupa obciążenia może korzystać.  

Poniższy przykład `MIN_PERCENTAGE_RESOURCE` ustawia przeznaczyć 9,6% zasobów systemowych `wgDataLoads` i gwarantuje, że jedno zapytanie będzie w stanie uruchomić cały czas.  Ponadto `CAP_PERCENTAGE_RESOURCE` jest ustawiona na 38,4% i ogranicza tę grupę obciążenia do czterech równoczesnych żądań.  Ustawiając `QUERY_EXECUTION_TIMEOUT_SEC` parametr na 3600, każda kwerenda, która działa dłużej niż 1 godzinę, zostanie automatycznie anulowana.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Tworzenie klasyfikatora

Wcześniej mapowanie zapytań do klas zasobów zostało wykonane za pomocą [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Aby osiągnąć te same funkcje i mapować żądania do grup obciążeń, należy użyć składni [CREATE WORKLOAD CLASSIFIER.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)  Korzystanie z sp_addrolemember umożliwia tylko mapowanie zasobów na żądanie na podstawie loginu.  Klasyfikator zapewnia dodatkowe opcje oprócz logowania, takie jak:
    - label
    - sesja
    - czas Poniższy przykład przypisuje zapytania `AdfLogin` z logowania, które mają `factloads` również option `wgDataLoads` [label](sql-data-warehouse-develop-label.md) ustawiony na grupę obciążenia utworzone powyżej.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Testowanie za pomocą przykładowej kwerendy

Poniżej znajduje się przykładowe zapytanie i kwerenda DMV, aby upewnić się, że grupa obciążenia i klasyfikator są poprawnie skonfigurowane.

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

- [Izolacja obciążenia](sql-data-warehouse-workload-isolation.md)
- [Jak utworzyć łącze do grupy obciążenia](quickstart-configure-workload-isolation-tsql.md)
