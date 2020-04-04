---
title: Zapytania dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: Odwołania do zasobów do nauki pisania zapytań dziennika w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 6b81aba553fc775821c80631aa83bbb3e8ac63b5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631794"
---
# <a name="azure-monitor-log-queries"></a>Zapytania dziennika usługi Azure Monitor

Dzienniki usługi Azure Monitor są oparte na Eksploratorze danych platformy Azure, a zapytania dziennika usługi Azure Monitor używają wersji tego samego języka zapytań Kusto. [Dokumentacja języka zapytań Kusto](/azure/kusto/query) zawiera wszystkie szczegóły dotyczące języka i powinna być podstawowym zasobem do pisania zapytań dziennika usługi Azure Monitor. Ta strona zawiera łącza do innych zasobów do nauki pisania zapytań i różnic z implementacją języka usługi Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Wprowadzenie

- [Wprowadzenie do usługi Azure Monitor Log Analytics](get-started-portal.md) to lekcja do pisania zapytań i pracy z wynikami w witrynie Azure portal.
- [Wprowadzenie do zapytań dziennika usługi Azure Monitor](get-started-queries.md) to lekcja do pisania zapytań przy użyciu danych dziennika usługi Azure Monitor.

## <a name="concepts"></a>Pojęcia

- [Analiza danych dziennika w usłudze Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) zawiera krótkie omówienie zapytań dziennika i opisuje, jak dane dziennika usługi Azure Monitor jest skonstruowany.
- [Wyświetlanie i analizowanie danych dziennika w usłudze Azure Monitor](../../azure-monitor/log-query/portals.md) wyjaśnia portale, w których można tworzyć i uruchamiać zapytania dziennika.

## <a name="reference"></a>Dokumentacja

- [Odwołanie do języka kwerendy](/azure/kusto/query) jest pełnym odwołaniem do języka zapytania Kusto.
- [Różnice językowe zapytań dziennika usługi Azure Monitor](data-explorer-difference.md) opisano różnice między wersjami języka zapytań Kusto.
- [Właściwości standardowe w rekordach dziennika usługi Azure Monitor](../../azure-monitor/platform/log-standard-properties.md) opisano właściwości, które są standardowe dla wszystkich danych dziennika usługi Azure Monitor.
- [Wykonywanie kwerend dziennika wielu zasobów w usłudze Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) opisuje sposób pisania kwerend dziennika, które używają danych z wielu obszarów roboczych usługi Log Analytics i aplikacji usługi Application Insights.

## <a name="examples"></a>Przykłady

- [Przykłady zapytań dziennika usługi Azure Monitor](examples.md) zawierają przykładowe kwerendy przy użyciu danych dziennika usługi Azure Monitor.

## <a name="lessons"></a>Lekcje

- [Praca z ciągami w kwerendach dziennika usługi Azure Monitor](string-operations.md) opisuje sposób pracy z danymi ciągu.
- [Praca z wartościami daty i godziny w kwerendach dziennika usługi Azure Monitor](datetime-operations.md) opisuje sposób pracy z danymi daty i godziny.
- [Agregacje w kwerendach dziennika usługi Azure Monitor](aggregations.md) i [zaawansowanych agregacjach w kwerendach dziennika usługi Azure Monitor](advanced-aggregations.md) opisują sposób agregowania i podsumowywania danych.
- [Sprzężenia w kwerendach dziennika usługi Azure Monitor](joins.md) opisano sposób dołączania do danych z wielu tabel.
- [Praca z JSON i struktur danych w usłudze Azure Monitor kwerend dziennika](json-data-structures.md) opisuje jak przeanalizować dane json.
- [Pisanie zaawansowanych zapytań dziennika w usłudze Azure Monitor](advanced-query-writing.md) opisuje strategie tworzenia złożonych zapytań i ponownego używania kodu.
- [Tworzenie wykresów i diagramów z zapytań dziennika usługi Azure Monitor](charts.md) opisuje sposób wizualizacji danych z kwerendy dziennika.

## <a name="cheatsheets"></a>Ściągawki

- [Sql do usługi Azure Monitor wpisuje zapytanie dziennika](sql-cheatsheet.md) pomaga użytkownikom, którzy są już zaznajomieni z SQL.
- [Splunk do usługi Azure Monitor wpisuje zapytanie dziennika](splunk-cheatsheet.md) pomaga użytkownikom, którzy są już zaznajomieni ze Splunk.

## <a name="next-steps"></a>Następne kroki

- Uzyskaj dostęp do pełnej [dokumentacji referencyjnej języka zapytań Kusto](/azure/kusto/query/).
