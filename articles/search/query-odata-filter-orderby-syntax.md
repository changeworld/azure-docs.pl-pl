---
title: Omówienie języka OData
titleSuffix: Azure Cognitive Search
description: Omówienie języka OData dla filtrów, wybierz i kolejność zapytań usługi Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: f3a1be435e297ab4a9ba7f8bfbd5f3ce3451d8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153880"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Omówienie języka OData dla `$filter`, `$orderby`i `$select` w usłudze Azure Cognitive Search

Usługa Azure Cognitive Search obsługuje podzbiór składni wyrażenia OData dla **$filter** **, $orderby**i **$select** wyrażeń. Wyrażenia filtru są oceniane podczas analizowania zapytań, ograniczania wyszukiwania do określonych pól lub dodawania kryteriów dopasowania używanych podczas skanowania indeksu. Wyrażenia typu "kolejność po" są stosowane jako krok przetwarzania końcowego nad zestawem wyników w celu sortowania zwracanych dokumentów. Wybrane wyrażenia określają, które pola dokumentu są uwzględniane w zestawie wyników. Składnia tych wyrażeń różni się od [składni zapytania prostego](query-simple-syntax.md) lub [pełnego,](query-lucene-syntax.md) który jest używany w parametrze **wyszukiwania,** chociaż istnieje pewne nakładanie się w składni odwoływania się do pól.

Ten artykuł zawiera omówienie języka wyrażeń OData używanego w filtrach, kolejności po i wybranych wyrażeniach. Język jest przedstawiony "oddolnie", zaczynając od najbardziej podstawowych elementów i opierając się na nich. Składnia najwyższego poziomu dla każdego parametru jest opisana w osobnym artykule:

- [składnia $filter](search-query-odata-filter.md)
- [składnia $orderby](search-query-odata-orderby.md)
- [$select składni](search-query-odata-select.md)

Wyrażenia OData wahają się od prostych do bardzo złożonych, ale wszystkie one mają wspólne elementy. Najbardziej podstawowe części wyrażenia OData w usłudze Azure Cognitive Search to:

- **Ścieżki pól**, które odnoszą się do określonych pól indeksu.
- **Stałe**, które są wartościami literału określonego typu danych.

> [!NOTE]
> Terminologia w usłudze Azure Cognitive Search różni się od [standardu OData](https://www.odata.org/documentation/) na kilka sposobów. To, co nazywamy **polem** w usłudze Azure Cognitive Search, jest nazywane **właściwością** w OData i podobnie dla **ścieżki pola** w porównaniu ze **ścieżką właściwości.** **Indeks** **zawierający dokumenty** w usłudze Azure Cognitive Search jest określany bardziej ogólnie w OData jako **zestaw jednostek** **zawierający jednostki.** Terminologia usługi Azure Cognitive Search jest używana w tym odwołaniu.

## <a name="field-paths"></a>Ścieżki pól

Następujący EBNF[(Rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę ścieżek pól.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

Ścieżka pola składa się z jednego lub więcej **identyfikatorów oddzielonych** ukośnikami. Każdy identyfikator jest sekwencją znaków, która musi zaczynać się od litery LUB podkreślenia ASCII i zawierać tylko litery, cyfry lub podkreślenia ASCII. Litery mogą być wielkie lub małe litery.

Identyfikator może odwoływać się do nazwy pola lub **zmiennej zakresu** w kontekście`any` wyrażenia `all` [kolekcji](search-query-odata-collection-operators.md) ( lub ) w filtrze. Zmienna zakresu jest jak zmienna pętli, która reprezentuje bieżący element kolekcji. W przypadku kolekcji złożonych ta zmienna reprezentuje obiekt, dlatego można użyć ścieżek pól, aby odwoływać się do podpola zmiennej. Jest to analogiczne do notacji kropkowej w wielu językach programowania.

Przykłady ścieżek pól przedstawiono w poniższej tabeli:

| Ścieżka pola | Opis |
| --- | --- |
| `HotelName` | Odnosi się do pola najwyższego poziomu indeksu |
| `Address/City` | Odnosi się `City` do podpola złożonego pola w indeksie; `Address` jest typu `Edm.ComplexType` w tym przykładzie |
| `Rooms/Type` | Odnosi się `Type` do podpola złożonego pola kolekcji w indeksie; `Rooms` jest typu `Collection(Edm.ComplexType)` w tym przykładzie |
| `Stores/Address/Country` | Odnosi się `Country` do podpola `Address` podpola złożonego pola kolekcji w indeksie; `Stores` jest typu `Collection(Edm.ComplexType)` `Address` i jest `Edm.ComplexType` typu w tym przykładzie |
| `room/Type` | Odnosi się `Type` do podołowiowego zmiennej `room` zakresu, na przykład w wyrażeniu filtru`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Odnosi się `Country` do podołowie podołowie `Address` zmiennej `store` zakresu, na przykład w wyrażeniu filtru`Stores/any(store: store/Address/Country eq 'Canada')` |

Znaczenie ścieżki pola różni się w zależności od kontekstu. W filtrach ścieżka pola odwołuje się do wartości *pojedynczego wystąpienia* pola w bieżącym dokumencie. W innych kontekstach, takich jak **$orderby** **, $select**lub w [wyszukiwaniu polowym w pełnej składni Lucene,](query-lucene-syntax.md#bkmk_fields)ścieżka pola odnosi się do samego pola. Ta różnica ma pewne konsekwencje dla sposobu używania ścieżek pól w filtrach.

Rozważmy ścieżkę `Address/City`pola . W filtrze odnosi się to do jednego miasta dla bieżącego dokumentu, takiego jak "San Francisco". W przeciwieństwie `Rooms/Type` do tego, odnosi się do `Type` podporządku dla wielu pokoi (jak "standard" dla pierwszego pokoju, "deluxe" dla drugiego pokoju, i tak dalej). Ponieważ `Rooms/Type` nie odwołuje się do *pojedynczego* wystąpienia `Type`podoka, nie można go używać bezpośrednio w filtrze. Zamiast tego, aby filtrować typ pomieszczenia, należy użyć [wyrażenia lambda](search-query-odata-collection-operators.md) ze zmienną zakresu, w stylu:

    Rooms/any(room: room/Type eq 'deluxe')

W tym przykładzie `room` zmienna `room/Type` zakresu pojawia się w ścieżce pola. W ten `room/Type` sposób odnosi się do typu bieżącego pokoju w bieżącym dokumencie. Jest to pojedyncze `Type` wystąpienie podobsza, więc może być używane bezpośrednio w filtrze.

### <a name="using-field-paths"></a>Korzystanie ze ścieżek pól

Ścieżki pól są używane w wielu parametrach [interfejsów API REST usługi Azure Cognitive Search.](https://docs.microsoft.com/rest/api/searchservice/) W poniższej tabeli wymieniono wszystkie miejsca, w których można z nich korzystać, a także wszelkie ograniczenia dotyczące ich użycia:

| interfejs API | Nazwa parametru | Ograniczenia |
| --- | --- | --- |
| [Tworzenie](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [aktualizowanie](https://docs.microsoft.com/rest/api/searchservice/update-index) indeksu | `suggesters/sourceFields` | Brak |
| [Tworzenie](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [aktualizowanie](https://docs.microsoft.com/rest/api/searchservice/update-index) indeksu | `scoringProfiles/text/weights` | Może odnosić się tylko do pól **z wyszukujem** |
| [Tworzenie](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [aktualizowanie](https://docs.microsoft.com/rest/api/searchservice/update-index) indeksu | `scoringProfiles/functions/fieldName` | Może odnosić się tylko do pól **filtrowalnych** |
| [Wyszukaj](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`gdy `queryType` jest`full` | Może odnosić się tylko do pól **z wyszukujem** |
| [Wyszukaj](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Może odnosić się tylko do **pól, które można poluć** |
| [Wyszukaj](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Może odnosić się tylko do pól **z wyszukujem** |
| [Wyszukaj](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Może odnosić się tylko do pól **z wyszukujem** |
| [Sugerowanie](https://docs.microsoft.com/rest/api/searchservice/suggestions) i [autouzupełnianie](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Może odnosić się tylko do pól, które są częścią [sugest](index-add-suggesters.md) |
| [Wyszukiwanie](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Sugerowanie](https://docs.microsoft.com/rest/api/searchservice/suggestions)i [Autouzupełnienie](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Może odnosić się tylko do pól **filtrowalnych** |
| [Szukaj](https://docs.microsoft.com/rest/api/searchservice/search-documents) i [zaproponuj](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Może odnosić się tylko do pól **sortowalnych** |
| [Wyszukaj](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Zaproponuj](https://docs.microsoft.com/rest/api/searchservice/suggestions)i [Odnośnik](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Może odnosić się tylko do **pól, które można pobrać** |

## <a name="constants"></a>Stałe

Stałe w OData są wartościami literału danego typu [modelu danych jednostki](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM). Zobacz [obsługiwane typy danych,](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) aby uzyskać listę obsługiwanych typów w usłudze Azure Cognitive Search. Stałe typów kolekcji nie są obsługiwane.

W poniższej tabeli przedstawiono przykłady stałych dla każdego z typów danych obsługiwanych przez usługę Azure Cognitive Search:

| Typ danych | Przykładowe stałe |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Ucieczka znaków specjalnych w stałychach ciągów

Stałe ciągów w OData są rozdzielane pojedynczymi cudzysłowami. Jeśli trzeba skonstruować kwerendę ze stałą ciągu, która sama może zawierać pojedyncze cudzysłowy, można uniknąć osadzonych cudzysłowów, podwajając je.

Na przykład fraza z niesformatowanym apostrofem, takim jak "Samochód Alicji", będzie `'Alice''s car'`reprezentowana w OData jako stała ciągu .

> [!IMPORTANT]
> Podczas tworzenia filtrów programowo, ważne jest, aby pamiętać, aby uniknąć stałych ciągów, które pochodzą z danych wejściowych użytkownika. Ma to na celu ograniczenie możliwości [ataków iniekcyjnych,](https://wikipedia.org/wiki/SQL_injection)zwłaszcza w przypadku stosowania filtrów do wdrażania [przycinania zabezpieczeń.](search-security-trimming-for-azure-search.md)

### <a name="constants-syntax"></a>Składnia stałych

Poniższy EBNF[(Extended Backus-Naur Form)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)definiuje gramatykę dla większości stałych pokazanych w powyższej tabeli. Gramatyki dla typów geoprzestrzennych można znaleźć w [funkcjach geoprzestrzennych OData w usłudze Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).

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

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Tworzenie wyrażeń ze ścieżek pól i stałych

Ścieżki pól i stałe są najbardziej podstawową częścią wyrażenia OData, ale są już pełne wyrażenia. W rzeczywistości parametr **$select** w usłudze Azure Cognitive Search jest niczym innym jak oddzieloną przecinkami listą ścieżek pól, a **$orderby** nie jest o wiele bardziej skomplikowana niż **$select**. Jeśli zdarzy ci się `Edm.Boolean` mieć pole typu w indeksie, możesz nawet napisać filtr, który jest tylko ścieżką tego pola. Stałe `true` i `false` są również prawidłowe filtry.

Jednak w większości przypadków będziesz potrzebować bardziej złożonych wyrażeń, które odwołują się do więcej niż jednego pola i stałej. Wyrażenia te są tworzone na różne sposoby w zależności od parametru.

Następujący formularz EBNF[(Rozszerzony formularz Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)definiuje gramatykę parametrów **$filter,** **$orderby**i **$select.** Są one zbudowane z prostszych wyrażeń, które odwołują się do ścieżek pól i stałych:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

Parametry **$orderby** i **$select** są zarówno rozdzielone przecinkami, jak i list prostszych wyrażeń. Parametr **$filter** jest wyrażeniem logicznym składającym się z prostszych wyrażeń podrzędnych. Te wyrażenia podrzędne są łączone przy użyciu operatorów [ `and`logicznych, `or` `not` ](search-query-odata-logical-operators.md)takich jak , i , operatory [ `eq` `lt`porównania, `gt`](search-query-odata-comparison-operators.md)takie jak , i tak dalej , i operatory zbierania, takie jak [ `any` i `all` ](search-query-odata-collection-operators.md).

Parametry **$filter,** **$orderby**i **$select** są bardziej szczegółowo analizowane w następujących artykułach:

- [Składnia $filter OData w usłudze Azure Cognitive Search](search-query-odata-filter.md)
- [Składnia $orderby OData w usłudze Azure Cognitive Search](search-query-odata-orderby.md)
- [Składnia $select OData w usłudze Azure Cognitive Search](search-query-odata-select.md)

## <a name="see-also"></a>Zobacz też  

- [Nawigacja aspektowa w usłudze Azure Cognitive Search](search-faceted-navigation.md)
- [Filtry w usłudze Azure Cognitive Search](search-filters.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Składnia zapytań Lucene](query-lucene-syntax.md)
- [Składnia kwerend prostych w usłudze Azure Cognitive Search](query-simple-syntax.md)
