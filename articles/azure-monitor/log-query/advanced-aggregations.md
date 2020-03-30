---
title: Zaawansowane agregacje w kwerendach dziennika usługi Azure Monitor| Dokumenty firmy Microsoft
description: W tym artykule opisano niektóre z bardziej zaawansowanych opcji agregacji dostępnych dla zapytań dziennika usługi Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: e5dc290a40342e0797001dde6cab90e12dd5cf39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662182"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Zaawansowane agregacje w kwerendach dziennika usługi Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy wykonać [agregacje w kwerendach usługi Azure Monitor.](./aggregations.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano niektóre z bardziej zaawansowanych opcji agregacji dostępnych dla zapytań usługi Azure Monitor.

## <a name="generating-lists-and-sets"></a>Generowanie list i zestawów
Można użyć `makelist` do obrotu danych według kolejności wartości w określonej kolumnie. Na przykład można zbadać najczęściej zdarzenia zamówienia odbywają się na komputerach. Zasadniczo można obracać dane według kolejności EventIDs na każdym komputerze. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer (Komputer)|list_EventID|
|---|---|
| komputer1 | [704,701,1501,1500,1085,704,704,701] |
| komputer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist`generuje listę w kolejności, w jakiej dane zostały do niej przekazane. Aby sortować zdarzenia od najstarszych do najnowszych, użyj `asc` w instrukcji zamówienia zamiast `desc`. 

Jest również przydatne do tworzenia listy tylko różne wartości. Jest to tak zwany _zestaw_ `makeset`i mogą być generowane z:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer (Komputer)|list_EventID|
|---|---|
| komputer1 | [704,701,1501,1500,1085] |
| komputer2 | [326,105,302,301,300,102] |
| ... | ... |

Like `makelist` `makeset` , działa również z uporządkowanymi danymi i wygeneruje tablice na podstawie kolejności wierszy, które są przekazywane do niego.

## <a name="expanding-lists"></a>Rozwijanie list
Odwrotna operacja `makelist` lub `makeset` `mvexpand`jest , która rozszerza listę wartości do oddzielnych wierszy. Można rozwinąć w dowolnej liczbie kolumn dynamicznych, zarówno JSON i tablicy. Na przykład można sprawdzić w tabeli *Pulsu pod* kątem rozwiązań wysyłających dane z komputerów, które wysłały bicie serca w ciągu ostatniej godziny:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer (Komputer) | Rozwiązania | 
|--------------|----------------------|
| komputer1 | "bezpieczeństwo", "aktualizacje", "zmianaTracking" |
| komputer2 | "bezpieczeństwo", "aktualizacje" |
| komputer3 | "antiMalware", "changeTracking" |
| ... | ... |

Służy `mvexpand` do pokazywalenia każdej wartości w osobnym wierszu zamiast listy oddzielonej przecinkami:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer (Komputer) | Rozwiązania | 
|--------------|----------------------|
| komputer1 | "bezpieczeństwo" |
| komputer1 | "aktualizacje" |
| komputer1 | "changeTracking" |
| komputer2 | "bezpieczeństwo" |
| komputer2 | "aktualizacje" |
| komputer3 | "antiMalware" |
| komputer3 | "changeTracking" |
| ... | ... |


Następnie można `makelist` użyć ponownie do grupowanie elementów razem, a tym razem zobacz listę komputerów na rozwiązanie:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Rozwiązania | list_Computer |
|--------------|----------------------|
| "bezpieczeństwo" | ["komputer1", "komputer2"] |
| "aktualizacje" | ["komputer1", "komputer2"] |
| "changeTracking" | ["komputer1", "komputer3"] |
| "antiMalware" | ["komputer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Obsługa brakujących pojemników
Przydatna jest `mvexpand` potrzeba wypełnienia wartości domyślnych brakujących pojemników. Załóżmy na przykład, że szukasz czasu pracy danej maszyny, eksplorując jej bicie serca. Chcesz również zobaczyć źródło pulsu, które znajduje się w kolumnie _kategorii._ Zwykle używamy prostego podsumowania w następujący sposób:

```Kusto
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
| ... | ... | ... |

W tych wynikach brakuje jednak zasobnika skojarzonego z "2017-06-06T19:00:00Z", ponieważ nie ma żadnych danych pulsu dla tej godziny. Użyj `make-series` funkcji, aby przypisać wartość domyślną do pustych zasobników. Spowoduje to wygenerowanie wiersza dla każdej kategorii z dwiema dodatkowymi kolumnami tablicy, jedną dla wartości i jedną dla pasujących zasobników czasu:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategoria | count_ | TimeGenerated |
|---|---|---|
| Agent bezpośredni | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.000000Z","2017-06-06T18:00:00.000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Trzeci element *tablicy count_* jest 0 zgodnie z oczekiwaniami i istnieje pasujące sygnatury czasowej "2017-06-06T19:00:00.0000000Z" w _TimeGenerated_ tablicy. Ten format tablicy jest jednak trudny do odczytania. Służy `mvexpand` do rozwijania tablic i tworzenia tego `summarize`samego formatu wyjściowego, co generowane przez:

```Kusto
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
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Zawężenie wyników do `let` `makeset`zestawu `toscalar`elementów: , ,`in`
Typowym scenariuszem jest wybranie nazw niektórych określonych jednostek na podstawie zestawu kryteriów, a następnie filtrowanie różnych danych ustawionych na ten zestaw jednostek. Na przykład można znaleźć komputery, o których wiadomo, że mają brakujące aktualizacje i zidentyfikować wiadomości IP, do których te komputery wywoływały:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Następne kroki

Zobacz inne lekcje dotyczące korzystania z [języka zapytań Kusto](/azure/kusto/query/) z danymi dziennika usługi Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Łączy](joins.md)
- [Wykresy](charts.md)
