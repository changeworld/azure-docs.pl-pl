---
title: Tworzenie wykresów i diagramów z zapytań dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano różne wizualizacje w usłudze Azure Monitor, aby wyświetlić dane dziennika na różne sposoby.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670325"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Tworzenie wykresów i diagramów z zapytań dziennika usługi Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy ukończyć [zaawansowane agregacje w kwerendach dziennika usługi Azure Monitor.](advanced-aggregations.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano różne wizualizacje w usłudze Azure Monitor do wyświetlania danych dziennika na różne sposoby.

## <a name="charting-the-results"></a>Tworzenie wykresów wyników
Zacznij od sprawdzenia, ile komputerów jest na system operacyjny, w ciągu ostatniej godziny:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Domyślnie wyniki są wyświetlane jako tabela:

![Tabela](media/charts/table-display.png)

Aby uzyskać lepszy widok, wybierz **opcję Wykres**i wybierz opcję **Koł,** aby wizualizować wyniki:

![Wykres kołowy](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Wykresy czasowe
Pokaż średni, 50 i 95 percentyle czasu procesora w pojemnikach 1 godziny. Kwerenda generuje wiele serii, a następnie można wybrać, które serie mają być wyświetlane na wykresie czasu:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Wybierz opcję wyświetlania wykresu **liniowego:**

![Wykres liniowy](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Linia odniesienia

Linia referencyjna może pomóc w łatwej identyfikacji, czy metryka przekroczyła określony próg. Aby dodać linię do wykresu, rozszerz zestaw danych o stałą kolumnę:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Linia odniesienia](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Wiele wymiarów
Wiele wyrażeń `by` w `summarize` klauzuli tworzenia wielu wierszy w wynikach, po jednym dla każdej kombinacji wartości.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Podczas wyświetlania wyników jako wykres, używa pierwszej kolumny `by` z klauzuli. W poniższym przykładzie przedstawiono skumulowany wykres kolumnowy przy użyciu _identyfikatora zdarzeń._ Wymiary muszą `string` być typu, więc w tym przykładzie _EventID_ jest rzutowy na ciąg. 

![Identyfikator zdarzeń wykresu słupkowego](media/charts/charts-and-diagrams-multiDimension1.png)

Można przełączać się między, wybierając listy rozwijanej z nazwą kolumny. 

![Typ konta wykresu słupkowego](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Następne kroki
Zobacz inne lekcje dotyczące korzystania z [języka zapytań Kusto](/azure/kusto/query/) z danymi dziennika usługi Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Łączy](joins.md)
