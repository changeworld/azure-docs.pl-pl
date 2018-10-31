---
title: Język zapytań usługi Azure Monitor Log Analytics | Dokumentacja firmy Microsoft
description: Odwołania do zasobów do nauki, jak pisać zapytania w usłudze Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: f8bef8c6ab5c0639f9a99eb2c0443c33d7b6d84e
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244066"
---
# <a name="log-analytics-query-language"></a>Zaloguj się języka zapytań usługi Analytics
Log Analytics udostępnia zbieranie dzienników i analizy dla usługi Azure Monitor. Ona bazuje na Eksploratora danych usługi Azure i korzysta z wersji tego samego języka zapytań. [Dokumentacja języka zapytań Eksploratora danych usługi Azure](/azure/kusto/query) zawiera wszystkie szczegółowe informacje dotyczące języka i powinien być podstawowy zasób do pisania zapytań usługi Log Analytics. Ta strona zawiera linki do innych zasobów do nauki, jak pisać zapytania, a także na różnice z implementacją usługi Log Analytics języka.

## <a name="getting-started"></a>Wprowadzenie

- [Rozpoczynanie pracy z usługą Log Analytics w witrynie Azure portal](get-started-analytics-portal.md) jest lekcji Pisanie zapytań i pracy z wynikami w witrynie Azure portal.
-  [Wprowadzenie do zapytań w usłudze Log Analytics](get-started-queries.md) jest lekcji do pisania zapytań przy użyciu danych usługi Log Analytics.

## <a name="concepts"></a>Pojęcia
- [Analizowanie danych usługi Log Analytics w usłudze Azure Monitor](../log-analytics-queries.md) zawiera krótkie omówienie dziennika zapytań i opisuje strukturę danych usługi Log Analytics.
- [Wyświetlanie i analizowanie danych w usłudze Log Analytics](../log-analytics-log-search-portals.md) wyjaśnia portali, gdzie tworzenie i uruchamianie zapytań usługi Log Analytics.

## <a name="reference"></a>Informacje ogólne

- [Dokumentacja języka zapytań](/azure/kusto/query) jest odwołanie do języka pełny język zapytań Eksploratora danych.
- [Zaloguj się różnic języka zapytań analizy](data-explorer-difference.md) w tym artykule opisano różnice między wersjami języka zapytań Eksploratora danych.
- [Właściwości standardowe w rekordach usługi Log Analytics](../log-analytics-standard-properties.md) opisuje właściwości, które są standardowe do wszystkich danych usługi Log Analytics.
- [Wykonaj wyszukiwanie w dzienniku między zasobami w usłudze Log Analytics](../log-analytics-cross-workspace-search.md) w tym artykule opisano sposób pisania zapytań, które używają danych z wielu obszarów roboczych usługi Log Analytics i aplikacji usługi Application Insights.


## <a name="examples"></a>Przykłady

- [Przykłady zapytań analizy dziennika](examples.md) zawiera przykładowe zapytania przy użyciu danych usługi Log Analytics.



## <a name="lessons"></a>Lekcje

- [Praca z ciągami zapytań usługi Log Analytics](string-operations.md) w tym artykule opisano sposób pracy z danymi w ciągu.
- [Praca z wartości daty / godziny w zapytań usługi Log Analytics](datetime-operations.md) w tym artykule opisano sposób pracy z danych daty i godziny. 
- [Agregacje w zapytań usługi Log Analytics](aggregations.md) i [zaawansowane agregacji w zapytań usługi Log Analytics](advanced-aggregations.md) opisują sposób agregują i podsumowują dane.
- [Sprzężenia w zapytań usługi Log Analytics](joins.md) w tym artykule opisano sposób łączenia danych z wielu tabel.
- [Praca z formatami JSON i danych struktury zapytań usługi Log Analytics](json-data-structures.md) opisano, jak analizować dane json.
- [Zapisywanie zaawansowanych zapytań w usłudze Log Analytics](advanced-query-writing.md) opisano strategie do tworzenia złożonych kwerend i ponowne użycie kodu.
- [Tworzenie wykresów i diagramów z zapytań usługi Log Analytics](charts.md) opisano, jak wizualizować dane z zapytania.

## <a name="cheatsheets"></a>Ściągawki

-  [SQL do ściągawka dotycząca języka zapytań usługi Log Analytics](sql-cheatsheet.md) pomaga użytkownikom, którzy są już zaznajomieni z programu SQL.
-  [Splunk do ściągawka dotycząca języka zapytań usługi Log Analytics](sql-cheatsheet.md) pomaga użytkownikom, którzy są już zaznajomieni z Splunk.
 
## <a name="next-steps"></a>Kolejne kroki

- Dostęp do pełnego [odwoływać się do dokumentacji języka zapytań Eksploratora danych](/azure/kusto/query/).