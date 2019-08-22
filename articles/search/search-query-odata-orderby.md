---
title: Zamówienie OData — według odwołania — Azure Search
description: Dokumentacja języka OData dla składni order-by w zapytaniach Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 8ee44549931100a1affa5e2bb9e5cda904c05ed1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647536"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Składnia $orderby OData w Azure Search

 Można użyć [parametru **$OrderBy** OData](query-odata-filter-orderby-syntax.md) , aby zastosować niestandardową kolejność sortowania dla wyników wyszukiwania w Azure Search. W tym artykule opisano szczegółowo składnię **$OrderBy** . Aby uzyskać ogólne informacje na temat używania **$OrderBy** podczas prezentowania wyników wyszukiwania, zobacz jak korzystać [z wyników wyszukiwania w Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Składnia

Parametr **$OrderBy** akceptuje listę rozdzielonych przecinkami, do 32 **klauzul ORDER-by**. Składnia klauzuli Order-by została opisana przez następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Każda klauzula ma kryteria sortowania, opcjonalnie po której następuje kierunek sortowania (`asc` w przypadku rosnącej `desc` lub malejącej). Jeśli nie określisz kierunku, wartość domyślna to Ascending. Kryterium sortowania może być ścieżką `sortable` pola lub wywołaniem [`geo.distance`](search-query-odata-geo-spatial-functions.md) lub [`search.score`](search-query-odata-search-score-function.md) funkcjami.

Jeśli wiele dokumentów ma takie same kryteria sortowania i `search.score` funkcja nie jest używana (na przykład w przypadku sortowania według pola liczbowego `Rating` , a trzy wszystkie dokumenty mają klasyfikację 4), powiązania zostaną przerwane według wyniku dokumentu w kolejności malejącej. Gdy wyniki dokumentu są takie same (na przykład w przypadku braku zapytania wyszukiwania pełnotekstowego określonego w żądaniu), względna kolejność dokumentów jest nieokreślona.

Można określić wiele kryteriów sortowania. Kolejność wyrażeń określa ostateczną kolejność sortowania. Na przykład, aby sortować Malejąco według wyniku, po którym następuje klasyfikacja, składnia byłaby `$orderby=search.score() desc,Rating desc`następująca.

Składnia dla `geo.distance` w **$OrderBy** jest taka sama jak w **$Filter**. W przypadku `geo.distance` używania w **$OrderBy**pole, do którego ma zastosowanie, musi być typu `Edm.GeographyPoint` i musi być `sortable`również.

Składnia dla `search.score` w **$OrderBy** ma wartość `search.score()`. Funkcja `search.score` nie przyjmuje żadnych parametrów.

## <a name="examples"></a>Przykłady

Sortuj Hotele rosnąco według stawki bazowej:

    $orderby=BaseRate asc

Sortuj Hotele Malejąco według klasyfikacji, a następnie rosnąco według stawki bazowej (należy pamiętać, że rosnąco jest wartością domyślną):

    $orderby=Rating desc,BaseRate

Sortuj Hotele Malejąco według klasyfikacji, a następnie rosnąco według odległości od danego współrzędnych:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Sortuj Hotele w kolejności malejącej według wyszukiwania. Ocena i ocena, a następnie w kolejności rosnącej według odległości od danego współrzędnych. Między dwiema hoteli z identycznymi wynikami i ocenami przydatności poniżej znajduje się pierwsza z nich:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Następne kroki  

- [Jak korzystać z wyników wyszukiwania w Azure Search](search-pagination-page-layout.md)
- [Przegląd języka wyrażenia OData dla Azure Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;Azure Search interfejs API REST usługi&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
