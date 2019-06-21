---
title: Wprowadzenie do zapytań dzienników w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera samouczek dla klientów zaczynających pracę Pisanie zapytań dzienników w usłudze Azure Monitor.
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
ms.date: 05/09/2019
ms.author: bwren
ms.openlocfilehash: b03109ee5cdb76247bf3be6fda97e0cf6e434f17
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296087"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Wprowadzenie do zapytań dzienników w usłudze Azure Monitor


> [!NOTE]
> Należy wykonać [Rozpoczynanie pracy z usługą Azure Monitor Log Analytics](get-started-portal.md) przed ukończenie tego samouczka.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym samouczku dowiesz się do pisania zapytań dzienników w usłudze Azure Monitor. Jego nauczą Cię, jak do:

- Zrozumienie struktury zapytania
- Sortowanie wyników zapytania
- Filtrowanie wyników zapytania
- Określ zakres czasu
- Wybierz pola do uwzględnienia w wynikach
- Definiowanie i korzystanie z polami niestandardowymi
- Wyniki agregacji i grupy

Aby uzyskać samouczek na temat korzystania z usługi Log Analytics w witrynie Azure portal, zobacz [Rozpoczynanie pracy z usługą Azure Monitor Log Analytics](get-started-portal.md).<br>
Aby uzyskać szczegółowe informacje na temat zapytań dzienników w usłudze Azure Monitor, zobacz [Przegląd dziennika zapytań w usłudze Azure Monitor](log-query-overview.md).

## <a name="writing-a-new-query"></a>Zapisywanie nowego zapytania
Zapytania można zacząć od jednej nazwy tabeli lub *wyszukiwania* polecenia. Należy rozpocząć z nazwą tabeli, ponieważ definiuje wyczyść zakres zapytania i zwiększa wydajność zapytań i znaczenie wyników.

> [!NOTE]
> Język zapytania Kusto, używany przez usługi Azure Monitor jest uwzględniana wielkość liter. Słowa kluczowe języka są zwykle zapisywane w małe. Korzystając z nazwy tabel lub kolumn w zapytaniu, pamiętaj mieć poprawną wielkość, jak pokazano w okienku schematu.

### <a name="table-based-queries"></a>Zapytania w oparciu o tabeli
Usługa Azure Monitor organizuje dane dziennika w tabelach, każdy składa się z wielu kolumn. Wszystkie tabele i kolumny są wyświetlane w okienku schematu w usłudze Log Analytics w portalu usługi analiza. Określ tabelę interesują, a następnie zapoznaj się z bitu danych:

```Kusto
SecurityEvent
| take 10
```

Powyżej zwraca 10 wyników z *SecurityEvent* tabeli w określonej kolejności. Jest to bardzo typowy sposób pobrać pierwszy rzut oka na tabelę i zrozumienie jego struktury i zawartości. Przeanalizujmy, jak jest zbudowany:

* Zapytania zaczyna się od nazwy tabeli *SecurityEvent* — ta część definiuje zakres kwerendy.
* Znaku kreski pionowej (|) oddziela polecenia, więc dane wyjściowe pierwszy z nich w danych wejściowych następującego polecenia. Możesz dodać dowolną liczbę elementów gazociągami.
* Zgodnie z potoku jest **zająć** polecenia, które zwraca określoną liczbę dowolnego rekordów z tabeli.

Firma Microsoft może faktycznie uruchomić zapytanie, nawet bez dodawania `| take 10` — która nadal będzie nieprawidłowa, ale może on zwrócić wyniki do 10 000.

### <a name="search-queries"></a>Zapytania wyszukiwania
Zapytania wyszukiwania są mniej ze strukturą i zazwyczaj bardziej odpowiednie do znajdowania rekordy, które zawierają określone wartości we wszystkich kolumn:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

To zapytanie wyszukuje *SecurityEvent* tabeli rekordy, które zawierają frazę "Szyfrowania". Z tych rekordów 10 rekordów zostaną zwrócone i wyświetlone. Firma Microsoft pominięcia `in (SecurityEvent)` części i po prostu uruchomić `search "Cryptographic"`, wyszukiwanie zostanie umieszczona *wszystkie* tabel, które będzie trwać dłużej i mniej wydajne rozwiązanie.

> [!WARNING]
> Zapytania wyszukiwania są zazwyczaj wolniej niż na podstawie tabeli zapytania, ponieważ mają przetworzyć większej ilości danych. 

## <a name="sort-and-top"></a>Sortuj i z góry
Gdy **zająć** jest przydatne uzyskać kilka rekordów, wyniki są zaznaczone i wyświetlane w losowej kolejności. Aby uzyskać uporządkowane widok, można wykonać następujące akcje **sortowania** przez preferowany kolumny:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Który może zwrócić jednak zbyt wiele wyników, a także może zająć trochę czasu. Sortuje w powyższym zapytaniu *całą* SecurityEvent tabelę według kolumny TimeGenerated. Portal analiza ogranicza następnie ekranu, aby pokazać tylko 10 000 rekordów. Podejście to oczywiście nie jest optymalna.

Najlepszy sposób pozyskania najnowszych 10 rekordów jest użycie **górnej**, która sortuje całą tabelę po stronie serwera, a następnie zwraca pierwszych rekordów:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

DESCENDING jest domyślne sortowanie kolejności, dlatego zazwyczaj pominąć **desc** argumentu. Dane wyjściowe będą wyglądać następująco:

![10 najważniejszych](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Tam, gdzie: warunek filtrowania
Filtry, wskazane przez ich nazw, filtrować dane według określonego warunku. Jest to najbardziej popularny sposób w celu ograniczenia wyników zapytania do odpowiednich informacji.

Aby dodać filtr do kwerendy, należy użyć **gdzie** operator następuje co najmniej jeden warunek. Na przykład, następujące zapytanie zwraca tylko *SecurityEvent* rekordy, w których _poziom_ jest równa _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Podczas pisania warunków filtrowania, można użyć następujących wyrażeń:

| Wyrażenie | Opis | Przykład |
|:---|:---|:---|
| == | Sprawdź równości<br>(z uwzględnieniem wielkości liter) | `Level == 8` |
| =~ | Sprawdź równości<br>(bez uwzględniania wielkości liter) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Sprawdź nierówności<br>(oba wyrażenia są identyczne) | `Level != 4` |
| *i*, *lub* | Wymagane między warunkami| `Level == 16 or CommandLine != ""` |

Aby filtrować według wielu warunków, można użyć **i**:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

lub przekazać wiele **gdzie** elementy pojedynczo po drugiej:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Wartości może mieć różne typy, więc może być konieczne ich do przeprowadzania porównania poprawnego typu rzutowania. Na przykład SecurityEvent *poziom* kolumna nie jest typu String, więc należy rzutować go do typu numerycznego takich jak *int* lub *długie*, zanim można używać operatorów wartości liczbowych na nim: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Określ zakres czasu

### <a name="time-picker"></a>Selektor godziny
Selektor czasu jest obok przycisku Uruchom i wskazuje, że firma Microsoft zadajesz zapytanie tylko rekordy z ostatnich 24 godzin. Jest to domyślny zakres czasu stosowane do wszystkich zapytań. Aby uzyskać tylko rekordy z ostatniej godziny, wybierz pozycję _Ostatnia godzina_ i ponownie uruchom zapytanie.

![Selektor czasu](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtr czasu w zapytaniu
Można także zdefiniować zakres czasu, dodając filtr czasu do zapytania. Warto umieścić filtr czasu natychmiast po Nazwa tabeli: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

W powyższym filtr czasu `ago(30m)` oznacza "30 minut temu", dlatego to zapytanie zwraca tylko rekordy z ostatnich 30 minut. Inne jednostki czasu obejmują dni (2d) (25m) w ciągu minut i sekund (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Projektów i rozszerzanie: Wybierz, a kolumny obliczeniowe
Użyj **projektu** aby wybrać określone kolumny, które mają zostać objęte wyniki:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Poprzedni przykład generuje następujące dane wyjściowe:

![Wyniki projektu zapytania](media/get-started-queries/project.png)

Można również użyć **projektu** zmiana nazw kolumn i definiowania nowych. W poniższym przykładzie użyto projektu, wykonaj następujące czynności:

* Wybierz tylko *komputera* i *TimeGenerated* oryginalne kolumny.
* Zmień nazwę *działania* kolumny *EventDetails*.
* Utwórz nową kolumnę o nazwie *EventCode*. **Substring()** funkcja jest używana do pobierania tylko pierwsze cztery znaki z pola działania.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**Rozszerzanie** zachowuje wszystkie kolumny oryginalnego zestawu wyników i definiuje także dodatkowe. Następujące zapytanie używa **rozszerzyć** dodać *EventCode* kolumny. Należy pamiętać, że w tej kolumnie mogą nie wyświetlać na końcu wyników tabeli w takim przypadku możesz należałoby rozwiń szczegóły rekordu, aby go wyświetlić.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Podsumowując: agregacji grup wierszy
Użyj **Podsumuj** do identyfikowania grup rekordów, zgodnie z co najmniej jedną kolumnę agregacji ich dotyczą. Najbardziej powszechnym zastosowaniem programu **Podsumuj** jest *liczba*, która zwraca liczbę wyników w każdej grupie.

Następujące zapytanie sprawdza wszystkie *wydajności* grup rekordów z ostatniej godziny, ich według *ObjectName*i zlicza rekordy w każdej grupie: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Czasami warto zdefiniować grupy przez wielu wymiarów. Każda unikatowa kombinacja tych wartości definiuje osobnej grupy:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Innym typowym zastosowaniem jest wykonywanie obliczeń matematycznych lub statystycznych w każdej grupie. Na przykład, następujące oblicza średnią *CounterValue* dla każdego komputera:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Niestety wyniki tego zapytania są bez znaczenia, ponieważ firma Microsoft łączyć ze sobą różnych liczników wydajności. Aby wprowadzić bardziej opisową, należy obliczyć średnią osobno dla każdej kombinacji *CounterName* i *komputera*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Sumuj według kolumny godziny
Grupowanie wyników można również opierać się na kolumnę czasu lub wartości ciągłej w innym. Po prostu podsumowywanie `by TimeGenerated` jednak utworzyć grupy dla każdego pojedynczego milisekund w przedziale czasu, ponieważ są unikatowe wartości. 

Aby utworzyć grupy na podstawie wartości ciągłej, najlepiej podziału zakresu w jednostki zarządzane przy użyciu **bin**. Następujące zapytanie analizuje *wydajności* rekordy, które mierzą wolnej pamięci (*dostępnej ilości megabajtów*) na określonym komputerze. Obliczania średniej wartości każdego okresu 1 godzinę w ciągu ostatnich 7 dni:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Aby dane wyjściowe bardziej zrozumiały, wybierz wyświetlania go jako wykres czasu, przedstawiając ilość dostępnej pamięci w czasie:

![Zapytanie pamięci wraz z upływem czasu](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Pisanie zapytań wyszukiwania](search-queries.md)
