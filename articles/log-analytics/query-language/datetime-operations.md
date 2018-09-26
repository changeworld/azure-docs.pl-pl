---
title: Praca z wartości daty / godziny w zapytaniach usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób pracy z danymi daty i godziny w zapytań usługi Log Analytics.
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
ms.openlocfilehash: 3a90fa5fb9eadd56b6b01306b251ff9581256bab
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161169"
---
# <a name="working-with-date-time-values-in-log-analytics-queries"></a>Praca z wartości daty / godziny w zapytań usługi Log Analytics

> [!NOTE]
> Należy wykonać [Rozpoczynanie pracy z usługą portalu analiza](get-started-analytics-portal.md) i [wprowadzenie do zapytań](get-started-queries.md) przed wykonaniem tej lekcji.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano sposób pracy z danymi daty i godziny w zapytań usługi Log Analytics.


## <a name="date-time-basics"></a>Podstawowe informacje dotyczące daty czasu
Język zapytań usługi Log Analytics zawiera dwie główne skojarzone z datami i godzinami: datetime i timespan. Wszystkie daty są wyrażone w formacie UTC. Chociaż wiele formatów daty/godziny są obsługiwane, w formacie ISO8601 jest preferowana. 

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

Data/Godzina mogą być tworzone przez rzutowanie na ciąg za pośrednictwem `todatetime` operatora. Na przykład, aby przejrzeć pulsów maszynie Wirtualnej wysyłane w określonym przedziale czasu, aby włączyć korzystanie z [między operator](/azure/kusto/query/betweenoperator) co jest wygodne określić zakres czasu...

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
Może być przydatne do express daty/godziny i przedział czasu w jednostka czasu innej niż domyślna. Na przykład załóżmy, że przeglądasz zdarzeń błędów z ostatnich 30 minut, a następnie muszą kolumnę obliczeniową, która pokazuje, jak dawno temu wystąpiło zdarzenie:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Możesz zobaczyć _timeAgo_ kolumna zawiera wartości takie jak: "00:09:31.5118992", co oznacza, są one formatowane jako hh:mm:ss.fffffff. Jeśli chcesz sformatować tych wartości, aby _numver_ minut od czasu rozpoczęcia, po prostu dzielnikiem tę wartość "1 minuta":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregacje i obsługą zasobników przez przedziały czasu
Inny scenariusz bardzo często jest konieczne uzyskanie statystyk w danym okresie czasu w ziarno czasu określonego. W tym celu `bin` operator może służyć jako część klauzuli summarize.

Użyj następującego zapytania, aby uzyskać numer zdarzenia, które wystąpiły co 5 minut, podczas ostatniej pół godziny:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

To daje poniższej tabeli:  
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

To daje następujące wyniki:

|sygnatura czasowa|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416  |


## <a name="time-zones"></a>Strefy czasowe
Ponieważ wszystkie wartości daty/godziny są wyrażone w formacie UTC, często jest przydatne przekonwertować je na lokalną strefę czasową. Na przykład można użyć tych obliczeń do konwersji czasu UTC na czas PST:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funkcje pokrewne

| Kategoria | Funkcja |
|:---|:---|
| Konwertowanie typów danych | [ToDateTime](/azure/kusto/query/todatetimefunction)[totimespan](/azure/kusto/query/totimespanfunction)  |
| Zaokrągla wartość rozmiar pojemnika | [pojemnika](/azure/kusto/query/binfunction) |
| Pobieranie określonej daty lub godziny | [temu](/azure/kusto/query/agofunction) [teraz](/azure/kusto/query/nowfunction)   |
| Pobierz część wartości | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [miesiąc roku](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweek) [dzieńroku](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Pobierz wartość typu date względem wartości  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne lekcje dla przy użyciu języka zapytań usługi Log Analytics:

- [Operacje na ciągach](string-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Zaawansowane agregacji](advanced-aggregations.md)
- [JSON i struktur danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)