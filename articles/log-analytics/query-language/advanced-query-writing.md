---
title: Zaawansowane zapytania w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera samouczek dotyczący korzystania z portalu usługi analiza pisać zapytania w usłudze Log Analytics.
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
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: f7594b7d1eb7d41508be435cdd0a6203433727c1
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603060"
---
# <a name="writing-advanced-queries-in-log-analytics"></a>Zapisywanie zaawansowanych zapytań w usłudze Log Analytics

> [!NOTE]
> Należy wykonać [Rozpoczynanie pracy z usługą portalu analiza](get-started-analytics-portal.md) i [wprowadzenie do zapytań](get-started-queries.md) przed wykonaniem tej lekcji.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Ponowne użycie kodu przy użyciu let
Użyj `let` przypisać wyniki do zmiennej i odwoływać się do niego później w zapytaniu:

```KQL
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Można także przypisać stałe wartości do zmiennych. Ułatwia to metoda do ustawiania parametrów dla pól, które należy zmienić za każdym razem, aby wykonać zapytanie. Modyfikowanie tych parametrów, zgodnie z potrzebami. Na przykład do obliczania wolnego miejsca na dysku i wolnej pamięci (w percentylach) w danym przedziale czasowym:

```KQL
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Dzięki temu można łatwo zmienić początek czas zakończenia przy następnym uruchomieniu zapytania.

### <a name="local-functions-and-parameters"></a>Funkcje lokalne i parametry
Użyj `let` instrukcje, aby tworzyć funkcje, które mogą być używane w jednym zapytaniu. Na przykład zdefiniować funkcję, która przyjmuje pola daty/godziny (w formacie UTC) i konwertuje je do standardowego formatu Stanów Zjednoczonych. 

```KQL
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="functions"></a>Funkcje
Można zapisać zapytania alias funkcji, dzięki czemu mogą być przywoływane przez inne zapytania. Na przykład następujące standardowe zapytanie zwraca wszystkie brakujące aktualizacje zabezpieczeń, zgłaszane w ciągu ostatniego dnia:

```KQL
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Można zapisać to zapytanie jako funkcja i nadaj aliasem, takie jak _security_updates_last_day_. Następnie można go do innego zapytania wyszukiwania związanych z SQL niezbędnych aktualizacji zabezpieczeń:

```KQL
security_updates_last_day | where Title contains "SQL"
```

Aby zapisać zapytanie jako funkcję, wybierz pozycję **Zapisz** przycisku w portalu i zmień **Zapisz jako** do _funkcja_. Alias funkcji może zawierać litery, cyfry i znaki podkreślenia, ale musi rozpoczynać się od litery lub znaku podkreślenia.

> [!NOTE]
> Zapisywanie funkcji jest możliwe w zapytań usługi Log Analytics, ale obecnie nie zapytania usługi Application Insights.


## <a name="print"></a>Drukowanie
`print` zwraca tabelę z jedną kolumnę i pojedynczego wiersza, jako wynik obliczeń. Jest to często używane w sytuacjach wymagających calcuation proste. Na przykład, aby znaleźć bieżącą godzinę w PST, a następnie dodaj kolumnę z EST:

```KQL
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Elementu DataTable
`datatable` Umożliwia zdefiniowanie zestawu danych. Schemat i zbiór wartości, a następnie przekazać tabeli na inne elementy zapytania. Na przykład aby utworzyć tabelę użycie pamięci RAM i Oblicz ich średnia wartość na godzinę:

```KQL
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Konstrukcje DataTable również są bardzo przydatne podczas tworzenia tabeli odnośników. Na przykład, aby zamapować tabeli danych, takich jak identyfikatory zdarzeń z _SecurityEvent_ tabeli, aby typy zdarzeń wymienionych w innym miejscu, tworzenie tabeli wyszukiwania za pomocą typów zdarzeń przy użyciu `datatable` i Dołącz do tego elementu datatable przy użyciu  _SecurityEvent_ danych:

```KQL
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
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