---
title: Odwołanie operator kolekcji OData — usługa Azure Search
description: Operatorzy kolekcję OData, wszystkie oraz wyrażeń lambda w zapytaniach usługi Azure Search.
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
ms.openlocfilehash: 7afafe158732b14ebe314eeee5d015acddc55b72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079940"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Operatorów kolekcji OData w usłudze Azure Search - `any` i `all`

Podczas zapisywania [wyrażenie filtru OData](query-odata-filter-orderby-syntax.md) za pomocą usługi Azure Search, jest często przydatne do filtrowania kolekcji pól. Można to osiągnąć przy użyciu `any` i `all` operatorów.

## <a name="syntax"></a>Składnia

Następujące EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatyki wyrażenia OData, która używa `any` lub `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

Istnieją trzy rodzaje wyrażeń, które filtrują kolekcji.

- Pierwsze dwa iteracja polem kolekcji stosowania predykatu, który został podany w postaci wyrażenia lambda do każdego elementu kolekcji.
  - Usługi za pomocą wyrażenia `all` zwraca `true` Jeśli predykat jest spełniony dla każdego elementu kolekcji.
  - Usługi za pomocą wyrażenia `any` zwraca `true` Jeśli predykat jest spełniony dla co najmniej jeden element w kolekcji.
- Trzecia formą kolekcji filtrowania używa `any` bez sprawdzenia, czy pole kolekcja jest pusta w wyrażeniu lambda. Jeśli kolekcja zawiera żadnych elementów, funkcja zwraca `true`. Jeśli kolekcja jest pusta, zwraca `false`.

A **wyrażenia lambda** w kolekcji filtrów przypomina treść pętli w języku programowania. Definiuje zmienną o nazwie **zmiennej zakresu**, który przechowuje bieżącego elementu kolekcji podczas iteracji. Definiuje również innego wyrażenie logiczne, które są kryteria filtrowania do zastosowania do zmiennej zakresu dla każdego elementu kolekcji.

## <a name="examples"></a>Przykłady

Dopasowanie dokumentów, których `tags` pole zawiera dokładnie ciąg "Wi-Fi":

    tags/any(t: t eq 'wifi')

Dopasowanie dokumenty, gdzie każdy element `ratings` pola znajduje się od 3 do 5 (włącznie):

    ratings/all(r: r ge 3 and r le 5)

Dopasowanie dokumentów, gdzie dowolne geo koordynuje w `locations` pola znajduje się w danym wielokąta:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Dopasowanie dokumenty, gdzie `rooms` pole jest puste:

    not rooms/any()

Odpowiada dokumentów w przypadku gdy wszystkie sale `rooms/amenities` pole zawiera wartość "tv" i `rooms/baseRate` jest mniejsza niż 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Ograniczenia

Nie każda funkcja wyrażenia filtru jest dostępna w treści wyrażenia lambda. Ograniczenia różnią się w zależności od typu danych pola kolekcji, które chcesz filtrować. W poniższej tabeli podsumowano ograniczenia.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Aby uzyskać szczegółowe informacje na temat tych ograniczeń, a także przykłady, zobacz [Rozwiązywanie problemów z kolekcji filtrów w usłudze Azure Search](search-query-troubleshoot-collection-filters.md). Aby uzyskać bardziej szczegółowe informacje o tym, dlaczego te ograniczenia istnieje, zobacz [objaśnienie filtrów kolekcji w usłudze Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Kolejne kroki  

- [Filtry w usłudze Azure Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
