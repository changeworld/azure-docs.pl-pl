---
title: Odwołanie do funkcji geoprzestrzenne OData — usługa Azure Search
description: OData funkcje geoprzestrzenne, geo.distance i geo.intersects w zapytaniach usługi Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079797"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>Funkcje geoprzestrzenne OData w usłudze Azure Search - `geo.distance` i `geo.intersects`

Usługa Azure Search obsługuje zapytania geoprzestrzenne w [wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) za pośrednictwem `geo.distance` i `geo.intersects` funkcji. `geo.distance` Funkcja zwraca odległość w kilometrach między dwoma punktami, jednego pola lub zmiennej zakresu i stała jest przekazywany jako część filtru. `geo.intersects` Funkcja zwraca `true` Jeśli dany punkt znajduje się w danym wielokąta, w której punkt jest zmienną pola lub zakresu i Wielokąt jest określony jako stała przekazanych jako część filtru.

`geo.distance` Funkcji można używać w [ **$orderby** parametru](search-query-odata-orderby.md) sortowanie wyników wyszukiwania przez odległość od danego punktu. Składnia `geo.distance` w **$orderby** jest taka sama jak w **$filter**. Korzystając z `geo.distance` w **$orderby**, pole, którego dotyczy musi być typu `Edm.GeographyPoint` i musi być również **sortowanie**.

## <a name="syntax"></a>Składnia

Następujące EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatyki `geo.distance` i `geo.intersects` geoprzestrzenne wartości, na których działają, a także funkcje:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

### <a name="geodistance"></a>geo.distance

`geo.distance` Funkcja przyjmuje dwa parametry typu `Edm.GeographyPoint` i zwraca `Edm.Double` wartość, która jest odległość między nimi w kilometrach. To różni się od innych usług, które obsługują geoprzestrzenne operacje usługi OData, które zwracają zazwyczaj odległości liczniki.

Jeden z parametrów do `geo.distance` musi być stałą punkt lokalizacji geograficznej i innych musi być ścieżką pola (lub zmienną zakresu, w przypadku filtr Iterowanie pola typu `Collection(Edm.GeographyPoint)`). Kolejność tych parametrów nie ma znaczenia.

Stała punkt lokalizacji geograficznej ma postać `geography'POINT(<longitude> <latitude>)'`, której długość i szerokość geograficzną stałych numerycznych.

> [!NOTE]
> Korzystając z `geo.distance` w filtrze, możesz porównać odległość zwrócona przez funkcję przy użyciu stałej `lt`, `le`, `gt`, lub `ge`. Operatory `eq` i `ne` nie są obsługiwane podczas porównywania odległości. Na przykład, jest to poprawne użycie `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>Geo.intersects

`geo.intersects` Funkcja przyjmuje zmienną typu `Edm.GeographyPoint` i stałą `Edm.GeographyPolygon` i zwraca `Edm.Boolean`  --  `true` Jeśli punkt znajduje się w granicach wielokąta `false` inaczej.

Wielokąt jest dwuwymiarowy powierzchni przechowywany jako sekwencja punktów Definiowanie otaczający pierścienia (zobacz [przykłady](#examples) poniżej). Wielokąt musi zostać zamknięty, czyli pierwszy i ostatni punkt zestawów musi być taka sama. [Punkty wielokąta musi znajdować się w kolejności do ruchu wskazówek zegara](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Zapytania geoprzestrzenne i wielokątów obejmujące 180th południków

Dla zapytania geoprzestrzenne wiele bibliotek formułowania kwerendę, która obejmuje 180th południków (w pobliżu linii zmiany) jest off-limits lub wymaga obejście tego problemu, takie jak podział wielokąta na dwa, jeden po obu stronach południka.

W usłudze Azure Search, zapytania geoprzestrzenne, obejmujących 180 stopni długości geograficznej będzie działać zgodnie z oczekiwaniami, jeśli kształt zapytania jest prostokątnym zestawem i Twoje współrzędne Wyrównaj do układu siatki wzdłuż długości i szerokości geograficznej (na przykład `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). W przeciwnym razie w przypadku innych niż prostokątne lub niewyrównanych kształtów, należy wziąć pod uwagę podejście split wielokąta.  

### <a name="geo-spatial-functions-and-null"></a>Funkcje geoprzestrzenne i `null`

Jak wszystkich innych pól niż kolekcji w usłudze Azure Search pola typu `Edm.GeographyPoint` może zawierać `null` wartości. Gdy usługa Azure Search szacują `geo.intersects` dla pola, które jest `null`, wynikiem zawsze będzie `false`. Zachowanie `geo.distance` w tym przypadku zależy od kontekstu:

- W filtrach `geo.distance` z `null` wyniki w polu `null`. Oznacza to, ponieważ dokument nie będą zgodne `null` w porównaniu do żadnych innych niż null wartość będącą wynikiem oceny `false`.
- Podczas sortowania wyników za pomocą **$orderby**, `geo.distance` z `null` pól wyników w możliwie największy odstęp. Mniejsza od wszystkich innych sortowania dokumentów za pomocą takiego pola podczas kierunek sortowania `asc` jest używana (ustawienie domyślne) i jest wyższy niż wszystkie inne, gdy jest kierunek `desc`.

## <a name="examples"></a>Przykłady

### <a name="filter-examples"></a>Przykłady filtrów

Znajdź wszystkie hoteli w ciągu 10 km od punktu danego odwołania (gdzie lokalizacji jest polem typu `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Znajdź wszystkie hotele, w ramach danego okienka ekranu, określane jako wielokąta (gdzie lokalizacji jest polem typu `Edm.GeographyPoint`). Należy pamiętać, że Wielokąt jest zamknięty (zestawy pierwszy i ostatni punkt muszą być takie same) i [punkty muszą być wymienione w kolejności do ruchu wskazówek zegara](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Przykłady klauzuli ORDER by

Sortuj malejąco według hotele `rating`, następnie rosnącej według odległości od danej współrzędnych:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Sortowanie hotele malejąco według `search.score` i `rating`, a następnie w polu rosnąco przez odległości od danej współrzędne tak, aby między dwa hotele z ocenami identyczne, znajdującego się najbliżej jest wymienione jako pierwsze:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Kolejne kroki  

- [Filtry w usłudze Azure Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
