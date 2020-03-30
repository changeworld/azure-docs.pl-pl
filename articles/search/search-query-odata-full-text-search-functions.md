---
title: OData odwołanie do funkcji wyszukiwania pełnotekstowego
titleSuffix: Azure Cognitive Search
description: Funkcje wyszukiwania pełnotekstowego OData, search.ismatch i search.ismatchscoring, w zapytaniach usługi Azure Cognitive Search.
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
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793349"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Funkcje wyszukiwania pełnotekstowego OData `search.ismatch` w usłudze Azure Cognitive Search — i`search.ismatchscoring`

Usługa Azure Cognitive Search obsługuje wyszukiwanie pełnotekstowe w kontekście `search.ismatchscoring` [wyrażeń filtru OData](query-odata-filter-orderby-syntax.md) za pośrednictwem `search.ismatch` funkcji i funkcji. Funkcje te umożliwiają łączenie wyszukiwania pełnotekstowego ze ścisłym filtrem logicznym w `search` sposób, który nie jest możliwy tylko przy użyciu parametru najwyższego poziomu [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> `search.ismatch` Funkcje `search.ismatchscoring` i funkcje są obsługiwane tylko w filtrach w [interfejsie API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents). Nie są one obsługiwane w interfejsach API [zaproponuj](https://docs.microsoft.com/rest/api/searchservice/suggestions) lub [autouzupełniaj.](https://docs.microsoft.com/rest/api/searchservice/autocomplete)

## <a name="syntax"></a>Składnia

Następujący EBNF ([Rozszerzony Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) `search.ismatch` definiuje `search.ismatchscoring` gramatykę i funkcje:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

### <a name="searchismatch"></a>search.ismatch

Funkcja `search.ismatch` ocenia kwerendę wyszukiwania pełnotekstowego jako część wyrażenia filtru. Dokumenty pasujące do zapytania wyszukiwania zostaną zwrócone w zestawie wyników. Dostępne są następujące przeciążenia tej funkcji:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametry są zdefiniowane w poniższej tabeli:

| Nazwa parametru | Typ | Opis |
| --- | --- | --- |
| `search` | `Edm.String` | Kwerenda wyszukiwania (w [prostej](query-simple-syntax.md) lub [pełnej](query-lucene-syntax.md) składni zapytania Lucene). |
| `searchFields` | `Edm.String` | Oddzielona przecinkami lista pól z wyszukujem; domyślnie wszystkie pola z wyszukujem w indeksie. W przypadku korzystania z `search` [wyszukiwania fielded](query-lucene-syntax.md#bkmk_fields) w parametrze specyfikatory pól w kwerendzie Lucene zastępują wszystkie pola określone w tym parametrze. |
| `queryType` | `Edm.String` | `'simple'`lub `'full'`; domyślnie `'simple'`na . Określa, jaki język kwerendy `search` został użyty w parametrze. |
| `searchMode` | `Edm.String` | `'any'`lub `'all'`, domyślnie na `'any'`. Wskazuje, czy dowolne lub wszystkie `search` wyszukiwane terminy w parametrze muszą być dopasowane, aby zliczyć dokument jako dopasowanie. Podczas korzystania z [operatorów logicznych Lucene](query-lucene-syntax.md#bkmk_boolean) w parametrze, `search` będą one pierwszeństwo przed tym parametrem. |

Wszystkie powyższe parametry są równoważne odpowiednim [parametrom żądania wyszukiwania w interfejsie API wyszukiwania.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

Funkcja `search.ismatch` zwraca wartość typu, `Edm.Boolean`która umożliwia skomponowanie jej z innymi podwyzatorami filtru przy użyciu [logicznych operatorów logicznych logicznych logicznych logicznych.](search-query-odata-logical-operators.md)

> [!NOTE]
> Usługa Azure Cognitive Search `search.ismatch` `search.ismatchscoring` nie obsługuje używania lub wewnątrz wyrażeń lambda. Oznacza to, że nie jest możliwe pisanie filtrów nad kolekcjami obiektów, które mogą korelować dopasowania wyszukiwania pełnotekstowego ze ścisłymi dopasowaniami filtrów na tym samym obiekcie. Aby uzyskać więcej informacji na temat tego ograniczenia, a także przykłady, zobacz [Rozwiązywanie problemów z filtrami kolekcji w usłudze Azure Cognitive Search.](search-query-troubleshoot-collection-filters.md) Aby uzyskać bardziej szczegółowe informacje na temat tego ograniczenia, zobacz [Opis filtrów kolekcji w usłudze Azure Cognitive Search.](search-query-understand-collection-filters.md)


### <a name="searchismatchscoring"></a>search.ismatchscoring

Funkcja, `search.ismatchscoring` podobnie `search.ismatch` jak funkcja, zwraca `true` dla dokumentów, które pasują do kwerendy wyszukiwania pełnotekstowego przekazywane jako parametr. Różnica między nimi polega na tym, `search.ismatchscoring` że wynik trafności dokumentów pasujących do `search.ismatch`zapytania przyczyni się do ogólnego wyniku dokumentu, podczas gdy w przypadku , wynik dokumentu nie zostanie zmieniony. Następujące przeciążenia tej funkcji są dostępne z `search.ismatch`parametrami identycznymi z parametrami:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Zarówno `search.ismatch` i `search.ismatchscoring` funkcje mogą być używane w tym samym wyrażeniu filtru.

## <a name="examples"></a>Przykłady

Znajdź dokumenty ze słowem "nabrzeże". Ta kwerenda filtrująca jest `search=waterfront`identyczna z [żądaniem wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) z programem .

    search.ismatchscoring('waterfront')

Znajdź dokumenty ze słowem "hostel" i ocena większa lub równa 4, lub dokumenty ze słowem "motel" i ocena równa 5. Uwaga: to żądanie nie może `search.ismatchscoring` być wyrażone bez funkcji.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Znajdź dokumenty bez słowa "luksus".

    not search.ismatch('luxury')

Znajdź dokumenty z frazą "widok oceanu" lub oceną równą 5. Kwerenda `search.ismatchscoring` będzie wykonywana tylko `HotelName` `Rooms/Description`względem pól i .

Dokumenty, które pasowały tylko do drugiej klauzuli rozłączenia, również `Rating` zostaną zwrócone - hotele o wysokości 5. Aby wyjaśnić, że te dokumenty nie pasują do żadnej z ocenionych części wyrażenia, zostaną zwrócone z wynikiem równym zero.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Znajdź dokumenty, w których terminy "hotel" i "lotnisko" znajdują się w obrębie 5 słów od siebie w opisie hotelu, a palenie nie jest dozwolone w co najmniej niektórych pokojach. Ta kwerenda używa [pełnego języka zapytania Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Następne kroki  

- [Filtry w usłudze Azure Cognitive Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
