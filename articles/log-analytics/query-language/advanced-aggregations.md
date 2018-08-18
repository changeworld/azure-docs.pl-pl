---
title: Zaawansowane agregacji w zapytaniach usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: Opisuje niektóre bardziej zaawansowane opcje agregacji, dostępne dla zapytań usługi Log Analytics.
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
ms.openlocfilehash: 4f2d49233a6eb92f567d4265210fcab394aa6461
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190222"
---
# <a name="advanced-aggregations-in-log-analytics-queries"></a>Zaawansowane agregacji w zapytań usługi Log Analytics

> [!NOTE]
> Należy wykonać [agregacji w zapytań usługi Log Analytics](./aggregations.md) przed wykonaniem tej lekcji.

W tym artykule opisano niektóre bardziej zaawansowane opcje agregacji, dostępne dla zapytań usługi Log Analytics.

## <a name="generating-lists-and-sets"></a>Generowanie listy i zestawy
Możesz użyć `makelist` z danymi obrotu przez kolejność wartości w określonej kolumnie. Można na przykład, zapoznaj się z najbardziej typowych kolejność zdarzeń miejsce na maszynach. Zasadniczo można przestawiać danych przez kolejność EventIDs na każdym komputerze. 

```OQL
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```
|Computer (Komputer)|list_EventID|
|---|---|
| KOMPUTER1 | [704,701,1501,1500,1085,704,704,701] |
| KOMPUTER2 | [326,105,302,301,300,102] |
| Przyciski ... | Przyciski ... |

`makelist` generuje listę w kolejności, który danych został przekazany do niego. Aby posortować zdarzenia od najstarszych do najnowszych, należy użyć `asc` w instrukcji order zamiast `desc`. 

Jest to również przydatne do tworzenia listy tylko unikatowe wartości. Jest to nazywane _ustaw_ i mogą być generowane z `makeset`:

```OQL
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```
|Computer (Komputer)|list_EventID|
|---|---|
| KOMPUTER1 | [704,701,1501,1500,1085] |
| KOMPUTER2 | [326,105,302,301,300,102] |
| Przyciski ... | Przyciski ... |

Podobnie jak `makelist`, `makeset` również współpracuje z uporządkowane danych i wygeneruje tablic, na podstawie kolejności wierszy, które są przekazywane do niego.

## <a name="expanding-lists"></a>Rozwijanie listy
Odwróconą operacją `makelist` lub `makeset` jest `mvexpand`, który rozwija listę wartości do rozdzielania wierszy. Można go rozszerzyć w dowolnej liczbie kolumn dynamiczne, zarówno w formacie JSON, jak i w tablicy. Na przykład, można sprawdzić *pulsu* tabeli dla rozwiązań wysyłanie danych z komputerów, które wysłali pulsu w ciągu ostatniej godziny:

```OQL
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```
| Computer (Komputer) | Rozwiązania | 
|--------------|----------------------|
| KOMPUTER1 | "zabezpieczenia", "aktualizacje", "śledzenia zmian" |
| KOMPUTER2 | "zabezpieczenia", "aktualizacji" |
| KOMPUTER3 | "ochrony przed złośliwym kodem", "śledzenia zmian" |
| Przyciski ... | Przyciski ... | Przyciski ... |

Użyj `mvexpand` pokazanie każdej wartości w osobnym wierszu zamiast rozdzielana przecinkami lista:

Puls | gdzie TimeGenerated > ago(1h) | Projekt komputera, Podziel (rozwiązania, ",") | mvexpand rozwiązania
```
| Computer | Solutions | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... | ... |
```

Następnie można użyć `makelist` ponownie do grupowania elementów ze sobą, a teraz wyświetlić listę komputerów, na rozwiązanie:

```OQL
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```
|Rozwiązania | list_Computer |
|--------------|----------------------|
| "zabezpieczenia" | ["Komputer1", "komputer2"] |
| "aktualizacji" | ["Komputer1", "komputer2"] |
| "śledzenia zmian" | ["Komputer1", "KOMPUTER3"] |
| "ochrony przed złośliwym kodem" | ["KOMPUTER3"] |
| Przyciski ... | Przyciski ... |

## <a name="handling-missing-bins"></a>Obsługa brakujących pojemników
Przydatne stosowania `mvexpand` potrzeby wypełnij wartości domyślne dla brakujących pojemniki. Na przykład załóżmy, że szukasz czas działania określonego komputera, eksplorując pulsu. Chcesz zobaczyć źródło pulsu, która jest również _kategorii_ kolumny. Zwykle, możemy użyć prostego Podsumuj instrukcji w następujący sposób:

```OQL
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```
| Kategoria | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agent bezpośredni | 2017-06-06T17:00:00Z | 15 |
| Agent bezpośredni | 2017-06-06T18:00:00Z | 60 |
| Agent bezpośredni | 2017-06-06T20:00:00Z | 55 |
| Agent bezpośredni | 2017-06-06T21:00:00Z | 57 |
| Agent bezpośredni | 2017-06-06T22:00:00Z | 60 |
| Przyciski ... | Przyciski ... | Przyciski ... |

Te wyniki do zasobnika skojarzony z "2017-06-06T19:00:00Z" Brak, ponieważ nie ma żadnych danych pulsu dla danej godziny. Użyj `make-series` funkcję, aby przypisać wartość domyślną do pustych zasobników. Spowoduje to wygenerowanie wiersz dla każdej kategorii, zawierającą dwie kolumny tablicy dodatkowe: jeden dla wartości i jeden dla zgodnego przedziałów czasu:

```OQL
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategoria | count_ | TimeGenerated |
|---|---|---|
| Agent bezpośredni | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| Przyciski ... | Przyciski ... | Przyciski ... |

Trzeci element *count_* tablicy to 0, zgodnie z oczekiwaniami i jest zgodne sygnatura czasowa "2017-06-06T19:00:00.0000000Z" w _TimeGenerated_ tablicy. Ten format tablicy jest trudne do odczytania, mimo że. Użyj `mvexpand` rozwiń tablice i generuje ten sam format danych wyjściowych wygenerowanych przez `summarize`:

```OQL
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```
| Kategoria | TimeGenerated | count_ |
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


```OQL
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Kolejne kroki

Zobacz inne lekcje dla przy użyciu języka zapytań usługi Log Analytics:

- [Operacje na ciągach](string-operations.md)
- [Operacje daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Zaawansowane agregacji](advanced-aggregations.md)
- [JSON i struktur danych](json-data-structures.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)