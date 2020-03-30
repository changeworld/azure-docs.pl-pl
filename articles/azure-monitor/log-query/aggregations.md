---
title: Agregacje w kwerendach dziennika usługi Azure Monitor| Dokumenty firmy Microsoft
description: W tym artykule opisano funkcje agregacji w kwerendach dziennika usługi Azure Monitor, które oferują przydatne sposoby analizowania danych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d164c53e7e2be55f3cede389901a256ba388808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670308"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Agregacje w kwerendach dziennika usługi Azure Monitor

> [!NOTE]
> Przed [ukończeniem](get-started-portal.md) tej lekcji należy wykonać wprowadzenie do portalu Analytics i [wprowadzenie do zapytań.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano funkcje agregacji w kwerendach dziennika usługi Azure Monitor, które oferują przydatne sposoby analizowania danych. Wszystkie te funkcje `summarize` działają z operatorem, który tworzy tabelę z zagregowanymi wynikami tabeli wejściowej.

## <a name="counts"></a>Liczy

### <a name="count"></a>count
Policz liczbę wierszy w zestawie wyników po zastosowaniu filtrów. Poniższy przykład zwraca całkowitą liczbę wierszy w tabeli _Perf_ z ostatnich 30 minut. Wynik jest zwracany w kolumnie o nazwie *count_,* chyba że zostanie przypisana określona nazwa:


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

Wizualizacja schematu czasowego może być przydatna, aby zobaczyć trend w czasie:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Dane wyjściowe z tego przykładu pokazuje linię trendu liczby rekordów perf w odstępach 5 minut:

![Trend liczenia](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Użyj `dcount` `dcountif` i zliczanie różnych wartości w określonej kolumnie. W poniższej kwerendzie ocenia się, ile różnych komputerów wysłało puls w ciągu ostatniej godziny:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Aby policzyć tylko komputery z systemem `dcountif`Linux, które wysłały bicie serca, użyj:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Ocena podgrup
Aby wykonać liczbę lub inne agregacje w podgrupach w danych, użyj słowa kluczowego. `by` Na przykład, aby policzyć liczbę różnych komputerów z systemem Linux, które wysyłały pulsy w każdym kraju/regionie:

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
|Wielka Brytania   | 0                   |
|Holandia      | 2                   |


Aby przeanalizować jeszcze mniejsze podgrupy danych, dodaj `by` do sekcji dodatkowe nazwy kolumn. Na przykład można policzyć różne komputery z każdego kraju/regionu na OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentyle i wariancja
Przy ocenie wartości liczbowych powszechną praktyką `summarize avg(expression)`jest ich uśrednianie za pomocą . Średnie są dotknięte wartościami skrajnymi, które charakteryzują tylko kilka przypadków. Aby rozwiązać ten problem, można użyć `median` mniej `variance`poufnych funkcji, takich jak lub .

### <a name="percentile"></a>Percentyl
Aby znaleźć wartość mediany, użyj `percentile` funkcji z wartością, aby określić percentyl:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Można również określić różne percentyle, aby uzyskać zagregowany wynik dla każdego:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Może to pokazać, że niektóre procesory komputerowe mają podobne wartości mediany, ale podczas gdy niektóre są stałe wokół mediany, inne komputery zgłosiły znacznie niższe i wyższe wartości procesora, co oznacza, że doświadczyły skoków.

### <a name="variance"></a>Wariancja
Aby bezpośrednio ocenić wariancję wartości, należy użyć metod odchylenia standardowego i wariancji:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Dobrym sposobem analizy stabilności użycia procesora jest połączenie stdev z medianą obliczeń:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Zobacz inne lekcje dotyczące korzystania z [języka zapytań Kusto](/azure/kusto/query/) z danymi dziennika usługi Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Łączy](joins.md)
- [Wykresy](charts.md)
