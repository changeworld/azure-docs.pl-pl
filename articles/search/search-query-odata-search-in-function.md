---
title: Odwołanie do funkcji search.in OData
titleSuffix: Azure Cognitive Search
description: Składnia i dokumentacja referencyjna do korzystania z funkcji search.in w zapytaniach usługi Azure Cognitive Search.
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113126"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Funkcja OData `search.in` w usłudze Azure Cognitive Search

Typowym scenariuszem w [wyrażeniach filtru OData](query-odata-filter-orderby-syntax.md) jest sprawdzenie, czy pojedyncze pole w każdym dokumencie jest równe jednej z wielu możliwych wartości. Na przykład w ten sposób niektóre aplikacje implementują [przycinanie zabezpieczeń](search-security-trimming-for-azure-search.md) — sprawdzając pole zawierające co najmniej jeden główny identyfikator względem listy identyfikatorów głównych reprezentujących użytkownika wystawiającego kwerendę. Jednym ze sposobów, aby napisać kwerendę w ten sposób jest użycie [`eq`](search-query-odata-comparison-operators.md) i [`or`](search-query-odata-logical-operators.md) operatorów:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Istnieje jednak krótszy sposób, aby to `search.in` napisać, używając funkcji:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Oprócz tego, że jest krótszy `search.in` i łatwiejszy do odczytania, przy użyciu zapewnia również [korzyści wydajności](#bkmk_performance) i pozwala uniknąć pewnych [ograniczeń rozmiaru filtrów,](search-query-odata-filter.md#bkmk_limits) gdy istnieją setki lub nawet tysiące wartości do uwzględnienia w filtrze. Z tego powodu zdecydowanie `search.in` zaleca się używanie zamiast bardziej złożonego rozłączenia wyrażeń równości.

> [!NOTE]
> Wersja 4.01 standardu OData niedawno wprowadziła [ `in` operatora](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), który `search.in` ma podobne zachowanie jak funkcja w usłudze Azure Cognitive Search. Jednak usługa Azure Cognitive Search nie obsługuje tego `search.in` operatora, więc należy użyć tej funkcji.

## <a name="syntax"></a>Składnia

Następujący EBNF[(Rozszerzony formularz Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)definiuje `search.in` gramatykę funkcji:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

Funkcja `search.in` sprawdza, czy dane pole ciągu lub zmienna zakresu jest równa jednej z danej listy wartości. Równość między zmienną a każdą wartością na liście jest określana w `eq` sposób uwzględniający wielkość liter, tak samo jak dla operatora. W związku z `search.in(myfield, 'a, b, c')` tym `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`wyrażenie podobne `search.in` jest równoważne , z tą różnicą, że przyniesie znacznie lepszą wydajność.

Istnieją dwa przeciążenia `search.in` funkcji:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametry są zdefiniowane w poniższej tabeli:

| Nazwa parametru | Typ | Opis |
| --- | --- | --- |
| `variable` | `Edm.String` | Odwołanie do pola ciągu (lub zmiennej zakresu nad `search.in` polem kolekcji ciągów w przypadku, gdy jest używany wewnątrz `any` lub `all` wyrażenia). |
| `valueList` | `Edm.String` | Ciąg zawierający rozdzielaną listę wartości, `variable` które mają być zgodne z parametrem. Jeśli `delimiters` parametr nie jest określony, domyślne ograniczniki są spacja i przecinek. |
| `delimiters` | `Edm.String` | Ciąg, w którym każdy znak jest traktowany jako `valueList` separator podczas analizowania parametru. Domyślną wartością tego `' ,'` parametru jest to, że wszystkie wartości ze spacjami i/lub przecinkami między nimi zostaną rozdzielone. Jeśli konieczne jest użycie separatorów innych niż spacje i przecinki, ponieważ wartości `'|'` zawierają te znaki, można określić alternatywne ograniczniki, takie jak w tym parametrze. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Wykonanie`search.in`

Jeśli używasz `search.in`, można oczekiwać, że czas odpowiedzi podsekundowej, gdy drugi parametr zawiera listę setek lub tysięcy wartości. Nie ma wyraźnego limitu liczby elementów, `search.in`do których można przejść, chociaż nadal jest ograniczony przez maksymalny rozmiar żądania. Jednak opóźnienie wzrośnie wraz ze wzrostem liczby wartości.

## <a name="examples"></a>Przykłady

Znajdź wszystkie hotele o nazwie równej "Motel z widokiem na morze" lub "Hotel budżetowy". Frazy zawierają spacje, które są domyślnym ogranicznikiem. Można określić alternatywny ogranicznik w pojedynczych cudzysłowach jako trzeci parametr ciągu:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Znajdź wszystkie hotele o nazwie równej "Motel z widokiem na morze" lub "Hotel budżetowy" oddzielone "|"):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Znajdź wszystkie hotele z pokojami z tagiem "wifi" lub "wanna":

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Znajdź dopasowanie do zwrotów w kolekcji, takich jak "podgrzewane wieszaki na ręczniki" lub "suszarka do włosów w zestawie" w tagach.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Znajdź wszystkie hotele bez tagu "motel" lub "cabin":

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Następne kroki  

- [Filtry w usłudze Azure Cognitive Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
