---
title: Odwołanie operatora zbierania danych OData — Azure Search
description: Operatory kolekcji OData, dowolne i wszystkie oraz wyrażenia lambda w zapytaniach Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: e057d0b57162d10aab13d8b1f77e0eaddca2ec2a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647633"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Operatory kolekcji OData w Azure Search `any` i`all`

Podczas pisania [wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) do użycia z Azure Search, często przydatne jest Filtrowanie pól kolekcji. Można to osiągnąć przy użyciu `any` operatorów i. `all`

## <a name="syntax"></a>Składnia

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę wyrażenia OData, które używa `any` lub. `all`

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
> [Diagram składni OData dla Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Istnieją trzy formy wyrażeń, które filtrują kolekcje.

- Pierwsze dwie iteracje w polu kolekcji, stosując predykat określony w formie wyrażenia lambda do każdego elementu kolekcji.
  - Wyrażenie używające `all` zwraca `true` , jeśli predykat ma wartość true dla każdego elementu kolekcji.
  - Wyrażenie używające `any` zwraca `true` , jeśli predykat ma wartość true dla co najmniej jednego elementu kolekcji.
- Trzecia forma filtru kolekcji używa `any` bez wyrażenia lambda, aby sprawdzić, czy pole kolekcji jest puste. Jeśli kolekcja zawiera jakiekolwiek elementy, zwraca `true`. Jeśli kolekcja jest pusta, zwraca wartość `false`.

**Wyrażenie lambda** w filtrze kolekcji jest takie jak treść pętli w języku programowania. Definiuje zmienną, nazywaną **zmienną zakresu**, która przechowuje bieżący element kolekcji podczas iteracji. Definiuje również inne wyrażenie logiczne, które jest kryteria filtru, które mają być zastosowane do zmiennej zakresu dla każdego elementu kolekcji.

## <a name="examples"></a>Przykłady

Dopasowuje dokumenty `tags` , których pole zawiera dokładnie ciąg "Wi-Fi":

    tags/any(t: t eq 'wifi')

Dopasowuje dokumenty, w których każdy `ratings` element pola mieści się w zakresie od 3 do 5 włącznie:

    ratings/all(r: r ge 3 and r le 5)

Dopasuj dokumenty, w których każda ze współrzędnych `locations` geograficznych w polu znajduje się w obrębie danego wielokątu:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Dopasuj dokumenty, w `rooms` których pole jest puste:

    not rooms/any()

Dopasuj dokumenty, `rooms/amenities` w przypadku których wszystkie pokoje zawierają wartość "TV" i `rooms/baseRate` jest mniejsza niż 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Ograniczenia

Nie każda funkcja wyrażeń filtru jest dostępna wewnątrz treści wyrażenia lambda. Ograniczenia różnią się w zależności od typu danych pola kolekcji, które chcesz filtrować. Poniższa tabela zawiera podsumowanie ograniczeń.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Aby uzyskać więcej informacji o tych ograniczeniach oraz przykładach, zobacz [Rozwiązywanie problemów z filtrami kolekcji w Azure Search](search-query-troubleshoot-collection-filters.md). Aby uzyskać bardziej szczegółowe informacje na temat tego, dlaczego istnieją ograniczenia, zobacz [Omówienie filtrów kolekcji w Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Następne kroki  

- [Filtry w Azure Search](search-filters.md)
- [Przegląd języka wyrażenia OData dla Azure Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;Azure Search interfejs API REST usługi&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
