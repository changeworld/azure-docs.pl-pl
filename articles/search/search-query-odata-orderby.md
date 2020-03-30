---
title: OData order-by reference
titleSuffix: Azure Cognitive Search
description: Dokumentacja dotycząca składni i języka do korzystania z kolejności w zapytaniach usługi Azure Cognitive Search.
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113146"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Składnia $orderby OData w usłudze Azure Cognitive Search

 [ParametrU OData **$orderby** ](query-odata-filter-orderby-syntax.md) można użyć do zastosowania niestandardowej kolejności sortowania wyników wyszukiwania w usłudze Azure Cognitive Search. W tym artykule opisano szczegółowo składnię **$orderby.** Aby uzyskać bardziej ogólne informacje dotyczące **używania $orderby** podczas prezentowania wyników wyszukiwania, zobacz [Jak pracować z wynikami wyszukiwania w usłudze Azure Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Składnia

Parametr **$orderby** akceptuje listę rozdzielonych przecinkami maksymalnie 32 **klauzul kolejność według**. Składnia klauzuli order-by jest opisana przez następujący EBNF ([Rozszerzony formularz Backus-Naur):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

Każda klauzula ma kryteria sortowania, po`asc` którym opcjonalnie następuje kierunek sortowania (dla rosnących lub `desc` malejących). Jeśli nie określisz kierunku, wartość domyślna jest rosnąca. Kryteria sortowania mogą być ścieżką `sortable` pola lub wywołaniem [`geo.distance`](search-query-odata-geo-spatial-functions.md) [`search.score`](search-query-odata-search-score-function.md) funkcji lub funkcji.

Jeśli wiele dokumentów ma takie `search.score` same kryteria sortowania, a funkcja nie jest `Rating` używana (na przykład, jeśli sortujesz według pola liczbowego, a trzy dokumenty mają ocenę 4), powiązania zostaną podzielone według wyniku dokumentu w porządku malejącym. Gdy wyniki dokumentu są takie same (na przykład, gdy nie ma kwerendy wyszukiwania pełnotekstowego określonego w żądaniu), względna kolejność powiązanych dokumentów jest nieokreślona.

Można określić wiele kryteriów sortowania. Kolejność wyrażeń określa ostateczną kolejność sortowania. Na przykład, aby posortować malejąco według wyniku, `$orderby=search.score() desc,Rating desc`a następnie Ocena, składnią będzie .

Składnia `geo.distance` **w $orderby** jest taka sama jak w **$filter**. W `geo.distance` przypadku **używania**w $orderby pole, do którego ma `Edm.GeographyPoint` zastosowanie, musi `sortable`być typu i musi być również.

Składnia `search.score` w **$orderby** jest `search.score()`. Funkcja `search.score` nie przyjmuje żadnych parametrów.

## <a name="examples"></a>Przykłady

Sortuj hotele rosnąco według stawki bazowej:

    $orderby=BaseRate asc

Sortuj hotele malejąco według klasyfikacji, a następnie rosnąco według stawki bazowej (pamiętaj, że wzrost jest domyślnie):

    $orderby=Rating desc,BaseRate

Sortuj hotele malejąco według klasyfikacji, a następnie rosnąco według odległości od podanych współrzędnych:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Sortuj hotele w porządku malejącym według search.score i rating, a następnie w kolejności rosnącej według odległości od podanych współrzędnych. Pomiędzy dwoma hotelami o identycznych wynikach i ocenach, najbliższy znajduje się na pierwszej liście:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Następne kroki  

- [Jak pracować z wynikami wyszukiwania w usłudze Azure Cognitive Search](search-pagination-page-layout.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
