---
title: Funkcje usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób używania funkcji do wywołania zapytania z innego zapytania w usłudze Log Analytics.
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
ms.openlocfilehash: 8f2855ed56d298ec4c6abee02dd59ce9471f0d2e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882853"
---
# <a name="using-functions-in-azure-monitor-log-analytics"></a>Za pomocą funkcji w usłudze Azure Monitor Log Analytics

> [!NOTE]
> Należy wykonać [Rozpoczynanie pracy z usługą portalu analiza](get-started-portal.md) i [wprowadzenie do zapytań](get-started-queries.md) przed wykonaniem tej lekcji.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


Zapytanie usługi Log Analytics za pomocą innego zapytania można zapisać go w funkcji. To umożliwia uproszczenie kwerend złożonych, dzieląc je na części i pozwala na ponowne użycie typowych kodu z wieloma zapytaniami.

## <a name="create-a-function"></a>Tworzenie funkcji

Tworzenie funkcji w witrynie Azure portal, klikając **Zapisz** i podają informacje przedstawione w poniższej tabeli.

| Ustawienie | Opis |
|:---|:---|
| Name (Nazwa)           | Nazwa wyświetlana dla zapytania w **Eksplorator zapytań**. |
| Zapisz jako        | Funkcja |
| Alias funkcji | Krótka nazwa do użycia funkcji w innych zapytań. Nie może zawierać spacji i muszą być unikatowe. |
| Kategoria       | Kategorię, aby zorganizować zapisanych zapytań i funkcji w **Eksplorator zapytań**. |

> [!NOTE]
> Funkcja w usłudze Log Analytics nie może zawierać innej funkcji.

> [!NOTE]
> Zapisywanie funkcji jest możliwe w zapytań usługi Log Analytics, ale obecnie nie zapytania usługi Application Insights.



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
Zobacz inne lekcje dla przy użyciu języka zapytań usługi Log Analytics:

- [Operacje na ciągach](string-operations.md)
- [Operacje daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Zaawansowane agregacji](advanced-aggregations.md)
- [JSON i struktur danych](json-data-structures.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)
