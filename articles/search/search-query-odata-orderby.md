---
title: OData klauzuli order by dokumentacja — Azure Search
description: Dokumentacja języka OData dla składni w klauzuli order by w zapytaniach usługi Azure Search.
ms.date: 06/13/2019
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
ms.openlocfilehash: 1ced35dc73e6d596fbeda32590ab0b69df396c5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079758"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Składnia OData $orderby w usłudze Azure Search

 Możesz użyć [OData **$orderby** parametru](query-odata-filter-orderby-syntax.md) do stosowania niestandardowej kolejności sortowania wyników wyszukiwania w usłudze Azure Search. W tym artykule opisano składnię **$orderby** szczegółowo. Aby uzyskać ogólne informacje o sposobie używania **$orderby** podczas wyświetlania wyników wyszukiwania, zobacz [sposób pracy z usługą search wyników w usłudze Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Składnia

**$Orderby** parametru akceptuje rozdzielaną przecinkami listę maksymalnie 32 **klauzule w klauzuli order by**. Składnia klauzuli order by jest opisana przez następujące EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

Każdej klauzuli ma kryteriów sortowania, opcjonalnie następuje kierunek sortowania (`asc` rosnącą lub `desc` dla malejącej). Jeśli nie określisz kierunku, wartość domyślna jest rosnąca. Kryteria sortowania mogą być ścieżka `sortable` pola lub wywołania [ `geo.distance` ](search-query-odata-geo-spatial-functions.md) lub [ `search.score` ](search-query-odata-search-score-function.md) funkcji.

Jeśli wiele dokumentów ma takich samych kryteriów sortowania i `search.score` funkcja nie jest używana (na przykład, jeśli sortujesz według liczbową `Rating` pola i trzy dokumenty wszystkie mają klasyfikację 4), ties będą uszkodzone wynik dokumentu, w kolejności malejącej. Gdy dokument wyniki są takie same (na przykład, gdy nie ma wyszukiwania pełnotekstowego zapytania określony w żądaniu), następnie względne uporządkowanie wiązanej dokumentów jest nieokreślony.

Można określić wielu kryteriów sortowania. Kolejność wyrażeń określa kolejność sortowania końcowej. Na przykład, aby posortować malejąco według wyników, a następnie ocenę, składnia będzie `$orderby=search.score() desc,Rating desc`.

Składnia `geo.distance` w **$orderby** jest taka sama jak w **$filter**. Korzystając z `geo.distance` w **$orderby**, pole, którego dotyczy musi być typu `Edm.GeographyPoint` i musi być również `sortable`.

Składnia `search.score` w **$orderby** jest `search.score()`. Funkcja `search.score` nie przyjmuje żadnych parametrów.

## <a name="examples"></a>Przykłady

Sortuj hotele rosnącej według stawkę za podstawowy:

    $orderby=BaseRate asc

Sortowanie hotele malejąco według klasyfikacji, a następnie rosnącej według stawki podstawowej (należy pamiętać, że rosnąca jest ustawieniem domyślnym):

    $orderby=Rating desc,BaseRate

Sortuj malejąco według klasyfikacji, a następnie rosnącej według odległości od danej współrzędne hotele:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Posortuj hotele malejąco według search.score i klasyfikacji, a następnie rosnąco przez odległości od danej współrzędnych. Między dwa hotele z oceny istotności identyczne i klasyfikacje znajdującego się najbliżej jest wymienione jako pierwsze:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Kolejne kroki  

- [Jak pracować z usługą wyszukiwania wyników w usłudze Azure Search](search-pagination-page-layout.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
