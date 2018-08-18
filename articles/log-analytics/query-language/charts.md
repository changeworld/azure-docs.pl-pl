---
title: Tworzenie wykresów i diagramów z zapytań usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano różne wizualizacje w usłudze Azure Log Analytics, aby wyświetlać dane na różne sposoby.
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
ms.openlocfilehash: 359e5e671287c4d330deeb2d3573877d9ee5d1c5
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190214"
---
# <a name="creating-charts-and-diagrams-from-log-analytics-queries"></a>Tworzenie wykresów i diagramów z zapytań usługi Log Analytics

> [!NOTE]
> Należy wykonać [zaawansowane agregacji w zapytań usługi Log Analytics](advanced-aggregations.md) przed wykonaniem tej lekcji.

W tym artykule opisano różne wizualizacje w usłudze Azure Log Analytics, aby wyświetlać dane na różne sposoby.

## <a name="charting-the-results"></a>Tworzenie wykresów wyników
Rozpocznij od przejrzenia liczbę komputerów, które istnieją na system operacyjny, w ciągu ostatniej godziny:

```OQL
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Domyślnie wyniki są wyświetlane jako tabelę:

![Tabela](media/charts/table-display.png)

Uzyskanie lepszego widoku, wybierz **wykresu**i wybierz polecenie **kołowy** możliwość wizualizowania wyników:

![Wykres kołowy](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Timecharts
Pokaż average, 50. i 95. percentyle czasu procesora w pojemnikach równej 1 godz. Zapytanie generuje wiele serii, a następnie możesz wybrać serii do wyświetlenia na wykresie czasu:

```OQL
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Wybierz **wiersza** wykresu opcja wyświetlania:

![Wykres liniowy](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Linii odwołania

Linię odwołania może pomóc w prosty sposób identyfikowania Metryka przekracza określony próg. Aby dodać wiersz do wykresu, należy rozszerzyć zestaw danych o stałej kolumny:

```OQL
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Linii odwołania](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Wiele wymiarów
Wiele wyrażeń w `by` klauzuli `summarize` utworzyć wiele wierszy w wynikach, jeden dla każdej kombinacji wartości.

```OQL
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Po wyświetleniu wyników jako wykres używa pierwszej kolumny z `by` klauzuli. W poniższym przykładzie przedstawiono wykres skumulowany kolumnowy przy użyciu _identyfikator zdarzenia._ Musi mieć wymiary `string` typu, więc w tym przykładzie _EventID_ jest rzutowany na ciąg. 

![EventID wykres słupkowy](media/charts/charts-and-diagrams-multiDimension1.png)

Możesz przełączać się między, wybierając listę rozwijaną z nazwą kolumny. 

![Wykres słupkowy dla konta](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne lekcje dla przy użyciu języka zapytań usługi Log Analytics:

- [Operacje na ciągach](string-operations.md)
- [Operacje daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Zaawansowane agregacji](advanced-aggregations.md)
- [JSON i struktur danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Sprzężenia](joins.md)