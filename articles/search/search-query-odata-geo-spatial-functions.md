---
title: Odwołanie do funkcji geoprzestrzennych OData
titleSuffix: Azure Cognitive Search
description: Składnia i dokumentacja referencyjna do korzystania z funkcji geoprzestrzennych OData, geo.distance i geo.intersects w zapytaniach usługi Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113164"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Funkcje geoprzestrzenne OData `geo.distance` w usłudze Azure Cognitive Search — i`geo.intersects`

Usługa Azure Cognitive Search obsługuje zapytania geoprzestrzenne `geo.distance` w `geo.intersects` [wyrażeniach filtru OData](query-odata-filter-orderby-syntax.md) za pośrednictwem funkcji i funkcji. Funkcja `geo.distance` zwraca odległość w kilometrach między dwoma punktami, z jednej jest zmienną pola lub zakresu, a druga jest stałą przekazywana jako część filtru. Funkcja `geo.intersects` zwraca, `true` jeśli dany punkt znajduje się w obrębie danego wielokąta, gdzie punkt jest zmienną pola lub zakresu, a wielokąt jest określony jako stała przekazywana jako część filtru.

Funkcja `geo.distance` może być również używana w [parametrze **$orderby** ](search-query-odata-orderby.md) do sortowania wyników wyszukiwania według odległości od danego punktu. Składnia `geo.distance` **w $orderby** jest taka sama jak w **$filter**. W `geo.distance` przypadku **używania**w $orderby pole, do którego ma `Edm.GeographyPoint` zastosowanie, musi być typu i musi być również **sortowalne.**

> [!NOTE]
> W `geo.distance` przypadku używania w **parametrze $orderby** pole przekazywanie do funkcji musi zawierać tylko jeden punkt geograficzny. Innymi słowy, musi być `Edm.GeographyPoint` typu, `Collection(Edm.GeographyPoint)`a nie . Nie można sortować pól kolekcji w usłudze Azure Cognitive Search.

## <a name="syntax"></a>Składnia

Następujący EBNF[(Rozszerzony formularz Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)definiuje `geo.distance` gramatykę i `geo.intersects` funkcje, a także wartości geoprzestrzenne, na których działają:

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

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

### <a name="geodistance"></a>geo.distance (odległość)

Funkcja `geo.distance` przyjmuje dwa parametry `Edm.GeographyPoint` typu `Edm.Double` i zwraca wartość, która jest odległość między nimi w kilometrach. Różni się to od innych usług, które obsługują operacje geoprzestrzenne OData, które zazwyczaj zwracają odległości w metrach.

Jednym z parametrów `geo.distance` musi być stała punktu geografii, a drugi musi być ścieżką pola (lub zmienną zakresu `Collection(Edm.GeographyPoint)`w przypadku filtru iteracji nad polem typu). Kolejność tych parametrów nie ma znaczenia.

Stała punktu geografii jest `geography'POINT(<longitude> <latitude>)'`postacią , gdzie długość i szerokość geograficzna są stałymi liczbowymi.

> [!NOTE]
> Podczas `geo.distance` korzystania z filtru należy porównać odległość zwróconą `lt`przez `le` `gt`funkcję `ge`ze stałą za pomocą , , lub . Operatory `eq` `ne` i nie są obsługiwane podczas porównywania odległości. Na przykład jest to `geo.distance`prawidłowe `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`użycie : .

### <a name="geointersects"></a>geo.intersects (przecinaje)

Funkcja `geo.intersects` przyjmuje zmienną `Edm.GeographyPoint` typu i `Edm.GeographyPolygon` stałą `Edm.Boolean`  --  `true` i zwraca, jeśli punkt znajduje się `false` w granicach wielokąta, w przeciwnym razie.

Wielokąt jest dwuwymiarową powierzchnią przechowywaną jako sekwencja punktów definiujących pierścień ograniczający (patrz [przykłady](#examples) poniżej). Wielokąt musi być zamknięty, co oznacza, że pierwszy i ostatni zestaw punktów muszą być takie same. [Punkty wielokąta muszą być w porządku przeciwnym do ruchu wskazówek zegara.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Zapytania geoprzestrzenne i wielokąty obejmujące 180 południk

W przypadku wielu bibliotek zapytań geoprzestrzennych formułowanie kwerendy zawierającej 180 południka (w pobliżu linii daty) jest wyłączone lub wymaga obejścia, takiego jak dzielenie wielokąta na dwa, po jednej po obu stronach południka.

W usłudze Azure Cognitive Search zapytania geoprzestrzenne, które zawierają długość geograficzną 180 stopni, będą działać zgodnie z oczekiwaniami, jeśli kształt zapytania `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`jest prostokątny, a współrzędne są wyrównane do układu siatki wzdłuż długości i szerokości geograficznej (na przykład ). W przeciwnym razie w przypadku kształtów nieproszonych lub niewyrównanych należy wziąć pod uwagę podejście wielokąta podziału.  

### <a name="geo-spatial-functions-and-null"></a>Funkcje geoprzestrzenne i`null`

Podobnie jak wszystkie inne pola niezbierania w `Edm.GeographyPoint` usłudze Azure Cognitive Search, pola typu mogą zawierać `null` wartości. Gdy usługa Azure `geo.intersects` Cognitive Search oceni `null`pole, które `false`jest, zawsze będzie wynikiem. Zachowanie `geo.distance` w tym przypadku zależy od kontekstu:

- W filtrach `geo.distance` `null` pole powoduje `null`. Oznacza to, że dokument `null` nie będzie zgodny, ponieważ `false`w porównaniu do dowolnej wartości innej niż null ocenia .
- Podczas sortowania **$orderby**wyników przy użyciu `geo.distance` $orderby `null` , pola powoduje maksymalną możliwą odległość. Dokumenty z takim polem będą sortowane niżej niż wszystkie inne, gdy używany jest `asc` `desc`kierunek sortowania (domyślnie) i wyższy niż wszystkie inne, gdy kierunek jest .

## <a name="examples"></a>Przykłady

### <a name="filter-examples"></a>Przykłady filtrów

Znajdź wszystkie hotele w odległości 10 kilometrów od danego `Edm.GeographyPoint`punktu odniesienia (gdzie lokalizacja jest polem typu):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Znajdź wszystkie hotele w obrębie danej rzutni opisanej jako wielokąt (gdzie lokalizacja jest polem typu). `Edm.GeographyPoint` Należy zauważyć, że wielokąt jest zamknięty (pierwszy i ostatni zestaw punktów muszą być takie same), a [punkty muszą być wymienione w kolejności przeciwnej do ruchu wskazówek zegara](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Przykłady zamówień

Sortuj hotele schodzące przez `rating`, a następnie wznoszące się w odległości od podanych współrzędnych:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Sortuj hotele `search.score` w `rating`porządku malejącym według i , a następnie w kolejności rosnącej według odległości od podanych współrzędnych, tak aby między dwoma hotelami o identycznych ocenach, najbliższy jest wymieniony jako pierwszy:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Następne kroki  

- [Filtry w usłudze Azure Cognitive Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
