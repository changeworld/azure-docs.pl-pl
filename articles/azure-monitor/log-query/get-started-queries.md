---
title: Wprowadzenie do zapytań dziennika w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Ten artykuł zawiera samouczek dotyczący rozpoczynania pisania zapytań dziennika w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: f56abe2bf6ccea1f55f9b3fe94b75016d449b46b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77670183"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Wprowadzenie do zapytań dziennika w usłudze Azure Monitor

> [!NOTE]
> Można pracować za pośrednictwem tego ćwiczenia w środowisku, jeśli zbierasz dane z co najmniej jednej maszyny wirtualnej. Jeśli nie, to skorzystaj z naszego [środowiska demo,](https://portal.loganalytics.io/demo)które zawiera wiele przykładowych danych.

W tym samouczku dowiesz się pisać zapytania dziennika w usłudze Azure Monitor. Nauczy Cię, jak:

- Opis struktury kwerend
- Sortowanie wyników kwerendy
- Filtrowanie wyników kwerendy
- Określanie zakresu czasu
- Wybieranie pól do uwzględnienia w wynikach
- Definiowanie i używanie pól niestandardowych
- Wyniki agregacji i grup

Aby zapoznać się z samouczkiem na temat korzystania z usługi Log Analytics w portalu Azure, zobacz [Wprowadzenie do usługi Azure Monitor Log Analytics](get-started-portal.md).<br>
Aby uzyskać więcej informacji na temat zapytań dziennika w usłudze Azure Monitor, zobacz [Omówienie zapytań dziennika w usłudze Azure Monitor.](log-query-overview.md)

Postępuj zgodnie z wersją wideo tego samouczka poniżej:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Pisanie nowej kwerendy
Kwerendy można rozpocząć od nazwy tabeli lub polecenia *wyszukiwania.* Należy rozpocząć od nazwy tabeli, ponieważ definiuje ona jasny zakres kwerendy i poprawia zarówno wydajność kwerendy, jak i trafność wyników.

> [!NOTE]
> W języku zapytań Kusto używanym przez usługę Azure Monitor jest uwzględniana wielkość liter. Słowa kluczowe języka są zwykle zapisywane przy użyciu małych liter. Korzystając z nazw tabel lub kolumn w kwerendzie, upewnij się, że używasz poprawnej sprawy, jak pokazano w okienku schematu.

### <a name="table-based-queries"></a>Kwerendy oparte na tabelach
Usługa Azure Monitor organizuje dane dziennika w tabelach, z których każda składa się z wielu kolumn. Wszystkie tabele i kolumny są wyświetlane w okienku schematu w usłudze Log Analytics w portalu Analytics. Zidentyfikuj interesującą Cię tabelę, a następnie przyjrzyj się odrobinie danych:

```Kusto
SecurityEvent
| take 10
```

Kwerenda pokazana powyżej zwraca 10 wyników z *SecurityEvent* tabeli, w żadnej określonej kolejności. Jest to bardzo powszechny sposób, aby rzucić okiem na tabelę i zrozumieć jego strukturę i zawartość. Przyjrzyjmy się, jak to jest zbudowane:

* Kwerenda rozpoczyna się od nazwy tabeli *SecurityEvent* - ta część definiuje zakres kwerendy.
* Znak potoku (|) oddziela polecenia, więc dane wyjściowe pierwszego z danych wejściowych następującego polecenia. Można dodać dowolną liczbę elementów potoku.
* Po potoku jest polecenie **take,** który zwraca określoną liczbę dowolnych rekordów z tabeli.

Możemy uruchomić kwerendę nawet `| take 10` bez dodawania - to nadal będzie prawidłowe, ale może zwrócić do 10 000 wyników.

### <a name="search-queries"></a>Zapytania wyszukiwania
Zapytania wyszukiwania są mniej ustrukturyzowane i zazwyczaj bardziej nadają się do znajdowania rekordów, które zawierają określoną wartość w dowolnej z ich kolumn:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Ta kwerenda przeszukuje tabelę *SecurityEvent* w poszukiwaniu rekordów zawierających frazę "Kryptograficzna". Z tych rekordów zostanie zwróconych i wyświetlonych 10 rekordów. Jeśli pominiemy `in (SecurityEvent)` część `search "Cryptographic"`i po prostu uruchomimy , wyszukiwanie przejdzie przez *wszystkie* tabele, co zajmie więcej czasu i będzie mniej wydajne.

> [!WARNING]
> Zapytania wyszukiwania są zazwyczaj wolniejsze niż kwerendy oparte na tabelach, ponieważ muszą przetwarzać więcej danych. 

## <a name="sort-and-top"></a>Sortowanie i top
Chociaż **take** jest przydatne, aby uzyskać kilka rekordów, wyniki są wybierane i wyświetlane w żadnej określonej kolejności. Aby uzyskać uporządkowany widok, możesz **posortować** według preferowanej kolumny:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

To może zwrócić zbyt wiele wyników choć i może również zająć trochę czasu. Powyższa kwerenda sortuje *całą tabelę* SecurityEvent według kolumny TimeGenerated. Portal Analytics ogranicza wyświetlanie do wyświetlania tylko 10 000 rekordów. Takie podejście nie jest oczywiście optymalne.

Najlepszym sposobem uzyskania tylko najnowszych 10 rekordów jest użycie **górnej**, która sortuje całą tabelę po stronie serwera, a następnie zwraca najlepsze rekordy:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Malejąco jest domyślną kolejnością sortowania, więc zazwyczaj pomijamy argument **desc.** Dane wyjściowe będą wyglądać następująco:

![Top 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Gdzie:filtrowanie pod warunkiem
Filtry, zgodnie z ich nazwą, filtrują dane według określonego warunku. Jest to najczęstszy sposób ograniczania wyników kwerendy do odpowiednich informacji.

Aby dodać filtr do kwerendy, należy użyć operatora **where,** po którym następuje co najmniej jeden z warunków. Na przykład następująca kwerenda zwraca tylko rekordy *SecurityEvent,* gdzie _poziom_ jest równy _8:_

```Kusto
SecurityEvent
| where Level == 8
```

Podczas pisania warunków filtrowania można użyć następujących wyrażeń:

| Wyrażenie | Opis | Przykład |
|:---|:---|:---|
| == | Sprawdź równość<br>(z uwzględnieniem wielkości liter) | `Level == 8` |
| =~ | Sprawdź równość<br>(niewrażliwe na argumenty) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Sprawdzanie nierówności<br>(oba wyrażenia są identyczne) | `Level != 4` |
| *oraz*, *lub* | Wymagane między warunkami| `Level == 16 or CommandLine != ""` |

Aby filtrować według wielu warunków, można użyć **i:**

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

lub rury **wielokrotne, gdzie** elementy jeden po drugim:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Wartości mogą mieć różne typy, więc może być konieczne rzutowanie ich do przeprowadzenia porównania na poprawny typ. Na przykład securityevent *level* kolumna jest typu String, więc należy rzutować go do typu numerycznego, takich jak *int* lub *long*, zanim będzie można użyć operatorów numerycznych na nim:`SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Określanie zakresu czasu

### <a name="time-picker"></a>Selektor czasu
Selektor czasu znajduje się obok przycisku Uruchom i wskazuje, że wysyłamy zapytania tylko do rekordów z ostatnich 24 godzin. Jest to domyślny zakres czasu stosowany do wszystkich zapytań. Aby uzyskać tylko rekordy z ostatniej godziny, wybierz _opcję Ostatnia godzina_ i uruchom kwerendę ponownie.

![Selektor czasu](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtr czasu w kwerendzie
Można również zdefiniować własny zakres czasu, dodając filtr czasu do kwerendy. Najlepiej umieścić filtr czasu natychmiast po nazwie tabeli: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

W powyższym `ago(30m)` filtrze czasu oznacza "30 minut temu", więc ta kwerenda zwraca tylko rekordy z ostatnich 30 minut. Inne jednostki czasu to dni (2d), minuty (25 m) i sekundy (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Projekt i rozszerzanie: wybieranie i obliczanie kolumn
Użyj **projektu,** aby wybrać określone kolumny do uwzględnienia w wynikach:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

W poprzednim przykładzie generuje to dane wyjściowe:

![Zapytanie o wyniki projektu](media/get-started-queries/project.png)

Można również użyć **projektu,** aby zmienić nazwę kolumn i zdefiniować nowe. W poniższym przykładzie użyto projektu do wykonania następujących czynności:

* Wybierz tylko oryginalne kolumny *Komputer* i *CzasGenerowane.*
* Zmień nazwę kolumny *Działanie* na *EventDetails*.
* Utwórz nową kolumnę o nazwie *EventCode*. Funkcja **substring()** jest używana do uzyskania tylko pierwszych czterech znaków z pola Działanie.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**extend** zachowuje wszystkie oryginalne kolumny w zestawie wyników i definiuje dodatkowe. Poniższa kwerenda używa **rozszerzenia,** aby dodać kolumnę *EventCode.* Należy zauważyć, że ta kolumna może nie być wyświetlana na końcu wyników tabeli, w którym to przypadku należy rozwinąć szczegóły rekordu, aby go wyświetlić.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Podsumowanie: zagregowane grupy wierszy
Użyj **podsumowania,** aby zidentyfikować grupy rekordów, zgodnie z jedną lub kilkoma kolumnami, i zastosować do nich agregacje. Najczęstszym zastosowaniem **sumowania** jest *liczba*, która zwraca liczbę wyników w każdej grupie.

Następująca kwerenda przegląda wszystkie rekordy *perf* z ostatniej godziny, grupuje je według *ObjectName*i zlicza rekordy w każdej grupie: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Czasami sensowne jest definiowanie grup według wielu wymiarów. Każda unikalna kombinacja tych wartości definiuje oddzielną grupę:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Innym powszechnym zastosowaniem jest wykonywanie obliczeń matematycznych lub statystycznych na każdej grupie. Na przykład poniższe oblicza średnią *wartość CounterValue* dla każdego komputera:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Niestety wyniki tej kwerendy są bez znaczenia, ponieważ mieszamy ze sobą różne liczniki wydajności. Aby uczynić to bardziej znaczącym, powinniśmy obliczyć średnią oddzielnie dla każdej kombinacji *CounterName* i *Computer:*

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Podsumowanie według kolumny czasu
Wyniki grupowania mogą być również oparte na kolumnie czasu lub innej wartości ciągłej. Po prostu `by TimeGenerated` podsumowanie jednak utworzy grupy dla każdej milisekundy w zakresie czasu, ponieważ są to unikatowe wartości. 

Aby utworzyć grupy na podstawie wartości ciągłych, najlepiej podzielić zakres na zarządzane jednostki za pomocą **pojemnika**. Poniższa kwerenda analizuje rekordy *perf,* które mierzą wolną pamięć *(Dostępne Bajty)* na określonym komputerze. Oblicza średnią wartość każdego okresu 1 godziny w ciągu ostatnich 7 dni:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Aby dane wyjściowe były wyraźniejsze, należy wybrać wyświetlanie go jako wykresu czasu, pokazując dostępnej pamięci w czasie:

![Pamięć kwerend w czasie](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o używaniu danych ciągu w kwerendzie dziennika [z workiem z ciągami w kwerendach dziennika usługi Azure Monitor.](string-operations.md)
- Dowiedz się więcej o agregowaniu danych w kwerendzie dziennika za pomocą [zaawansowanych agregacji w kwerendach dziennika usługi Azure Monitor.](advanced-aggregations.md)
- Dowiedz się, jak dołączać dane z wielu tabel za pomocą [sprzężeń w kwerendach dziennika usługi Azure Monitor.](joins.md)
- Pobierz dokumentację dotyczącą całego języka zapytań Kusto w [odwołaniu do języka KQL](/azure/kusto/query/).
