---
title: Praca z wartości daty / godziny w zapytaniach dzienników usługi Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób pracy z danych daty i godziny w zapytaniach dzienników usługi Azure Monitor.
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
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 402511ba3c45e8bd12cb7f92ecd54f6084c8ada2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112361"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Praca z wartości daty / godziny w zapytaniach dzienników usługi Azure Monitor

> [!NOTE]
> Należy wykonać [Rozpoczynanie pracy z usługą portalu analiza](get-started-portal.md) i [wprowadzenie do zapytań](get-started-queries.md) przed wykonaniem tej lekcji.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano sposób pracy z danych daty i godziny w zapytaniach dzienników usługi Azure Monitor.


## <a name="date-time-basics"></a>Podstawowe informacje dotyczące daty czasu
Język zapytania Kusto zawiera dwie główne skojarzone z datami i godzinami: datetime i timespan. Wszystkie daty są wyrażone w formacie UTC. Chociaż wiele formatów daty/godziny są obsługiwane, w formacie ISO8601 jest preferowana. 

Timespans są wyrażane jako ułamek dziesiętny jednostkę czasu:

|skrót   | Jednostka czasu    |
|:---|:---|
|d.           | dzień          |
|g           | godz.         |
|ś           | minuta       |
|s           | sekunda       |
|ms          | Milisekundy  |
|mikrosekund | mikrosekund  |
|znaczników        | nanosekundowych   |

Data/Godzina mogą być tworzone przez rzutowanie na ciąg za pośrednictwem `todatetime` operatora. Na przykład, aby przejrzeć pulsów maszynie Wirtualnej wysyłane w określonym przedziale czasu, należy użyć `between` operatora, aby określić zakres czasu.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Inny typowy scenariusz porównuje wartość typu datetime do chwili obecnej. Na przykład, aby wyświetlić wszystkich interwałów pulsu w ciągu ostatnich dwóch minut, można użyć `now` operator wraz z przedziału czasu, który reprezentuje dwie minuty:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Skrót jest również dostępna dla tej funkcji:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Metoda najkrótszej i najbardziej czytelne jednak korzysta `ago` operator:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Załóżmy, że zamiast, wiedząc, godzina rozpoczęcia i zakończenia, wiesz, czas rozpoczęcia i czas trwania. Można ponownie napisać zapytanie w następujący sposób:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Konwertowanie jednostek czasu
Możesz chcieć express datetime i timespan w jednostkach czasu, inną niż domyślna. Na przykład jeśli recenzowania zdarzeń błędów z ostatnich 30 minut i potrzebujesz kolumny obliczeniowej pokazujący czas przed jakim wystąpiło zdarzenie:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo` Kolumna zawiera wartości takie jak: "00:09:31.5118992", co oznacza, że są one formatowane jako hh:mm:ss.fffffff. Jeśli chcesz sformatować tych wartości, aby `numver` minut od czasu rozpoczęcia dzielnikiem tę wartość "1 minuta":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregacje i obsługą zasobników przez przedziały czasu
Innym typowym scenariuszem jest konieczne uzyskanie statystyk w danym okresie czasu w ziarno czasu określonego. W tym scenariuszu `bin` operator może służyć jako część klauzuli summarize.

Użyj następującego zapytania, aby uzyskać numer zdarzenia, które wystąpiły co 5 minut, podczas ostatniej pół godziny:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

To zapytanie tworzy poniższej tabeli:  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Innym sposobem na utworzenie zasobników wyników jest używać funkcji, takich jak `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

To zapytanie tworzy następujące wyniki:

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Strefy czasowe
Ponieważ wszystkie wartości daty/godziny są wyrażone w formacie UTC, często jest przydatne przekonwertować te wartości do lokalnej strefy czasowej. Na przykład można użyć tych obliczeń do konwersji czasu UTC na czas PST:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funkcje pokrewne

| Category | Funkcja |
|:---|:---|
| Konwertowanie typów danych | [ToDateTime](/azure/kusto/query/todatetimefunction)[totimespan](/azure/kusto/query/totimespanfunction)  |
| Zaokrągla wartość rozmiar pojemnika | [pojemnika](/azure/kusto/query/binfunction) |
| Pobieranie określonej daty lub godziny | [temu](/azure/kusto/query/agofunction) [teraz](/azure/kusto/query/nowfunction)   |
| Pobierz część wartości | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Pobieranie wartości dat względnych  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne lekcje dotyczące korzystania z [język zapytania Kusto](/azure/kusto/query/) z usługą Azure Monitor możesz rejestrować dane:

- [Operacje na ciągach](string-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Zaawansowane agregacji](advanced-aggregations.md)
- [JSON i struktur danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)