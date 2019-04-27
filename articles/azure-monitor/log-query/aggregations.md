---
title: Agregacje w usłudze Azure Monitor rejestrowania zapytań | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje agregacji w zapytaniach dzienników usługi Azure Monitor, które oferują przydatna do analizowania danych.
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
ms.openlocfilehash: 864e9586082ed95bf17135414ec4b879e3034ace
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589629"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Agregacje w zapytaniach dzienników usługi Azure Monitor

> [!NOTE]
> Należy wykonać [Rozpoczynanie pracy z usługą portalu analiza](get-started-portal.md) i [wprowadzenie do zapytań](get-started-queries.md) przed wykonaniem tej lekcji.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano funkcje agregacji w zapytaniach dzienników usługi Azure Monitor, które oferują przydatna do analizowania danych. Te wszystkie funkcje współdziałają z `summarize` operator, który tworzy tabelę z zagregowanych wyników w tabeli wejściowej.

## <a name="counts"></a>Liczniki

### <a name="count"></a>count
Liczbę wierszy w zestawie, po zastosowaniu wszystkie filtry wyników. Poniższy przykład zwraca łączną liczbę wierszy w _wydajności_ tabelę z ostatnich 30 minut. Wynik jest zwracany w kolumnie o nazwie *count_* , chyba że przypisać określonej nazwy:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Wizualizację na wykres czasu może być przydatne wyświetlić trend wraz z upływem czasu:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Dane wyjściowe, w tym przykładzie przedstawiono wydajności trendline liczba rekordów w odstępach 5 minut:

![Trend liczby](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>Funkcja, dcountif
Użyj `dcount` i `dcountif` na różne wartości w określonej kolumnie. Następujące zapytanie oblicza liczbę unikatowych komputerów pulsy w ciągu ostatniej godziny:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Aby zliczyć tylko komputery z systemem Linux wysyłające pulsy, należy użyć `dcountif`:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Ocena podgrupy
Aby przeprowadzić liczbą lub inne agregacje podgrup w Twoich danych, należy użyć `by` — słowo kluczowe. Na przykład, aby określić liczbę unikatowych komputerów z systemem Linux, wysyłające pulsy w każdym kraju:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|Stany Zjednoczone    | 19                  |
|Kanada           | 3                   |
|Irlandia          | 0                   |
|Zjednoczone Królestwo   | 0                   |
|Holandia      | 2                   |


W celu przeanalizowania mniejszych podgrupy danych, należy dodać dodatkową kolumnę nazwy `by` sekcji. Na przykład możesz chcieć liczba unikatowych komputerów, z każdego kraju na OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentyle i Wariancja
Podczas obliczania wartości liczbowe, powszechną praktyką jest średnia je przy użyciu `summarize avg(expression)`. Średnie dotyczy wartości skrajne, charakteryzujące tylko kilka przypadków. Aby rozwiązać ten problem, można użyć mniej ważnych funkcji takich jak `median` lub `variance`.

### <a name="percentile"></a>Percentyl
Aby znaleźć wartość mediany, użyj `percentile` funkcji z wartością do określenia percentyla:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Można również określić różne percentyle można pobrać zagregowany wynik dla każdego:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

To pokazuje niektóre komputera procesory CPU mają podobne wartości mediany, że mimo że niektóre stały wokół mediany, inne komputery zgłaszali znacznie niższe a wyższe wartości procesora CPU, co oznacza, że wystąpił skoki.

### <a name="variance"></a>Wariancja
Aby ocenić bezpośrednio wariancję wartości, należy użyć odchylenie standardowe i Wariancja metody:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Dobrym sposobem na analizowanie stabilności użycie procesora CPU jest połączyć stdev mediany obliczeń:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Zobacz inne lekcje dotyczące korzystania z [język zapytania Kusto](/azure/kusto/query/) z usługą Azure Monitor możesz rejestrować dane:

- [Operacje na ciągach](string-operations.md)
- [Operacje daty i godziny](datetime-operations.md)
- [Zaawansowane agregacji](advanced-aggregations.md)
- [JSON i struktur danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)
