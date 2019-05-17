---
title: Usługa Azure Monitor dziennika zapytań | Dokumentacja firmy Microsoft
description: Odwołania do zasobów do nauki, jak pisać zapytania dzienników w usłudze Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: 6fae245e4d72f7f6f7809d85da17023deb1518e5
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560847"
---
# <a name="azure-monitor-log-queries"></a>Usługa Azure Monitor dziennika zapytań
Dzienniki monitora platformy Azure są oparte na Eksploratora danych usługi Azure i usługi Azure Monitor dziennika zapytań Użyj wersji tego samego języka zapytania Kusto. [Dokumentacji języka zapytania Kusto](/azure/kusto/query) zawiera wszystkie szczegółowe informacje dotyczące języka i powinien być podstawowy zasoby umożliwiające korzystanie z Pisanie zapytań dzienników w usłudze Azure Monitor. Ta strona zawiera linki do innych zasobów do nauki, jak pisać zapytania i niedostępne w przypadku wdrożenia usługi Azure Monitor języka.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Wprowadzenie

- [Rozpoczynanie pracy z usługą Azure Monitor Log Analytics](get-started-portal.md) jest lekcji Pisanie zapytań i pracy z wynikami w witrynie Azure portal.
- [Rozpoczynanie pracy z usługą Azure Monitor dziennika zapytań](get-started-queries.md) jest lekcji do pisania zapytań przy użyciu usługi Azure Monitor dane dziennika.

## <a name="concepts"></a>Pojęcia
- [Analizuj dane dzienników w usłudze Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) zawiera krótkie omówienie dziennika zapytania i opisuje strukturę danych dzienników usługi Azure Monitor.
- [Wyświetlanie i analizowanie danych dzienników w usłudze Azure Monitor](../../azure-monitor/log-query/portals.md) wyjaśnia portali, gdzie tworzenie i uruchamianie zapytań log.

## <a name="reference"></a>Odwołanie

- [Dokumentacja języka zapytań](/azure/kusto/query) jest kompletny Skorowidz języka zapytania Kusto.
- [Różnice języka zapytań usługi Azure Monitor dziennika](data-explorer-difference.md) w tym artykule opisano różnice między wersjami języka zapytania Kusto.
- [Standardowe właściwości w usłudze Azure Monitor rekordów dziennika](../../azure-monitor/platform/log-standard-properties.md) opisuje właściwości, które są standardowe do wszystkich danych dziennika usługi Azure Monitor.
- [Wykonywać zapytania obejmujące wiele zasobów dzienników w usłudze Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) opisano, jak pisać zapytania dziennika, zawierających dane z wielu obszarów roboczych usługi Log Analytics i aplikacje usługi Application Insights.


## <a name="examples"></a>Przykłady

- [Przykłady zapytań dzienników w usłudze Azure Monitor](examples.md) zawiera przykładowe zapytania przy użyciu usługi Azure Monitor dane dziennika.



## <a name="lessons"></a>Lekcje

- [Praca z ciągami zapytań dzienników usługi Azure Monitor](string-operations.md) w tym artykule opisano sposób pracy z danymi w ciągu.
- [Praca z wartości daty / godziny w zapytaniach dzienników usługi Azure Monitor](datetime-operations.md) w tym artykule opisano sposób pracy z danych daty i godziny. 
- [Agregacje w usłudze Azure Monitor rejestrowania zapytań](aggregations.md) i [zaawansowane agregacji w zapytaniach dzienników usługi Azure Monitor](advanced-aggregations.md) opisują sposób agregują i podsumowują dane.
- [Sprzężenia w usłudze Azure Monitor dziennika zapytań](joins.md) w tym artykule opisano sposób łączenia danych z wielu tabel.
- [Praca z formatami JSON i danych struktury w zapytaniach dzienników usługi Azure Monitor](json-data-structures.md) opisano, jak analizować dane json.
- [Zapisywanie zaawansowanej rejestrowania zapytań w usłudze Azure Monitor](advanced-query-writing.md) opisano strategie do tworzenia złożonych kwerend i ponowne użycie kodu.
- [Tworzenie wykresów i diagramów z usługi Azure Monitor dziennika zapytań](charts.md) opisano, jak wizualizować dane z zapytania dziennika.

## <a name="cheatsheets"></a>Ściągawki

-  [SQL do wykonywania zapytań w usłudze Azure Monitor dziennika](sql-cheatsheet.md) pomaga użytkownikom, którzy są już zaznajomieni z programu SQL.
-  [Splunk do wykonywania zapytań w usłudze Azure Monitor dziennika](splunk-cheatsheet.md) pomaga użytkownikom, którzy są już zaznajomieni z Splunk.
 
## <a name="next-steps"></a>Kolejne kroki

- Dostęp do pełnego [dokumentacji języka zapytania Kusto](/azure/kusto/query/).
