---
title: Zaawansowane zapytania w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera samouczek dotyczący korzystania z portalu usługi analiza pisać zapytania w usłudze Azure Monitor.
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
ms.openlocfilehash: 65713ed9c2d0635e776a7a7e5f205b6d55438ed4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60589589"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Zapisywanie zaawansowanych zapytań w usłudze Azure Monitor

> [!NOTE]
> Należy wykonać [Rozpoczynanie pracy z usługą Azure Monitor Log Analytics](get-started-portal.md) i [wprowadzenie do zapytań](get-started-queries.md) przed wykonaniem tej lekcji.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Ponowne użycie kodu przy użyciu let
Użyj `let` przypisać wyniki do zmiennej i odwoływać się do niego później w zapytaniu:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Można także przypisać stałe wartości do zmiennych. Ułatwia to metoda do ustawiania parametrów dla pól, które należy zmienić za każdym razem, aby wykonać zapytanie. Modyfikowanie tych parametrów, zgodnie z potrzebami. Na przykład do obliczania wolnego miejsca na dysku i wolnej pamięci (w percentylach) w danym przedziale czasowym:

```Kusto
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

```Kusto
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

## <a name="print"></a>Drukuj
`print` zwraca tabelę z jedną kolumnę i pojedynczego wiersza, jako wynik obliczeń. Jest to często używane w sytuacjach wymagających prostego obliczenia. Na przykład, aby znaleźć bieżącą godzinę w PST, a następnie dodaj kolumnę z EST:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Elementu DataTable
`datatable` Umożliwia zdefiniowanie zestawu danych. Schemat i zbiór wartości, a następnie przekazać tabeli na inne elementy zapytania. Na przykład aby utworzyć tabelę użycie pamięci RAM i Oblicz ich średnia wartość na godzinę:

```Kusto
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

```Kusto
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
Zobacz inne lekcje dotyczące korzystania z [język zapytania Kusto](/azure/kusto/query/) z usługą Azure Monitor możesz rejestrować dane:

- [Operacje na ciągach](string-operations.md)
- [Operacje daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Zaawansowane agregacji](advanced-aggregations.md)
- [JSON i struktur danych](json-data-structures.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)
