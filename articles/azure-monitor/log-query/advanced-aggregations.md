---
title: Zaawansowane agregacji w zapytaniach dzienników usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Opisuje niektóre bardziej zaawansowane opcje agregacji, dostępne dla usługi Azure Monitor dziennika zapytań.
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
ms.openlocfilehash: 56e87da0353a41504035a070d4c10bab0dda2279
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551757"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Zaawansowane agregacji w zapytaniach dzienników usługi Azure Monitor

> [!NOTE]
> Należy wykonać [agregacji w zapytaniach usługi Azure Monitor](./aggregations.md) przed wykonaniem tej lekcji.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano niektóre bardziej zaawansowane opcje agregacji, dostępne dla zapytań usługi Azure Monitor.

## <a name="generating-lists-and-sets"></a>Generowanie listy i zestawy
Możesz użyć `makelist` z danymi obrotu przez kolejność wartości w określonej kolumnie. Można na przykład, zapoznaj się z najbardziej typowych kolejność zdarzeń miejsce na maszynach. Zasadniczo można przestawiać danych przez kolejność EventIDs na każdym komputerze. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer (Komputer)|list_EventID|
|---|---|
| KOMPUTER1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| Przyciski ... | Przyciski ... |

`makelist` generuje listę w kolejności, który danych został przekazany do niego. Aby posortować zdarzenia od najstarszych do najnowszych, należy użyć `asc` w instrukcji order zamiast `desc`. 

Jest to również przydatne do tworzenia listy tylko unikatowe wartości. Jest to nazywane _ustaw_ i mogą być generowane z `makeset`:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer (Komputer)|list_EventID|
|---|---|
| KOMPUTER1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| Przyciski ... | Przyciski ... |

Podobnie jak `makelist`, `makeset` również współpracuje z uporządkowane danych i wygeneruje tablic, na podstawie kolejności wierszy, które są przekazywane do niego.

## <a name="expanding-lists"></a>Rozwijanie listy
Odwróconą operacją `makelist` lub `makeset` jest `mvexpand`, który rozwija listę wartości do rozdzielania wierszy. Można go rozszerzyć w dowolnej liczbie kolumn dynamiczne, zarówno w formacie JSON, jak i w tablicy. Na przykład, można sprawdzić *pulsu* tabeli dla rozwiązań wysyłanie danych z komputerów, które wysłali pulsu w ciągu ostatniej godziny:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer (Komputer) | Rozwiązania | 
|--------------|----------------------|
| KOMPUTER1 | "zabezpieczenia", "aktualizacje", "śledzenia zmian" |
| computer2 | "zabezpieczenia", "aktualizacji" |
| KOMPUTER3 | "ochrony przed złośliwym kodem", "śledzenia zmian" |
| Przyciski ... | Przyciski ... |

Użyj `mvexpand` pokazanie każdej wartości w osobnym wierszu zamiast rozdzielana przecinkami lista:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer (Komputer) | Rozwiązania | 
|--------------|----------------------|
| KOMPUTER1 | "zabezpieczenia" |
| KOMPUTER1 | "aktualizacji" |
| KOMPUTER1 | "śledzenia zmian" |
| computer2 | "zabezpieczenia" |
| computer2 | "aktualizacji" |
| KOMPUTER3 | "ochrony przed złośliwym kodem" |
| KOMPUTER3 | "śledzenia zmian" |
| Przyciski ... | Przyciski ... |


Następnie można użyć `makelist` ponownie do grupowania elementów ze sobą, a teraz wyświetlić listę komputerów, na rozwiązanie:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Rozwiązania | list_Computer |
|--------------|----------------------|
| "zabezpieczenia" | ["computer1", "computer2"] |
| "aktualizacji" | ["computer1", "computer2"] |
| "śledzenia zmian" | ["computer1", "computer3"] |
| "ochrony przed złośliwym kodem" | ["computer3"] |
| Przyciski ... | Przyciski ... |

## <a name="handling-missing-bins"></a>Obsługa brakujących pojemników
Przydatne stosowania `mvexpand` potrzeby wypełnij wartości domyślne dla brakujących pojemniki. Na przykład załóżmy, że szukasz czas działania określonego komputera, eksplorując pulsu. Chcesz zobaczyć źródło pulsu, która jest również _kategorii_ kolumny. Zwykle, możemy użyć prostego Podsumuj instrukcji w następujący sposób:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agent bezpośredni | 2017-06-06T17:00:00Z | 15 |
| Agent bezpośredni | 2017-06-06T18:00:00Z | 60 |
| Agent bezpośredni | 2017-06-06T20:00:00Z | 55 |
| Agent bezpośredni | 2017-06-06T21:00:00Z | 57 |
| Agent bezpośredni | 2017-06-06T22:00:00Z | 60 |
| Przyciski ... | Przyciski ... | Przyciski ... |

Te wyniki do zasobnika skojarzony z "2017-06-06T19:00:00Z" Brak, ponieważ nie ma żadnych danych pulsu dla danej godziny. Użyj `make-series` funkcję, aby przypisać wartość domyślną do pustych zasobników. Spowoduje to wygenerowanie wiersz dla każdej kategorii, zawierającą dwie kolumny tablicy dodatkowe: jeden dla wartości i jeden dla zgodnego przedziałów czasu:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Category | count_ | TimeGenerated |
|---|---|---|
| Agent bezpośredni | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| Przyciski ... | Przyciski ... | Przyciski ... |

Trzeci element *count_* tablicy to 0, zgodnie z oczekiwaniami i jest zgodne sygnatura czasowa "2017-06-06T19:00:00.0000000Z" w _TimeGenerated_ tablicy. Ten format tablicy jest trudne do odczytania, mimo że. Użyj `mvexpand` rozwiń tablice i generuje ten sam format danych wyjściowych wygenerowanych przez `summarize`:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agent bezpośredni | 2017-06-06T17:00:00Z | 15 |
| Agent bezpośredni | 2017-06-06T18:00:00Z | 60 |
| Agent bezpośredni | 2017-06-06T19:00:00Z | 0 |
| Agent bezpośredni | 2017-06-06T20:00:00Z | 55 |
| Agent bezpośredni | 2017-06-06T21:00:00Z | 57 |
| Agent bezpośredni | 2017-06-06T22:00:00Z | 60 |
| Przyciski ... | Przyciski ... | Przyciski ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Zawężanie wyników zestaw elementów: `let`, `makeset`, `toscalar`, `in`
Typowym scenariuszem jest wybierz nazwy niektórych konkretnych jednostek, w oparciu o określone kryteria, a następnie przeprowadź filtrowanie inny zestaw danych do tego zestawu jednostek. Na przykład możesz znaleźć komputery, które są znane brakujące aktualizacje i identyfikowania adresów IP, że te komputery przestawione do:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
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