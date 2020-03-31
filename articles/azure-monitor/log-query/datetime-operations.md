---
title: Praca z wartościami daty i godziny w kwerendach dziennika usługi Azure Monitor| Dokumenty firmy Microsoft
description: W tym artykule opisano sposób pracy z danymi daty i godziny w kwerendach dziennika usługi Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655382"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Praca z wartościami daty i godziny w kwerendach dziennika usługi Azure Monitor

> [!NOTE]
> Przed [ukończeniem](get-started-portal.md) tej lekcji należy wykonać wprowadzenie do portalu Analytics i [wprowadzenie do zapytań.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano sposób pracy z danymi daty i godziny w kwerendach dziennika usługi Azure Monitor.


## <a name="date-time-basics"></a>Podstawowe informacje o dacie
Język zapytania Kusto ma dwa główne typy danych skojarzone z datami i godzinami: datetime i timespan. Wszystkie daty są wyrażone w utc. Podczas gdy obsługiwanych jest wiele formatów datetime, preferowany jest format ISO8601. 

Rozpiętości czasu są wyrażane jako dziesiętne, po którym następuje jednostka czasu:

|Skrót   | jednostka czasu    |
|:---|:---|
|d           | dzień          |
|h           | godzina         |
|m           | minuta       |
|s           | sekunda       |
|Pani          | Milisekundy  |
|mikrosekunda | mikrosekunda  |
|Zaznacz        | Nanosekund   |

Datetimes mogą być tworzone przez `todatetime` rzutowanie ciągu za pomocą operatora. Na przykład, aby przejrzeć pulsy maszyny Wirtualnej wysyłane `between` w określonym przedziale czasu, użyj operatora, aby określić zakres czasu.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Innym typowym scenariuszem jest porównanie datetime do chwili obecnej. Na przykład, aby wyświetlić wszystkie pulsy w ciągu `now` ostatnich dwóch minut, można użyć operatora wraz z timespan, który reprezentuje dwie minuty:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Skrót jest również dostępny dla tej funkcji:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Najkrótsza i najbardziej czytelna metoda `ago` jest jednak przy użyciu operatora:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Załóżmy, że zamiast znać czas rozpoczęcia i zakończenia, znasz czas rozpoczęcia i czas trwania. Kwerendę można przepisać w następujący sposób:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Konwersja jednostek czasu
Można wyrazić datetime lub timespan w jednostce czasu innej niż domyślna. Jeśli na przykład przeglądasz zdarzenia błędów z ostatnich 30 minut i potrzebujesz kolumny obliczeniowej pokazującej, jak dawno temu miało miejsce zdarzenie:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Kolumna `timeAgo` zawiera wartości takie jak: "00:09:31.5118992", co oznacza, że są one sformatowane jako hh:mm:ss.fffff. Jeśli chcesz sformatować `numver` te wartości do minut od godziny rozpoczęcia, podziel tę wartość przez "1 minutę":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregacje i zasobników według interwałów czasu
Innym typowym scenariuszem jest konieczność uzyskania statystyk w określonym okresie czasu w określonym czasie ziarna. W tym scenariuszu `bin` operator może służyć jako część klauzuli podsumowania.

Użyj następującej kwerendy, aby uzyskać liczbę zdarzeń, które wystąpiły co 5 minut w ciągu ostatnich pół godziny:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Ta kwerenda tworzy następującą tabelę:  

|Czasgenerowany(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Innym sposobem tworzenia zasobników wyników jest użycie `startofday`funkcji, takich jak:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Ta kwerenda daje następujące wyniki:

|sygnatura czasowa|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Strefy czasowe
Ponieważ wszystkie wartości datetime są wyrażone w czasie UTC, często jest przydatne do konwersji tych wartości do lokalnej strefy czasowej. Na przykład użyj tego obliczenia, aby przekonwertować czas UTC na czas pst:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funkcje pokrewne

| Kategoria | Funkcja |
|:---|:---|
| Konwertowanie typów danych | [todatetime](/azure/kusto/query/todatetimefunction)  [totimespan](/azure/kusto/query/totimespanfunction)  |
| Zaokrąglanie wartości do rozmiaru pojemnika | [Bin](/azure/kusto/query/binfunction) |
| Uzyskaj określoną datę lub godzinę | [temu](/azure/kusto/query/agofunction) [teraz](/azure/kusto/query/nowfunction)   |
| Uzyskaj część wartości | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Uzyskaj względną wartość daty  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear startofday](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Następne kroki
Zobacz inne lekcje dotyczące korzystania z [języka zapytań Kusto](/azure/kusto/query/) z danymi dziennika usługi Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Łączy](joins.md)
- [Wykresy](charts.md)
