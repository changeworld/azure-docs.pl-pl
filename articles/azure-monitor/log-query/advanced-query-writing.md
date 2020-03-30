---
title: Zaawansowane zapytania w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Ten artykuł zawiera samouczek dotyczący używania portalu Analytics do pisania zapytań w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 3d228c62cd2d1bcb7f4515cd698186e2ebcbe929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670291"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Pisanie zaawansowanych zapytań w usłudze Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy wykonać wprowadzenie [do usługi Azure Monitor Log Analytics](get-started-portal.md) i wprowadzenie do [zapytań.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Ponowne przywykczenie kodu z
Służy `let` do przypisywania wyników do zmiennej i odwoływania się do niej w dalszej części kwerendy:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Do zmiennych można również przypisać wartości stałe. Obsługuje to metodę konfigurowania parametrów dla pól, które należy zmienić przy każdym wykonaniu kwerendy. W razie potrzeby zmodyfikuj te parametry. Na przykład, aby obliczyć wolne miejsce na dysku i wolną pamięć (w percentyle), w danym oknie czasu:

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

Ułatwia to zmianę czasu rozpoczęcia następnego uruchomienia kwerendy.

### <a name="local-functions-and-parameters"></a>Funkcje i parametry lokalne
Instrukcje służą `let` do tworzenia funkcji, które mogą być używane w tej samej kwerendzie. Na przykład zdefiniuj funkcję, która przyjmuje pole datetime (w formacie UTC) i konwertuje je na standardowy format amerykański. 

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
`print`zwróci tabelę z pojedynczą kolumną i pojedynczym wierszem, pokazując wynik obliczeń. Jest to często używane w przypadkach, gdy potrzebujesz prostego obliczenia. Na przykład, aby znaleźć bieżący czas w PST i dodać kolumnę z EST:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable`umożliwia zdefiniowanie zestawu danych. Podać schemat i zestaw wartości, a następnie potok tabeli do innych elementów kwerendy. Na przykład, aby utworzyć tabelę użycia pamięci RAM i obliczyć ich średnią wartość na godzinę:

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

Konstrukcje datatable są również bardzo przydatne podczas tworzenia tabeli odnośnych. Na przykład, aby zamapować dane tabeli, takie jak identyfikatory zdarzeń z tabeli _SecurityEvent,_ `datatable` do typów zdarzeń wymienionych w innym miejscu, utwórz tabelę odnośnika z typami zdarzeń przy użyciu tych danych i dołącz do niej za pomocą danych _SecurityEvent:_

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

## <a name="next-steps"></a>Następne kroki
Zobacz inne lekcje dotyczące korzystania z [języka zapytań Kusto](/azure/kusto/query/) z danymi dziennika usługi Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Łączy](joins.md)
- [Wykresy](charts.md)
