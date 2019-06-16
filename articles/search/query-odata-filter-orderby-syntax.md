---
title: Omówienie języka OData — usługa Azure Search
description: Omówienie języka OData filtrów, zaznacz i klauzuli order by dla zapytań usługi Azure Search.
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
ms.openlocfilehash: 166c23088fe0388199ca51efde05153bb5697e38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063699"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>Omówienie języka OData dla `$filter`, `$orderby`, i `$select` w usłudze Azure Search

Usługa Azure Search obsługuje podzbiór składnia wyrażenia OData **$filter**, **$orderby**, i **$select** wyrażenia. Wyrażenia filtru są oceniane podczas zapytania analizy, ograniczając wyszukiwanie określonych pól lub dodawanie kryteriów dopasowania używane podczas skanowania indeksu. Wyrażenia order by są stosowane jako krok przetwarzania końcowego przez zestaw wyników, aby posortować dokumentów, które są zwracane. Wybierz wyrażeń określić pola dokumentu, które znajdują się w zestawie wyników. Składnia wyrażenia różni się od [proste](query-simple-syntax.md) lub [pełną](query-lucene-syntax.md) zapytania składnia, która jest używana w **wyszukiwania** parametru, mimo, że niektóre nakładają się w składni odwołuje się do pola.

Ten artykuł zawiera omówienie języka wyrażeń OData używane w filtry, klauzuli order by i wyrażenia select. Język jest prezentowana "wstępujące", począwszy od najbardziej podstawowe elementy i budowanie na ich. W oddzielnym artykule opisano składnię najwyższego poziomu dla każdego parametru:

- [Składnia $filter](search-query-odata-filter.md)
- [Składnia $orderby](search-query-odata-orderby.md)
- [Składnia $select](search-query-odata-select.md)

OData wyrażeń z zakresu od prostego do bardzo złożone, ale wszystkie współużytkują wspólne elementy. Najbardziej podstawowa części wyrażenia OData w usłudze Azure Search są:

- **Pole ścieżki**, które odnoszą się do określonych pól indeksu.
- **Stałe**, które są wartości literału typu danych.

> [!NOTE]
> Terminologia dotycząca usługi Azure Search, który różni się od [ze standardu OData](https://www.odata.org/documentation/) na kilka sposobów. Tak zwany **pola** w usłudze Azure Search jest nazywany **właściwość** w protokole OData i podobnie dla **ścieżka pola** a **ścieżkę właściwości**. **Indeksu** zawierający **dokumenty** w usłudze Azure Search jest określany ogólnie w protokole OData jako **zestaw jednostek** zawierający **jednostek**. Terminologia dotycząca usługi Azure Search jest używane w całym to odwołanie.

## <a name="field-paths"></a>Pola ścieżek

Następujące EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę pola ścieżki.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

Ścieżka pola składa się z co najmniej jeden **identyfikatory** oddzielonych ukośnikami. Każdy identyfikator jest sekwencją znaków, które musi zaczynać się literą ASCII lub znakiem podkreślenia i może zawierać wyłącznie ASCII litery, cyfry i znaki podkreślenia. Górna lub małe, może być liter.

Identyfikator może odwoływać się do nazwy pola lub do **zmiennej zakresu** w kontekście [wyrażeniu kolekcji](search-query-odata-collection-operators.md) (`any` lub `all`) w filtrze. Zmienna zakresu jest jak zmienna pętli, która reprezentuje bieżący element w kolekcji. W przypadku złożonych kolekcji tej zmiennej reprezentuje obiekt, dlatego można użyć pola ścieżki do odwoływania się do pól podrzędnych w zmiennej. Jest to analogiczne do notacji z kropką w wielu językach programowania.

W poniższej tabeli przedstawiono przykładowe pole ścieżki:

| Ścieżka pola | Opis |
| --- | --- |
| `HotelName` | Odwołuje się do najwyższego poziomu pola indeksu |
| `Address/City` | Odwołuje się do `City` podrzędne pola złożonych pola w indeksie; `Address` typu `Edm.ComplexType` w tym przykładzie |
| `Rooms/Type` | Odwołuje się do `Type` podrzędne pola złożonych kolekcji pola w indeksie; `Rooms` typu `Collection(Edm.ComplexType)` w tym przykładzie |
| `Stores/Address/Country` | Odwołuje się do `Country` podrzędne pola `Address` podrzędne pola złożonych kolekcji pola w indeksie; `Stores` typu `Collection(Edm.ComplexType)` i `Address` typu `Edm.ComplexType` w tym przykładzie |
| `room/Type` | Odwołuje się do `Type` podrzędne pola `room` zmienna zakresu, na przykład w wyrażeniu filtru `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Odwołuje się do `Country` podrzędne pola `Address` podrzędne pola `store` zmienna zakresu, na przykład w wyrażeniu filtru `Stores/any(store: store/Address/Country eq 'Canada')` |

Znaczenie ścieżka pola różni się w zależności od kontekstu. W filtrach, ścieżka pola odwołuje się do wartości *pojedyncze wystąpienie* pola w bieżącym dokumencie. W innych kontekstach takich jak **$orderby**, **$select**, lub [fielded wyszukiwania w pełnej składni Lucene](query-lucene-syntax.md#bkmk_fields), ścieżka pola odwołuje się do samego pola. Różnica ta ma pewne skutki dla jak jej używasz ścieżki pola w filtrach.

Należy wziąć pod uwagę ścieżka pola `Address/City`. W filtrze odnosi się to do pojedynczego Miasto dla bieżącego dokumentu, takich jak "San Francisco". Z kolei `Rooms/Type` odwołuje się do `Type` podrzędne pola dla wielu pokojów (np. "standardowy" pierwsze miejsce "deluxe" dla drugiego miejsca i tak dalej). Ponieważ `Rooms/Type` nie odwołuje się do *pojedyncze wystąpienie* podrzędne pola `Type`, nie można użyć bezpośrednio w filtrze. Zamiast tego, aby filtrować według typu miejsca, należy użyć [wyrażenia lambda](search-query-odata-collection-operators.md) ze zmienną zakresu w następujący sposób:

    Rooms/any(room: room/Type eq 'deluxe')

W tym przykładzie zmienna zakresu `room` pojawia się w `room/Type` ścieżka pola. W ten sposób `room/Type` odwołuje się do typu bieżącej pokoju w bieżącym dokumencie. Jest to pojedyncze wystąpienie `Type` podrzędne pola, dzięki czemu może być używany bezpośrednio w filtrze.

### <a name="using-field-paths"></a>Za pomocą pola ścieżki

Pole ścieżki są używane w wielu parametrach [interfejsu API usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/). W poniższej tabeli wymieniono wszystkie miejsca, w którym mogą być używane, a także wszelkie ograniczenia dotyczące użycia:

| Interfejs API | Nazwa parametru | Ograniczenia |
| --- | --- | --- |
| [Tworzenie](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-index) indeksu | `suggesters/sourceFields` | Brak |
| [Tworzenie](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-index) indeksu | `scoringProfiles/text/weights` | Można odwoływać się tylko do **wyszukiwanie** pola |
| [Tworzenie](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-index) indeksu | `scoringProfiles/functions/fieldName` | Można odwoływać się tylko do **filtrowanie** pola |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` gdy `queryType` jest `full` | Można odwoływać się tylko do **wyszukiwanie** pola |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Można odwoływać się tylko do **aspektów** pola |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Można odwoływać się tylko do **wyszukiwanie** pola |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Można odwoływać się tylko do **wyszukiwanie** pola |
| [Zaproponuj](https://docs.microsoft.com/rest/api/searchservice/suggestions) i [autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Można odwoływać się tylko do pól, które są częścią [sugestora](index-add-suggesters.md) |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents), [zasugerować](https://docs.microsoft.com/rest/api/searchservice/suggestions), i [autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Można odwoływać się tylko do **filtrowanie** pola |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents) i [zasugerować](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Można odwoływać się tylko do **sortowanie** pola |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents), [zasugerować](https://docs.microsoft.com/rest/api/searchservice/suggestions), i [wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Można odwoływać się tylko do **pobieranie** pola |

## <a name="constants"></a>Stałe

Stałe w protokole OData są wartości literału z danym [modelu Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM struktury) typu. Zobacz [obsługiwanych typów danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Aby uzyskać listę obsługiwanych typów w usłudze Azure Search. Stałe typów kolekcji nie są obsługiwane.

W poniższej tabeli przedstawiono przykłady stałych dla każdego z typów danych obsługiwanych przez usługę Azure Search:

| Typ danych | Przykład — stałe |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

Następujące EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę dla większości stałe pokazanych w powyższej tabeli. Gramatyka dla typów geoprzestrzenne znajdują się w [funkcje geoprzestrzenne OData w usłudze Azure Search](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

## <a name="building-expressions-from-field-paths-and-constants"></a>Tworzenie wyrażeń z pola ścieżki i stałe

Pole ścieżki i stałe są najbardziej podstawową częścią wyrażenia OData, ale są one już pełnego wyrażenia, samodzielnie. W rzeczywistości **$select** parametru w usłudze Azure Search jest nothing, ale rozdzielaną przecinkami listę ścieżek pola i **$orderby** nie jest znacznie bardziej skomplikowane niż **$select**. Jeśli występuje pole o typie `Edm.Boolean` w indeksie, można nawet napisać filtr, który ma nic, ale ścieżka tego pola. Stałe `true` i `false` podobnie są prawidłowymi filtrami.

Jednak w większości przypadków należy bardziej złożonych wyrażeń, które odwołują się do więcej niż jedno pole i stały. Wyrażenia te są tworzone na różne sposoby w zależności od tego parametru.

Następujące EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatyka dla **$filter**, **$orderby**, i **$select** parametrów. Te są tworzone na podstawie prostsze wyrażeń, które odwołują się do pola ścieżki i stałe:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

**$Orderby** i **$select** parametry są zarówno rozdzielaną przecinkami listę prostsze wyrażeń. **$Filter** parametr jest wyrażenie logiczne, które składa się prostsze wyrażeń podrzędnych. Te wyrażenia podrzędne są łączone za pomocą operatorów logicznych, takich jak [ `and`, `or`, i `not` ](search-query-odata-logical-operators.md), operatory porównania, takie jak [ `eq`, `lt`, `gt`i tak dalej](search-query-odata-comparison-operators.md)i operatorów kolekcji, takie jak [ `any` i `all` ](search-query-odata-collection-operators.md).

**$Filter**, **$orderby**, i **$select** parametry zostały opisane bardziej szczegółowo w następujących artykułach:

- [Składnia OData $filter w usłudze Azure Search](search-query-odata-filter.md)
- [Składnia OData $orderby w usłudze Azure Search](search-query-odata-orderby.md)
- [Składnia OData $select w usłudze Azure Search](search-query-odata-select.md)

## <a name="see-also"></a>Zobacz także  

- [Nawigacja aspektowa w usłudze Azure Search](search-faceted-navigation.md)
- [Filtry w usłudze Azure Search](search-filters.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Składnia zapytań Lucene](query-lucene-syntax.md)
- [Prosta składnia zapytań w usłudze Azure Search](query-simple-syntax.md)
