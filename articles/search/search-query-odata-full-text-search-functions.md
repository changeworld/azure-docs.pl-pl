---
title: Odwołanie do funkcji wyszukiwania pełnotekstowego w OData — usługa Azure Search
description: Funkcje wyszukiwania pełnotekstowego OData, search.ismatch i search.ismatchscoring w zapytaniach usługi Azure Search.
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
ms.openlocfilehash: 158312a7afe88e7b9885376c5d28b01958acbbfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079810"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>Funkcje wyszukiwania pełnotekstowego OData w usłudze Azure Search - `search.ismatch` i `search.ismatchscoring`

Usługa Azure Search obsługuje wyszukiwanie pełnotekstowe w kontekście [wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) za pośrednictwem `search.ismatch` i `search.ismatchscoring` funkcji. Te funkcje pozwalają połączyć wyszukiwania pełnotekstowego z ograniczeniami filtrowaniem logiczną w sposób, który nie jest możliwe tylko za pomocą najwyższego poziomu `search` parametru [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> `search.ismatch` i `search.ismatchscoring` funkcje są obsługiwane tylko w filtrach w [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents). Nie są obsługiwane w [Suggest](https://docs.microsoft.com/rest/api/searchservice/suggestions) lub [autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) interfejsów API.

## <a name="syntax"></a>Składnia

Następujące EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatyki `search.ismatch` i `search.ismatchscoring` funkcje:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

### <a name="searchismatch"></a>Search.ismatch

`search.ismatch` Funkcja oblicza zapytanie wyszukiwania pełnotekstowego, jako część wyrażenia filtru. Dokumenty, które odpowiada zapytaniu wyszukiwania zostaną zwrócone w zestawie wyników. Dostępne są następujące przeciążenia tej funkcji:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametry są zdefiniowane w poniższej tabeli:

| Nazwa parametru | Typ | Opis |
| --- | --- | --- |
| `search` | `Edm.String` | Zapytanie wyszukiwania (albo [proste](query-simple-syntax.md) lub [pełne](query-lucene-syntax.md) składnia zapytań Lucene). |
| `searchFields` | `Edm.String` | Rozdzielana przecinkami lista pola z możliwością wyszukiwania do przeszukania; Domyślnie wszystkie pola z możliwością wyszukiwania w indeksie. Korzystając z [fielded wyszukiwania](query-lucene-syntax.md#bkmk_fields) w `search` parametru specyfikatory pola zapytania Lucene zastępują wszystkie pola, w tym parametrze. |
| `queryType` | `Edm.String` | `'simple'` lub `'full'`; wartość domyślna to `'simple'`. Określa, jakiego języka zapytania użyto w `search` parametru. |
| `searchMode` | `Edm.String` | `'any'` lub `'all'`, wartość domyślna to `'any'`. Wskazuje, czy niektóre lub wszystkie z wyszukiwania warunków w `search` parametru muszą być zgodne, aby można było policzyć dokumentu jako dopasowanie. Korzystając z [operatorów logicznych Lucene](query-lucene-syntax.md#bkmk_boolean) w `search` parametru one mają wyższy priorytet niż ten parametr. |

Powyższych parametrów są równoważne do odpowiednich [przeszukania parametry żądania interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

`search.ismatch` Funkcja zwraca wartość typu `Edm.Boolean`, co pozwala na została utworzona przy użyciu innych podrzędną wyrażeniach filtru przy użyciu typu Boolean [operatorów logicznych](search-query-odata-logical-operators.md).

> [!NOTE]
> Usługa Azure Search nie obsługuje używania `search.ismatch` lub `search.ismatchscoring` wewnątrz wyrażenia lambda. Oznacza to, że nie jest możliwe filtrów zapisu za pośrednictwem kolekcji obiektów, które można skorelować wyszukiwania pełnotekstowego zgodne z ograniczeniami filtrowania dla tego samego obiektu. Aby uzyskać szczegółowe informacje na temat tego ograniczenia, a także przykłady, zobacz [Rozwiązywanie problemów z kolekcji filtrów w usłudze Azure Search](search-query-troubleshoot-collection-filters.md). Aby uzyskać bardziej szczegółowe informacje o tym, dlaczego to ograniczenie istnieje, zobacz [objaśnienie filtrów kolekcji w usłudze Azure Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Search.ismatchscoring

`search.ismatchscoring` Działanie, takie jak `search.ismatch` funkcja zwraca `true` dokumentów, zgodne z zapytaniem wyszukiwania pełnotekstowego przekazany jako parametr. Różnica między nimi jest, czy wskaźnik istotności wyników pasujących dokumentów `search.ismatchscoring` zapytania przyczyni się do ogólny wynik dokumentu znajduje się w przypadku `search.ismatch`, nie będzie można zmienić na wynik dokumentu. Następujące przeciążenia tej funkcji są dostępne z parametrami identycznych z właściwościami `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Zarówno `search.ismatch` i `search.ismatchscoring` funkcje mogą być używane w tym samym wyrażeniu filtru.

## <a name="examples"></a>Przykłady

Znajdowanie dokumentów z wyrazem "waterfront". To zapytanie filtru jest taka sama jak [żądania wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) z `search=waterfront`.

    search.ismatchscoring('waterfront')

Znajdź dokumenty zawierające słowo "hostel" i ocena większa lub równa 4 czy dokumenty zawierające słowo "motel" i ocena równy 5. Uwaga: to żądanie nie może być wyrażona bez `search.ismatchscoring` funkcji.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Znajdź dokumenty bez słowa "luksusowe".

    not search.ismatch('luxury')

Znajdź dokumenty zawierające frazę "view ocean" lub klasyfikacji równa 5. `search.ismatchscoring` Kwerenda zostanie wykonana tylko względem pól `HotelName` i `Rooms/Description`.

Dokumenty, pasujących do drugiej klauzuli rozłączenia zostaną zwrócone zbyt — hotele z `Rating` równy 5. Aby stał się wyczyścić, że te dokumenty nie pasuje do żadnego z ocenami elementy wyrażenia, zostanie zwrócony wynik jest równy zero.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Znajdowanie dokumentów, których warunki "hotel" i "Kuwejcie" w ciągu 5 słów od siebie nawzajem w opisie hotelu i gdzie palenia nie jest dozwolona w przynajmniej część pomieszczeniach. To zapytanie używa [pełny język zapytań Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Kolejne kroki  

- [Filtry w usłudze Azure Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
