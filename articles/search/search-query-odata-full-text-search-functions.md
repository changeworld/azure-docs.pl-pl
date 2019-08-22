---
title: Dokumentacja funkcji wyszukiwania pełnotekstowego OData-Azure Search
description: Funkcje wyszukiwania pełnotekstowego OData, Search. IsMatch i Search. ismatchscoring w kwerendach Azure Search.
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
ms.openlocfilehash: c3b28c8799b09ddfe008df8539709c5a704ac6b4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648011"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>Funkcja wyszukiwania pełnotekstowego OData w Azure Search `search.ismatch` i`search.ismatchscoring`

Azure Search obsługuje wyszukiwanie pełnotekstowe w kontekście [wyrażeń filtru OData](query-odata-filter-orderby-syntax.md) za pośrednictwem `search.ismatch` funkcji i `search.ismatchscoring` . Te funkcje umożliwiają łączenie wyszukiwania pełnotekstowego z ścisłym filtrowaniem logicznym w sposób, który nie jest możliwy tylko przy użyciu parametru najwyższego `search` poziomu [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> Funkcje `search.ismatch` i`search.ismatchscoring` są obsługiwane tylko w filtrach w [interfejsie API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents). Nie są one obsługiwane w interfejsach API [Sugeruj](https://docs.microsoft.com/rest/api/searchservice/suggestions) lub [Autouzupełnianie](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Składnia

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę `search.ismatch` funkcji and: `search.ismatchscoring`

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

### <a name="searchismatch"></a>Wyszukaj. IsMatch

`search.ismatch` Funkcja oblicza zapytanie wyszukiwania pełnotekstowego jako część wyrażenia filtru. Dokumenty, które pasują do zapytania wyszukiwania, zostaną zwrócone w zestawie wyników. Dostępne są następujące przeciążenia tej funkcji:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametry są zdefiniowane w poniższej tabeli:

| Nazwa parametru | Type | Opis |
| --- | --- | --- |
| `search` | `Edm.String` | Zapytanie wyszukiwania (w prostej [](query-simple-syntax.md) lub [pełnej](query-lucene-syntax.md) składni zapytań Lucene). |
| `searchFields` | `Edm.String` | Rozdzielana przecinkami lista pól wyszukiwania do wyszukania; Domyślnie wszystkie pola z możliwością wyszukiwania w indeksie. W przypadku używania [wyszukiwania z polami](query-lucene-syntax.md#bkmk_fields) w `search` parametrze specyfikatory pola w zapytaniu Lucene zastępują wszystkie pola określone w tym parametrze. |
| `queryType` | `Edm.String` | `'simple'`lub `'full'`; wartość domyślna `'simple'`to. Określa, `search` jaki język zapytań był używany w parametrze. |
| `searchMode` | `Edm.String` | `'any'`lub `'all'`, wartość domyślna `'any'`to. Wskazuje, czy wszystkie lub wszystkie terminy wyszukiwania w `search` parametrze muszą być dopasowane w celu zliczenia dokumentu jako zgodnego. W przypadku korzystania z [operatorów logicznych](query-lucene-syntax.md#bkmk_boolean) "Lucene" w `search` parametrze będą one miały pierwszeństwo przed tym parametrem. |

Wszystkie powyższe parametry są równoważne z odpowiednimi parametrami [żądania wyszukiwania w interfejsie API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Funkcja zwraca wartość typu `Edm.Boolean`, która pozwala na redagowanie jej z innymi wyrażeniami podrzędnymi filtru przy użyciu logicznych [operatorów logicznych.](search-query-odata-logical-operators.md) `search.ismatch`

> [!NOTE]
> Azure Search nie obsługuje użycia `search.ismatch` ani `search.ismatchscoring` wewnątrz wyrażeń lambda. Oznacza to, że nie jest możliwe zapisanie filtrów za pomocą kolekcji obiektów, które mogą skorelować wyniki wyszukiwania pełnotekstowego przy użyciu ścisłych dopasowania filtru dla tego samego obiektu. Aby uzyskać więcej informacji na temat tego ograniczenia, jak również przykładów, zobacz [Rozwiązywanie problemów z filtrami kolekcji w Azure Search](search-query-troubleshoot-collection-filters.md). Aby uzyskać bardziej szczegółowe informacje na temat tego ograniczenia, zobacz [Omówienie filtrów kolekcji w Azure Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Search. ismatchscoring

Funkcja, podobnie jak funkcja, zwraca `true` dla dokumentów, które pasują do zapytania wyszukiwania pełnotekstowego przesłanego jako parametr. `search.ismatch` `search.ismatchscoring` Różnica między nimi polega na tym, że Ocena istotności dokumentów pasujących `search.ismatchscoring` do zapytania będzie współtworzyć ogólny wynik dokumentu, a w `search.ismatch`przypadku nie zostanie zmieniony wynik oceny dokumentu. Następujące przeciążenia tej funkcji są dostępne z parametrami takimi jak `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Obie funkcje `search.ismatchscoring` i mogą być używane w tym samym wyrażeniu filtru. `search.ismatch`

## <a name="examples"></a>Przykłady

Znajdź dokumenty z wyrazem "Waterfront". To zapytanie filtru jest identyczne z [żądaniem wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) w `search=waterfront`.

    search.ismatchscoring('waterfront')

Znajdź dokumenty z wyrazem "Hostel" i klasyfikacją wyższą lub równą 4 lub dokumenty z wyrazem "Motel" i klasyfikacją równą 5. Należy zauważyć, że `search.ismatchscoring` to żądanie nie mogło być wyrażone bez funkcji.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Znajdź dokumenty bez słowa "możliwość zaprojektowania".

    not search.ismatch('luxury')

Znajdź dokumenty z frazą "widok oceanu" lub klasyfikacją równą 5. Zapytanie zostanie wykonane tylko względem pól `HotelName` i `Rooms/Description`. `search.ismatchscoring`

Dokumenty, które pasują tylko do drugiej klauzuli rozłączenia, zostaną zwrócone zbyt--Hotele `Rating` z równymi 5. Aby jasno wymusić, że te dokumenty nie pasują do żadnej z ocenionych części wyrażenia, zostaną zwrócone z wynikiem równym zero.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Znajdź dokumenty, w których terminy "Hotel" i "Lotnisko" znajdują się w 5 wyrazach od siebie w opisie hotelu i gdzie palenie nie jest dozwolone w co najmniej niektórych pokojach. To zapytanie używa [pełnego języka zapytań Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Następne kroki  

- [Filtry w Azure Search](search-filters.md)
- [Przegląd języka wyrażenia OData dla Azure Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;Azure Search interfejs API REST usługi&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
