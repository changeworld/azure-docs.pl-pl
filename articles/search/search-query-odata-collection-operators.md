---
title: Odwołanie do operatora kolekcji OData
titleSuffix: Azure Cognitive Search
description: Podczas tworzenia wyrażeń filtru w zapytaniach usługi Azure Cognitive Search należy używać operatorów "dowolne" i "wszystkie" w wyrażeniach lambda, gdy filtr znajduje się w polu kolekcji lub złożonej kolekcji.
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113238"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Operatory zbierania OData `any` w usłudze Azure Cognitive Search — i`all`

Podczas pisania [wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) do użycia z usługi Azure Cognitive Search, często jest przydatne do filtrowania pól kolekcji. Można to osiągnąć `any` za `all` pomocą operatorów i.

## <a name="syntax"></a>Składnia

Następujący EBNF[(Rozszerzony formularz Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)definiuje gramatykę wyrażenia OData, które używa `any` lub `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

Istnieją trzy formy wyrażenia, które filtrują kolekcje.

- Pierwsze dwa iteracji nad pole kolekcji, stosując predykatu podane w postaci wyrażenia lambda do każdego elementu kolekcji.
  - Wyrażenie przy `all` `true` użyciu zwraca, jeśli predykat jest true dla każdego elementu kolekcji.
  - Wyrażenie przy `any` `true` użyciu zwraca, jeśli predykat jest true dla co najmniej jeden element kolekcji.
- Trzecia forma filtru kolekcji używa `any` bez wyrażenia lambda, aby sprawdzić, czy pole kolekcji jest puste. Jeśli kolekcja ma jakieś elementy, zwraca `true`. Jeśli kolekcja jest pusta, zwraca `false`.

Wyrażenie **lambda** w filtrze kolekcji jest jak treść pętli w języku programowania. Definiuje zmienną, zwaną **zmienną zakresu**, która przechowuje bieżący element kolekcji podczas iteracji. Definiuje również inne wyrażenie logiczne, które jest kryteria filtrowania, które mają zastosowanie do zmiennej zakresu dla każdego elementu kolekcji.

## <a name="examples"></a>Przykłady

Dopasuj dokumenty, których `tags` pole zawiera dokładnie ciąg "wifi":

    tags/any(t: t eq 'wifi')

Dopasuj dokumenty, `ratings` w których każdy element pola mieści się między 3 a 5, włącznie:

    ratings/all(r: r ge 3 and r le 5)

Dopasuj dokumenty, w których `locations` którykolwiek z współrzędnych geograficznych w polu znajduje się w obrębie danego wielokąta:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Dopasuj `rooms` dokumenty, w których pole jest puste:

    not rooms/any()

Dopasuj dokumenty, `rooms/amenities` w których dla `rooms/baseRate` wszystkich pokoi pole zawiera "tv" i jest mniejsze niż 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Ograniczenia

Nie każda funkcja wyrażeń filtru jest dostępna wewnątrz treści wyrażenia lambda. Ograniczenia różnią się w zależności od typu danych pola kolekcji, które chcesz filtrować. W poniższej tabeli podsumowano ograniczenia.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Aby uzyskać więcej informacji na temat tych ograniczeń, a także przykładów, zobacz [Rozwiązywanie problemów z filtrami kolekcji w usłudze Azure Cognitive Search.](search-query-troubleshoot-collection-filters.md) Aby uzyskać bardziej szczegółowe informacje na temat przyczyn istnienia tych ograniczeń, zobacz [Opis filtrów kolekcji w usłudze Azure Cognitive Search.](search-query-understand-collection-filters.md)

## <a name="next-steps"></a>Następne kroki  

- [Filtry w usłudze Azure Cognitive Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
