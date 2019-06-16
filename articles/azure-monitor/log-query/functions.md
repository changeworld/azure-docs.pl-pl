---
title: Funkcje w zapytaniach dzienników usługi Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób używania funkcji do wywołania zapytania z innego zapytania dzienników w usłudze Azure Monitor.
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
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 7eb5f090026b415e3c980116a9317594f4ca8115
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60728070"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Za pomocą funkcji w zapytaniach dzienników usługi Azure Monitor

> [!NOTE]
> Należy wykonać [Rozpoczynanie pracy z usługą portalu analiza](get-started-portal.md) i [wprowadzenie do zapytań](get-started-queries.md) przed wykonaniem tej lekcji.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


Zapytanie dziennika za pomocą innego zapytania można zapisać go w funkcji. To umożliwia uproszczenie kwerend złożonych, dzieląc je na części i pozwala na ponowne użycie typowych kodu z wieloma zapytaniami.

## <a name="create-a-function"></a>Tworzenie funkcji

Tworzenie funkcji przy użyciu usługi Log Analytics w witrynie Azure portal, klikając **Zapisz** i podają informacje przedstawione w poniższej tabeli.

| Ustawienie | Opis |
|:---|:---|
| Name (Nazwa)           | Nazwa wyświetlana dla zapytania w **Eksplorator zapytań**. |
| Zapisz jako        | Funkcja |
| Alias funkcji | Krótka nazwa do użycia funkcji w innych zapytań. Nie może zawierać spacji i muszą być unikatowe. |
| Category       | Kategorię, aby zorganizować zapisanych zapytań i funkcji w **Eksplorator zapytań**. |

> [!NOTE]
> Funkcja w usłudze Azure Monitor nie może zawierać innej funkcji.

> [!NOTE]
> Zapisywanie funkcji jest możliwe w zapytaniach dzienników usługi Azure Monitor, ale obecnie nie zapytania usługi Application Insights.



## <a name="use-a-function"></a>Użyj funkcji
Aby użyć funkcji, łącznie z jego aliasu w innym zapytaniu. Można je jak każdą inną tabelę.

## <a name="example"></a>Przykład
Następujące przykładowe zapytanie zwraca wszystkie brakujące aktualizacje zabezpieczeń, zgłaszane w ciągu ostatniego dnia. Zapisz to zapytanie jako funkcję z aliasu _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Utwórz innego zapytania i odwołania _security_updates_last_day_ funkcję, aby wyszukać związanych z SQL niezbędnych aktualizacji zabezpieczeń.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne lekcje dotyczące pisania usługi Azure Monitor dziennika zapytań:

- [Operacje na ciągach](string-operations.md)
- [Operacje daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Zaawansowane agregacji](advanced-aggregations.md)
- [JSON i struktur danych](json-data-structures.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)
