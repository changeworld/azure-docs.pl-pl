---
title: Tworzenie wykresów i diagramów z zapytań dzienników Azure Monitor | Microsoft Docs
description: Opisuje różne wizualizacje w Azure Monitor do wyświetlania danych dzienników na różne sposoby.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 34975a1752467c61ea5b329210473eee266c98d1
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900398"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Tworzenie wykresów i diagramów z zapytań dzienników Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy wykonać [Zaawansowane agregacje w zapytaniach dziennika Azure monitor](advanced-aggregations.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano różne wizualizacje w Azure Monitor do wyświetlania danych dzienników na różne sposoby.

## <a name="charting-the-results"></a>Tworzenie wykresów wyników
Zacznij od przejrzenia liczby komputerów dla każdego systemu operacyjnego w ciągu ostatniej godziny:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Domyślnie wyniki są wyświetlane w postaci tabeli:

![Tabela](media/charts/table-display.png)

Aby uzyskać lepszy widok, wybierz pozycję **Wykres**i wybierz opcję **kołowy** , aby wizualizować wyniki:

![Wykres kołowy](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Timecharts
Pokaż średnie, pięćdziesiąt i używany 95. percentyly czasu procesora w pojemnikach o wartości 1 godziny. Zapytanie generuje wiele serii, a następnie można wybrać serie do wyświetlenia na wykresie czasu:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Wybierz opcję wyświetlania wykresu **liniowego** :

![Wykres liniowy](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Wiersz odwołania

Wiersz odwołania może ułatwić identyfikację, czy Metryka przekroczyła określony próg. Aby dodać linię do wykresu, rozwiń zestaw danych za pomocą kolumny stałej:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Wiersz odwołania](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Wiele wymiarów
Wiele wyrażeń w `by` klauzuli `summarize` tworzy wiele wierszy w wynikach, po jednym dla każdej kombinacji wartości.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Po wyświetleniu wyników w postaci wykresu zostanie użyta pierwsza kolumna z klauzuli `by`. Poniższy przykład przedstawia skumulowany wykres kolumnowy przy użyciu _EventId._ Wymiary muszą być typu `string`, więc w tym przykładzie _EventID_ jest rzutowany na ciąg. 

![EventID wykresu słupkowego](media/charts/charts-and-diagrams-multiDimension1.png)

Możesz przełączać się między, wybierając listę rozwijaną z nazwą kolumny. 

![Pasek kont wykresu słupkowego](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi lekcjami dotyczącymi używania [języka zapytań Kusto](/azure/kusto/query/) z danymi dziennika Azure Monitor:

- [Operacje na ciągach](string-operations.md)
- [Operacje na dacie i godzinie](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [JSON i struktury danych](json-data-structures.md)
- [Zaawansowane zapisywanie zapytań](advanced-query-writing.md)
- [Łącze](joins.md)