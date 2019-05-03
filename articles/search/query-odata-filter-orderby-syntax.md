---
title: Składnia wyrażenia OData, filtry i klauzuli order by klauzule — usługa Azure Search
description: Filtr klauzuli order by wyrażenia i składnia OData dla zapytań usługi Azure Search.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: b1f77a9e0a3308098e5f6c699f2fc79e5c437f17
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024259"
---
# <a name="odata-expression-syntax-for-filters-and-order-by-clauses-in-azure-search"></a>Składnia wyrażenia OData, filtry i klauzule w klauzuli order by w usłudze Azure Search

Usługa Azure Search obsługuje podzbiór składnia wyrażenia OData **$filter** i **$orderby** wyrażenia. Wyrażenia filtru są oceniane podczas zapytania analizy, ograniczając wyszukiwanie określonych pól lub dodawanie kryteriów dopasowania używane podczas skanowania indeksu. Wyrażenia order by są stosowane jako krok przetwarzania końcowego za pośrednictwem zestawu wyników. Filtry i wyrażenia w klauzuli order by znajdują się w żądaniu zapytania, przestrzega składnia OData, niezależnie od [proste](query-simple-syntax.md) lub [pełne](query-lucene-syntax.md) zapytania składnią używaną w **wyszukiwania** parametr. Ten artykuł zawiera dokumentację referencyjną wyrażenia OData, używane w wyrażeniach sortowania i filtrów.

## <a name="filter-syntax"></a>Składnia filtru

A **$filter** wyrażenia można wykonać autonomiczny jako w pełni zaakceptowania kwerendy lub Uściślij zapytanie, które ma dodatkowe parametry. Poniższe przykłady ilustrują kilka kluczowych scenariuszy. W pierwszym przykładzie filtr jest treść zapytania.


```POST
POST /indexes/hotels/docs/search?api-version=2019-05-06
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }
```

Inny typowy przypadek użycia jest tworzenie aspektów filtrów w połączeniu, gdzie filtr zmniejsza prawdopodobieństwo kwerendy na podstawie wybranych nawigacji reguł opartych na użytkownika:

```POST
POST /indexes/hotels/docs/search?api-version=2019-05-06
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }
```

### <a name="filter-operators"></a>Operatory filtru  

- Operatory logiczne (i, czy nie).  

- Wyrażeniach porównania (`eq, ne, gt, lt, ge, le`). W porównaniach ciągów jest rozróżniana wielkość liter.  

- Stałe obsługiwane [modelu Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) typów (EDM) (zobacz [obsługiwanych typów danych &#40;usługi Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Aby uzyskać listę obsługiwanych typów). Stałe typów kolekcji nie są obsługiwane.  

- Odwołania do nazw pól. Tylko `filterable` pola mogą być używane w wyrażeniach filtru.  

- `any` bez parametrów. To sprawdzenie, czy pole typu `Collection(Edm.String)` zawiera wszystkie elementy.  

- `any` i `all` z obsługą wyrażenia lambda ograniczone. 
    
  -   `any/all` są obsługiwane na polach typu `Collection(Edm.String)`. 
    
  -   `any` można używać tylko wyrażeń proste równości lub z `search.in` funkcji. Proste wyrażenia składają się z porównanie jednego pola, a wartość literału, np. `Title eq 'Magna Carta'`.
    
  -   `all` można używać tylko wyrażeń nierówności proste lub z `not search.in`.   

- Funkcje Geoprzestrzenne `geo.distance` i `geo.intersects`. `geo.distance` Funkcja zwraca odległość w kilometrach między dwoma punktami, jednego pola, a drugi jest stałą przekazywanej jako część filtru. `geo.intersects` Funkcja zwraca wartość true, jeśli dany punkt znajduje się w danym wielokąta, w którym punkt znajduje się pole i Wielokąt jest określony jako stała przekazanych jako część filtru.  

  Wielokąt jest Powierzchnia dwuwymiarowa przechowywany jako sekwencja punktów Definiowanie blokujących cyklicznego (Zobacz przykład poniżej). Wielokąt musi zostać zamknięty, czyli pierwszy i ostatni punkt zestawów musi być taka sama. [Punkty wielokąta musi znajdować się w kolejności do ruchu wskazówek zegara](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

  `geo.distance` Zwraca odległość w kilometrach w usłudze Azure Search. To różni się od innych usług, które obsługują operacje geoprzestrzenne OData, które zwracają zazwyczaj odległości liczniki.  

  > [!NOTE]  
  >  Korzystając z geo.distance w filtrze, możesz porównać odległość zwrócona przez funkcję przy użyciu stałej `lt`, `le`, `gt`, lub `ge`. Operatory `eq` i `ne` nie są obsługiwane podczas porównywania odległości. Na przykład, jest to poprawne użycie geo.distance: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.  

- `search.in` Funkcja sprawdza, czy dany ciąg znaków jest równa jeden z danej listy wartości. Jego można również w dowolnych lub wszystkich porównać jedną wartość pola kolekcji ciągu z danej listy wartości. Równość pola i każdej wartości na liście jest określana w czasie wielkość liter, taki sam sposób jak w przypadku `eq` operatora. W związku z tym, takie jak wyrażenie `search.in(myfield, 'a, b, c')` jest odpowiednikiem `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, chyba że `search.in` przyniesie znacznie lepszej wydajności. 

   Pierwszy parametr `search.in` funkcja jest odwołanie do pola ciągu (lub zmiennej zakresu nad polem ciągu kolekcji, w przypadku których `search.in` jest używana wewnątrz `any` lub `all` wyrażenia). 
  
   Drugi parametr jest ciąg zawierający listę wartości rozdzielonych spacjami i/lub przecinkami. 
  
   Trzeci parametr jest ciągiem, gdzie każdy znak w ciągu lub podzbiór ten ciąg jest traktowany jako separator podczas analizowania listy wartości w drugim parametrze. Jeśli musisz użyć separatory niż spacje i przecinki, ponieważ wartości obejmują te znaki, które można określić opcjonalny trzeci parametr `search.in`. 

  > [!NOTE]   
  > Niektóre scenariusze wymagają porównanie pola na dużej liczbie wartości stałych. Na przykład implementacji dostosowanie do zabezpieczeń przy użyciu filtrów może wymagać porównanie pola Identyfikator dokumentu z listą identyfikatorów, do których użytkownik zgłaszający żądanie ma uprawnienia odczytu. W scenariuszach, takich jak to zdecydowanie zaleca się przy użyciu `search.in` zamiast bardziej skomplikowanych rozłączenia wyrażeń równości. Na przykład użyć `search.in(Id, '123, 456, ...')` zamiast `Id eq 123 or Id eq 456 or ....`. 
  >
  > Jeśli używasz `search.in`, można oczekiwać, że gdy drugi parametr zawiera listę setek lub tysięcy wartości czas odpowiedzi sekundy. Należy pamiętać, że nie ma limitu jawne na liczbę elementów, które można przekazać do `search.in`, chociaż można nadal obowiązują ograniczenia rozmiaru maksymalnego żądania. Jednak opóźnienie rośnie wraz ze wzrostem natężenia liczbę wartości.

- `search.ismatch` Funkcja daje w wyniku zapytania wyszukiwania jako część wyrażenia filtru. Dokumenty, które odpowiada zapytaniu wyszukiwania zostaną zwrócone w zestawie wyników. Dostępne są następujące przeciążenia tej funkcji:
  - `search.ismatch(search)`
  - `search.ismatch(search, searchFields)`
  - `search.ismatch(search, searchFields, queryType, searchMode)`

  gdzie: 
  
  - `search`: zapytanie wyszukiwania (albo [proste](query-simple-syntax.md) lub [pełne](query-lucene-syntax.md) składni zapytania). 
  - `queryType`: "prosta" lub "pełnej", wartość domyślna to "simple". Określa, jakiego języka zapytania użyto w `search` parametru.
  - `searchFields`: rozdzielana przecinkami lista pola z możliwością wyszukiwania do przeszukania, wartość domyślna to wszystkie pola z możliwością wyszukiwania w indeksie.    
  - `searchMode`: "wszystkie" lub "wszystkie", wartość domyślna to "dowolne". Wskazuje, czy dowolne lub wszystkie terminy wyszukiwania muszą się zgadzać aby można było policzyć dokumentu jako dopasowanie.

  Powyższych parametrów są równoważne do odpowiednich [wyszukiwania parametry żądania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

- `search.ismatchscoring` Działanie, takie jak `search.ismatch` funkcji, zwraca wartość true, dokumentów, pasujących do zapytania wyszukiwania przekazany jako parametr. Różnica między nimi jest, czy wskaźnik istotności wyników pasujących dokumentów `search.ismatchscoring` zapytania przyczyni się do ogólny wynik dokumentu znajduje się w przypadku `search.ismatch`, nie będzie można zmienić na wynik dokumentu. Następujące przeciążenia tej funkcji są dostępne z parametrami identycznych z właściwościami `search.ismatch`:
  - `search.ismatchscoring(search)`
  - `search.ismatchscoring(search, searchFields)`
  - `search.ismatchscoring(search, searchFields, queryType, searchMode)`

  `search.ismatch` i `search.ismatchscoring` funkcje są w pełni prostopadły sobą i pozostałe algebry filtru. Oznacza to, że obie funkcje mogą być używane w tym samym wyrażeniu filtru. 

### <a name="geospatial-queries-and-polygons-spanning-the-180th-meridian"></a>Zapytania Geoprzestrzenne i wielokątów obejmujące 180th południków  
 Dla wielu zapytania geoprzestrzenne bibliotek formułowania kwerendę, która obejmuje 180th południków (w pobliżu linii zmiany) jest off-limits lub wymaga obejście tego problemu, takie jak podział wielokąta na dwa, jeden po obu stronach południka.  

 W usłudze Azure Search, zapytania geoprzestrzenne, zawierające 180 stopni długości geograficznej będzie działać zgodnie z oczekiwaniami, jeśli kształt zapytania jest prostokątnym zestawem i Twoje współrzędne Wyrównaj do układu siatki wzdłuż długości i szerokości geograficznej (na przykład `geo.intersects(location, geography'POLYGON((179 65,179 66,-179 66,-179 65,179 65))'`). W przeciwnym razie w przypadku innych niż prostokątne lub niewyrównanych kształtów, należy wziąć pod uwagę podejście split wielokąta.  

<a name="bkmk_limits"></a>

## <a name="filter-size-limitations"></a>Ograniczenia rozmiaru filtru 

 Istnieją limity rozmiaru i złożoności wyrażenia filtru, których można wysłać do usługi Azure Search. Limity są mniej więcej zależy od liczby klauzul w wyrażeniu filtru. Regułą jest, że w przypadku setek klauzule jest ryzyka limit. Firma Microsoft zaleca, projektowania aplikacji w taki sposób, czy nie generuje filtry niepowiązane rozmiar.  


## <a name="filter-examples"></a>Przykłady filtrów  

 Znajdź wszystkie hotele ze stawką podstawową mniejsza niż 200 USD, które są oceniane co najmniej 4:  

```
$filter=baseRate lt 200.0 and rating ge 4
```

 Znajdź wszystkie hotele innych niż "Roach Motel", które zostały renovated od 2010:  

```
$filter=hotelName ne 'Roach Motel' and lastRenovationDate ge 2010-01-01T00:00:00Z
```

 Znajdź wszystkie hotele z stawkę za podstawowy mniejsza niż 200 USD, które zostały renovated od 2010 ciągiem datetime literału, który zawiera informacje o strefie czasowej dla pacyficznego czasu standardowego:  

```
$filter=baseRate lt 200 and lastRenovationDate ge 2010-01-01T00:00:00-08:00
```

 Znajdź wszystkie hotele, które parkowania uwzględniona i nie zezwalają na palenia:  

```
$filter=parkingIncluded and not smokingAllowed
```

 \- LUB —  

```
$filter=parkingIncluded eq true and smokingAllowed eq false
```

 Znajdź wszystkie hotele, które mają luksusowe lub obejmują parkowania ocena, 5:  

```
$filter=(category eq 'Luxury' or parkingIncluded eq true) and rating eq 5
```

 Znajdź wszystkie hotele z tagiem "Wi-Fi" (gdzie każdy hotelu tagami przechowywana w polu Collection(Edm.String)):  

```
$filter=tags/any(t: t eq 'wifi')
```

 Znajdź wszystkie hotele bez tagu "motel":  

```
$filter=tags/all(t: t ne 'motel')
```

 Znajdź wszystkie hotele z żadnych znaczników:  

```
$filter=tags/any()
```

Znajdź wszystkie hotele, które nie mają tagi:  

```
$filter=not tags/any()
```


 Znajdź wszystkie hoteli w ciągu 10 km od danego odwołania punktu (gdzie lokalizacji jest polem typu Edm.GeographyPoint):  

```
$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

 Znajdź wszystkie hotele, w ramach danego okienka ekranu, określane jako wielokąta (gdzie lokalizacji jest polem typu Edm.GeographyPoint). Należy pamiętać, że Wielokąt jest zamknięty (zestawy pierwszy i ostatni punkt muszą być takie same) i [punkty muszą być wymienione w kolejności do ruchu wskazówek zegara](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

```
$filter=geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

 Znajdź wszystkie hotele, które albo nie mają żadnej wartości w polu "description" lub wartość jest jawnie ustawiona na wartość null:  

```
$filter=description eq null
```

Znajdź wszystkie hotele o nazwie równa "motel Roach" lub "Budżetu hotel"). Zwroty zawierają spacje, który jest domyślnym ogranicznikiem. Możesz specicfy alternatywnych ogranicznika w pojedynczym cudzysłowie jako trzeci parametr ciągu:  

```
$filter=search.in(name, 'Roach motel,Budget hotel', ',')
```

Znajdź wszystkie hotele o nazwie równa albo Roach motel "lub"Budżetu hotel"oddzielone" | "):  

```
$filter=search.in(name, 'Roach motel|Budget hotel', '|')
```

Znajdź wszystkie hotele za pomocą znacznika "sieć Wi-Fi" lub "puli":  

```
$filter=tags/any(t: search.in(t, 'wifi, pool'))
```

Znajdź dopasowanie fraz w obrębie kolekcji, takie jak "podgrzewanego ręczników stojakami" lub uwzględnione hairdryer w znacznikach. 

```
$filter=tags/any(t: search.in(t, 'heated towel racks,hairdryer included', ','))
```

Znajdź wszystkie hotele bez tagu "motel" ani "podręcznego":  

```
$filter=tags/all(t: not search.in(t, 'motel, cabin'))
```

Znajdowanie dokumentów z wyrazem "waterfront". To zapytanie filtru jest taka sama jak [żądania wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) z `search=waterfront`.

```
$filter=search.ismatchscoring('waterfront')
```

Znajdź dokumenty zawierające słowo "hostel" i ocena większa lub równa 4 czy dokumenty zawierające słowo "motel" i ocena równy 5. Uwaga: to żądanie nie może być wyrażona bez `search.ismatchscoring` funkcji.

```
$filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Znajdź dokumenty bez słowa "luksusowe".

```
$filter=not search.ismatch('luxury') 
```

Znajdź dokumenty zawierające frazę "view ocean" lub klasyfikacji równa 5. `search.ismatchscoring` Kwerenda zostanie wykonana tylko względem pól `hotelName` i `description`.
Należy pamiętać, dokumenty, pasujących do drugiej klauzuli rozłączenia zostaną zwrócone zbyt — hotele z oceną równa 5. Aby było jasne dokumentów, które nie pasuje do żadnego z ocenami elementy wyrażenia, zostanie zwrócony wynik jest równy zero.

```
$filter=search.ismatchscoring('"ocean view"', 'description,hotelName') or rating eq 5
```

Znajdź dokumentów, których warunki "hotel" i "Kuwejcie" w ciągu 5 słów od siebie nawzajem w opisie hotelu i którym palenia nie jest dozwolone. To zapytanie używa [pełny język zapytań Lucene](query-lucene-syntax.md).

```
$filter=search.ismatch('"hotel airport"~5', 'description', 'full', 'any') and not smokingAllowed 
```

## <a name="order-by-syntax"></a>Składni w klauzuli ORDER by

**$Orderby** parametru akceptuje rozdzielaną przecinkami listę maksymalnie 32 wyrażeń formularza `sort-criteria [asc|desc]`. Kryteria sortowania mogą być nazwa `sortable` pola lub wywołania `geo.distance` lub `search.score` funkcji. Można użyć dowolnego `asc` lub `desc` jawnie określić kolejność sortowania. Domyślna kolejność jest rosnąca.

Jeśli wiele dokumentów ma takich samych kryteriów sortowania i `search.score` funkcja nie jest używana (na przykład, jeśli sortujesz według liczbową `rating` pola i trzy dokumenty wszystkie mają klasyfikację 4), ties będą uszkodzone wynik dokumentu, w kolejności malejącej. Gdy dokument wyniki są takie same (na przykład, gdy nie ma wyszukiwania pełnotekstowego zapytania określony w żądaniu), następnie względne uporządkowanie wiązanej dokumentów jest nieokreślony.
 
Można określić wielu kryteriów sortowania. Kolejność wyrażeń określa kolejność sortowania końcowej. Na przykład, aby posortować malejąco według wyników, a następnie ocenę, składnia będzie `$orderby=search.score() desc,rating desc`.

Składnia `geo.distance` w **$orderby** jest taka sama jak w **$filter**. Korzystając z `geo.distance` w **$orderby**, pole, którego dotyczy musi być typu `Edm.GeographyPoint` i musi być również `sortable`.  

Składnia `search.score` w **$orderby** jest `search.score()`. Funkcja `search.score` nie przyjmuje żadnych parametrów.  
 

## <a name="order-by-examples"></a>Przykłady klauzuli ORDER by

Sortuj hotele rosnącej według stawkę za podstawowy:

```
$orderby=baseRate asc
```

Sortowanie hotele malejąco według klasyfikacji, a następnie rosnącej według stawki podstawowej (należy pamiętać, że rosnąca jest ustawieniem domyślnym):

```
$orderby=rating desc,baseRate
```

Sortuj malejąco według klasyfikacji, a następnie rosnącej według odległości od danej współrzędne hotele:

```
$orderby=rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

Sortuj hotele malejąco według search.score i klasyfikacji, a następnie rosnąco przez odległości od danej współrzędne tak, aby między dwa hotele z ocenami identyczne, znajdującego się najbliżej jest wymienione jako pierwsze:

```
$orderby=search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```
<a name="bkmk_unsupported"></a>

## <a name="unsupported-odata-syntax"></a>Nieobsługiwana składnia OData

-   Wyrażenia arytmetyczne  

-   Funkcje (z wyjątkiem odległość i przecina funkcje geoprzestrzenne)  

-   `any/all` za pomocą wyrażenia lambda dowolnych  

## <a name="see-also"></a>Zobacz także  

+ [Nawigacja aspektowa w usłudze Azure Search](search-faceted-navigation.md) 
+ [Filtry w usłudze Azure Search](search-filters.md) 
+ [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Składnia zapytań Lucene](query-lucene-syntax.md)
+ [Prosta składnia zapytań w usłudze Azure Search](query-simple-syntax.md)   
