---
title: Odwołanie operatora zbierania danych OData
titleSuffix: Azure Cognitive Search
description: Operatory kolekcji OData, dowolne i wszystkie oraz wyrażenia lambda w zapytaniach Wyszukiwanie poznawcze platformy Azure.
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
ms.openlocfilehash: fdb250a844b70cef4f6941debbb1fa7450874932
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793400"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Operatory kolekcji OData w usłudze Azure Wyszukiwanie poznawcze — `any` i `all`

Podczas pisania [wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) do użycia z usługą Azure wyszukiwanie poznawcze często warto odfiltrować pola kolekcji. Można to osiągnąć przy użyciu operatorów `any` i `all`.

## <a name="syntax"></a>Składnia

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę wyrażenia OData, które używa `any` lub `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla usługi Azure wyszukiwanie poznawcze](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Istnieją trzy formy wyrażeń, które filtrują kolekcje.

- Pierwsze dwie iteracje w polu kolekcji, stosując predykat określony w formie wyrażenia lambda do każdego elementu kolekcji.
  - Wyrażenie używające `all` zwraca `true`, jeśli predykat ma wartość true dla każdego elementu kolekcji.
  - Wyrażenie używające `any` zwraca `true`, jeśli predykat ma wartość true dla co najmniej jednego elementu kolekcji.
- Trzecia forma filtru kolekcji używa `any` bez wyrażenia lambda, aby sprawdzić, czy pole kolekcji jest puste. Jeśli kolekcja zawiera jakiekolwiek elementy, zwraca `true`. Jeśli kolekcja jest pusta, zwraca `false`.

**Wyrażenie lambda** w filtrze kolekcji jest takie jak treść pętli w języku programowania. Definiuje zmienną, nazywaną **zmienną zakresu**, która przechowuje bieżący element kolekcji podczas iteracji. Definiuje również inne wyrażenie logiczne, które jest kryteria filtru, które mają być zastosowane do zmiennej zakresu dla każdego elementu kolekcji.

## <a name="examples"></a>Przykłady

Dopasowuje dokumenty, których pole `tags` zawiera dokładnie ciąg "Wi-Fi":

    tags/any(t: t eq 'wifi')

Dopasuj dokumenty, w których każdy element pola `ratings` ma wartość od 3 do 5 włącznie:

    ratings/all(r: r ge 3 and r le 5)

Dopasuj dokumenty, w których każda ze współrzędnych geograficznych w `locations` polu znajduje się w obrębie danego wielokątu:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Dopasuj dokumenty, w których pole `rooms` jest puste:

    not rooms/any()

Dopasuj dokumenty, w przypadku których wszystkie pokoje zawierają pola `rooms/amenities` "TV" i `rooms/baseRate` są mniejsze niż 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Ograniczenia

Nie każda funkcja wyrażeń filtru jest dostępna wewnątrz treści wyrażenia lambda. Ograniczenia różnią się w zależności od typu danych pola kolekcji, które chcesz filtrować. Poniższa tabela zawiera podsumowanie ograniczeń.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Aby uzyskać więcej informacji o tych ograniczeniach oraz przykładach, zobacz [Rozwiązywanie problemów z filtrami kolekcji na platformie Azure wyszukiwanie poznawcze](search-query-troubleshoot-collection-filters.md). Aby uzyskać bardziej szczegółowe informacje o tym, dlaczego istnieją te ograniczenia, zobacz [Omówienie filtrów kolekcji na platformie Azure wyszukiwanie poznawcze](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Następne kroki  

- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;w interfejsie API REST usługi Azure wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
