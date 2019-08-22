---
title: Dokumentacja funkcji search.in OData — Azure Search
description: Funkcja search.in OData w zapytaniach Azure Search.
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
ms.openlocfilehash: 8bac0205fa2de8378abaa4d9e8ba8e05ea69192e
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647936"
---
# <a name="odata-searchin-function-in-azure-search"></a>Funkcja `search.in` OData w Azure Search

Typowy scenariusz w [wyrażeniach filtru OData](query-odata-filter-orderby-syntax.md) polega na sprawdzeniu, czy pojedyncze pole w każdym dokumencie jest równe jednej z wielu możliwych wartości. Na przykład jest to sposób, w jaki niektóre aplikacje implementują [przycinanie zabezpieczeń](search-security-trimming-for-azure-search.md) — sprawdzając pole zawierające co najmniej jeden identyfikator podmiotu w oparciu o listę identyfikatorów głównych reprezentujących użytkownika wystawiającego zapytanie. Jednym ze sposobów pisania zapytania w tym celu jest użycie [`eq`](search-query-odata-comparison-operators.md) operatorów i: [`or`](search-query-odata-logical-operators.md)

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Istnieje jednak krótszy sposób zapisania tego elementu przy użyciu `search.in` funkcji:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Poza krótszym i łatwiejszym do odczytu, `search.in` użycie także zapewnia [korzyści z wydajności](#bkmk_performance) i pozwala uniknąć pewnych [ograniczeń rozmiaru filtrów](search-query-odata-filter.md#bkmk_limits) , gdy istnieją setki lub nawet tysiące wartości do uwzględnienia w filtrze. Z tego powodu zdecydowanie zalecamy użycie `search.in` zamiast bardziej złożonego rozłączenia wyrażeń równości.

> [!NOTE]
> Wersja 4,01 standardu OData ostatnio wprowadziła [ `in` operator](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), który ma `search.in` podobne zachowanie jak funkcja w Azure Search. Jednak Azure Search nie obsługuje tego operatora, dlatego należy zamiast tego użyć `search.in` funkcji.

## <a name="syntax"></a>Składnia

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę `search.in` funkcji:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Funkcja `search.in` sprawdza, czy dane pole ciągu lub zmienna zakresu są równe jednej z podanej listy wartości. Równość między zmienną a każdą wartością na liście jest określana w sposób uwzględniający wielkość liter, tak samo jak w przypadku `eq` operatora. W związku z tym `search.in(myfield, 'a, b, c')` wyrażenie takie jak `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`jest równoważne z `search.in` , z tą różnicą, że daje znacznie lepszą wydajność.

Istnieją dwa przeciążenia `search.in` funkcji:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametry są zdefiniowane w poniższej tabeli:

| Nazwa parametru | Type | Opis |
| --- | --- | --- |
| `variable` | `Edm.String` | Odwołanie do pola ciągu (lub zmienna zakresu w polu kolekcji ciągów w przypadku, gdzie `search.in` jest używana `any` wewnątrz wyrażenia or `all` ). |
| `valueList` | `Edm.String` | Ciąg zawierający rozdzielaną listę wartości, które mają być zgodne `variable` z parametrem. `delimiters` Jeśli parametr nie jest określony, domyślne ograniczniki są spacjami i przecinkami. |
| `delimiters` | `Edm.String` | Ciąg, w którym każdy znak jest traktowany jako separator podczas analizowania `valueList` parametru. Wartość domyślna tego parametru `' ,'` to oznacza, że wszystkie wartości zawierające spacje i/lub przecinki między nimi są rozdzielone. Jeśli musisz użyć separatorów innych niż spacje i przecinki, ponieważ wartości te zawierają te znaki, możesz określić alternatywne ograniczniki, takie jak `'|'` w tym parametrze. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Wydajność`search.in`

Jeśli używasz `search.in`, możesz oczekiwać, że czas odpowiedzi podsekundu, gdy drugi parametr zawiera listę setek lub tysięcy wartości. Nie ma żadnego jawnego limitu liczby elementów `search.in`, które można przekazać, chociaż nadal ograniczono limit rozmiaru maksymalnego żądania. Opóźnienie zostanie jednak powiększone w miarę zwiększania się liczby wartości.

## <a name="examples"></a>Przykłady

Znajdź wszystkie hotele z nazwą równą "Sea View Motel" lub "hotelem". Wyrażenia zawierają spacje, które są domyślnymi ogranicznikami. Można określić alternatywny ogranicznik w pojedynczym cudzysłowie jako trzeci parametr ciągu:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Znajdź wszystkie hotele z nazwą równą "Sea View Motel" lub "hotelem" oddzielonym przez "|"):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Znajdź wszystkie hotele z pokojach, które mają tag "Wi-Fi" lub "wanny":

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Znajdź dopasowanie do fraz w kolekcji, takich jak "ogrzewane Stojaki ręczników" lub "hairdryer dołączone" w tagach.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Znajdź wszystkie hotele bez tagu "Motel" lub "cabin':

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Następne kroki  

- [Filtry w Azure Search](search-filters.md)
- [Przegląd języka wyrażenia OData dla Azure Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;Azure Search interfejs API REST usługi&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
