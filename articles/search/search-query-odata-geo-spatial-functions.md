---
title: Odwołanie do funkcji przestrzennej w postaci geograficznej OData
titleSuffix: Azure Cognitive Search
description: Funkcje geoprzestrzenne OData, geograficzne. odległość i geograficznie. przecinają się w zapytaniach usługi Azure Wyszukiwanie poznawcze.
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
ms.openlocfilehash: 09034423e16c652cf6994b38f8d92574abc0ce55
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793338"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Funkcje geoprzestrzenne OData w usłudze Azure Wyszukiwanie poznawcze — `geo.distance` i `geo.intersects`

Usługa Azure Wyszukiwanie poznawcze obsługuje zapytania przestrzenne geograficzne w [wyrażeniach filtrów OData](query-odata-filter-orderby-syntax.md) za pośrednictwem funkcji `geo.distance` i `geo.intersects`. Funkcja `geo.distance` zwraca odległość w kilometrach między dwoma punktami, jedną lub zmienną pola lub zakres i jedną z nich jest stałą przekazaną w ramach filtra. Funkcja `geo.intersects` zwraca `true`, jeśli dany punkt znajduje się w obrębie danego wielokątu, gdzie punkt jest zmienną pola lub zakresu, a Wielokąt jest określony jako stała przekazaniem jako część filtru.

Funkcji `geo.distance` można także użyć w [parametrze **$OrderBy** ](search-query-odata-orderby.md) do sortowania wyników wyszukiwania według odległości od danego punktu. Składnia `geo.distance` w **$OrderBy** jest taka sama jak w **$Filter**. W przypadku używania `geo.distance` w **$OrderBy**, pole, którego ma dotyczyć, musi być typu `Edm.GeographyPoint` i musi również mieć możliwość **sortowania**.

> [!NOTE]
> W przypadku używania `geo.distance` w parametrze **$OrderBy** pole przekazywane do funkcji musi zawierać tylko jeden punkt geograficzny. Innymi słowy, musi być typu `Edm.GeographyPoint`, a nie `Collection(Edm.GeographyPoint)`. Nie można sortować według pól kolekcji na platformie Azure Wyszukiwanie poznawcze.

## <a name="syntax"></a>Składnia

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę funkcji `geo.distance` i `geo.intersects`, a także wartości przestrzenne, na których działają:

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

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla usługi Azure wyszukiwanie poznawcze](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

### <a name="geodistance"></a>Lokalizacja geograficzna

Funkcja `geo.distance` przyjmuje dwa parametry typu `Edm.GeographyPoint` i zwraca wartość `Edm.Double`, która jest odległości między nimi w kilometrach. Różni się to od innych usług, które obsługują operacje przestrzenne w geograficznie, które zwykle zwracają odległości w licznikach.

Jeden z parametrów do `geo.distance` musi być stałą punktu geograficznego, a druga musi być ścieżką pola (lub zmienną zakresu w przypadku filtrowania filtru dla pola typu `Collection(Edm.GeographyPoint)`). Kolejność tych parametrów nie ma znaczenia.

Stała punktu geograficznego ma postać `geography'POINT(<longitude> <latitude>)'`, gdzie długość geograficzna i Szerokość geograficzna są stałymi liczbowymi.

> [!NOTE]
> W przypadku używania `geo.distance` w filtrze należy porównać odległość zwracaną przez funkcję ze stałą przy użyciu `lt`, `le`, `gt`lub `ge`. Operatory `eq` i `ne` nie są obsługiwane w przypadku porównywania odległości. Na przykład jest to poprawne użycie `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>geograficznie. intersects

Funkcja `geo.intersects` przyjmuje zmienną typu `Edm.GeographyPoint` i stałą `Edm.GeographyPolygon` i zwraca `Edm.Boolean` -- `true`, jeśli punkt znajduje się w granicach wielokąta, `false` w przeciwnym razie.

Wielokąt jest powierzchnią dwuwymiarową przechowywaną jako sekwencja punktów definiujących pierścień ograniczenia (Zobacz poniższe [przykłady](#examples) ). Wielokąt musi być zamknięty, co oznacza, że pierwszy i ostatni zestaw punktów muszą być takie same. [Punkty w wielokąta muszą znajdować się w porządku w lewo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Zapytania przestrzenne geograficznie i wielokąty obejmujące 180th południka

W przypadku wielu geograficznie dowolnych bibliotek zapytań, które zawierają 180th południa (blisko linii zmiany), jest limitów lub wymaga obejścia, takiego jak dzielenie wielokąta na dwie, jeden po obu stronach południka.

W przypadku usługi Azure Wyszukiwanie poznawcze zapytania przestrzenne, które obejmują długość geograficzną o 180 stopni, będą działały zgodnie z oczekiwaniami, jeśli kształt zapytania jest prostokątny, a współrzędne są wyrównane do układu siatki wzdłuż długości i szerokości geograficznej (na przykład `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). W przeciwnym razie dla kształtów nieprostokątnych lub niewyrównanych Rozważmy podejście Split wielokąt.  

### <a name="geo-spatial-functions-and-null"></a>Funkcje przestrzenne i `null`

Podobnie jak w przypadku wszystkich innych pól, które nie są kolekcjami w usłudze Azure Wyszukiwanie poznawcze, pola typu `Edm.GeographyPoint` mogą zawierać wartości `null`. Gdy usługa Azure Wyszukiwanie poznawcze oblicza `geo.intersects` dla pola, które jest `null`, wynik będzie zawsze `false`ny. Zachowanie `geo.distance` w tym przypadku zależy od kontekstu:

- W filtrach `geo.distance` pola `null` wyniki w `null`. Oznacza to, że dokument nie jest zgodny, ponieważ `null` w porównaniu do dowolnej wartości innej niż null zostanie oszacowany `false`.
- Podczas sortowania wyników przy użyciu **$OrderBy**, `geo.distance` pola `null` powoduje maksymalną możliwą odległość. Dokumenty z takimi polami będą sortowane mniej niż wszystkie inne, gdy `asc` jest używany kierunek sortowania (wartość domyślna) i więcej niż wszystkie inne, gdy kierunek jest `desc`.

## <a name="examples"></a>Przykłady

### <a name="filter-examples"></a>Przykłady filtrów

Znajdź wszystkie hotele w promieniu 10 kilometrów danego punktu odwołania (lokalizacja jest polem typu `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Znajdź wszystkie hotele w obrębie danego okienka ekranu opisane jako wielokąt (lokalizacja jest polem typu `Edm.GeographyPoint`). Należy zauważyć, że Wielokąt jest zamknięty (pierwszy i ostatni zestaw punktów musi być taki sam), a [punkty muszą być wymienione w porządku w lewo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Order — przykłady

Sortuj Hotele Malejąco według `rating`, a następnie rosnące według odległości od danego współrzędnych:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Sortuj Hotele w kolejności malejącej według `search.score` i `rating`, a następnie w kolejności rosnącej według odległości od danego współrzędnych, tak aby między dwiema hoteli z identyczną klasyfikacją były najpierw wymienione najbliższe:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Następne kroki  

- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;w interfejsie API REST usługi Azure wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
