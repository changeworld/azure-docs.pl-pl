---
title: Dokumentacja funkcji search.in OData
titleSuffix: Azure Cognitive Search
description: Składnia i dokumentacja referencyjna dotycząca korzystania z funkcji search.in w zapytaniach usługi Azure Wyszukiwanie poznawcze.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113126"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Funkcja `search.in` OData na platformie Azure Wyszukiwanie poznawcze

Typowy scenariusz w [wyrażeniach filtru OData](query-odata-filter-orderby-syntax.md) polega na sprawdzeniu, czy pojedyncze pole w każdym dokumencie jest równe jednej z wielu możliwych wartości. Na przykład jest to sposób, w jaki niektóre aplikacje implementują [przycinanie zabezpieczeń](search-security-trimming-for-azure-search.md) — sprawdzając pole zawierające co najmniej jeden identyfikator podmiotu w oparciu o listę identyfikatorów głównych reprezentujących użytkownika wystawiającego zapytanie. Jednym ze sposobów pisania zapytania w tym celu jest użycie operatorów [`eq`](search-query-odata-comparison-operators.md) i [`or`](search-query-odata-logical-operators.md) :

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Istnieje jednak krótszy sposób zapisania tego elementu przy użyciu funkcji `search.in`:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Oprócz krótszej i łatwiejszej odczytu użycie `search.in` zapewnia również [korzyści z wydajności](#bkmk_performance) i pozwala uniknąć pewnych [ograniczeń rozmiaru filtrów](search-query-odata-filter.md#bkmk_limits) , gdy istnieją setki lub nawet tysiące wartości do uwzględnienia w filtrze. Z tego powodu zdecydowanie zalecamy użycie `search.in` zamiast bardziej złożonego rozłączenia wyrażeń równości.

> [!NOTE]
> Wersja 4,01 standardu OData ostatnio wprowadziła [operator`in`](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), który ma podobne zachowanie jako funkcję `search.in` na platformie Azure wyszukiwanie poznawcze. Jednak usługa Azure Wyszukiwanie poznawcze nie obsługuje tego operatora, dlatego należy zamiast tego użyć funkcji `search.in`.

## <a name="syntax"></a>Składnia

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę funkcji `search.in`:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla usługi Azure wyszukiwanie poznawcze](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Funkcja `search.in` sprawdza, czy dana zmienna pola ciągu lub zakresu jest równa jednej z podanej listy wartości. Równość między zmienną a każdą wartością na liście jest określana w sposób uwzględniający wielkość liter, tak samo jak w przypadku operatora `eq`. W związku z tym wyrażenie takie jak `search.in(myfield, 'a, b, c')` jest równoważne `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, z tą różnicą, że `search.in` da znacznie lepszą wydajność.

Istnieją dwa przeciążenia funkcji `search.in`:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametry są zdefiniowane w poniższej tabeli:

| Nazwa parametru | Typ | Opis |
| --- | --- | --- |
| `variable` | `Edm.String` | Odwołanie do pola ciągu (lub zmienna zakresu w polu kolekcji ciągów w przypadku, gdy `search.in` jest używany wewnątrz wyrażenia `any` lub `all`). |
| `valueList` | `Edm.String` | Ciąg zawierający rozdzielaną listę wartości do dopasowania względem parametru `variable`. Jeśli parametr `delimiters` nie zostanie określony, domyślnymi ogranicznikami są spacje i przecinki. |
| `delimiters` | `Edm.String` | Ciąg, w którym każdy znak jest traktowany jako separator podczas analizowania parametru `valueList`. Wartość domyślna tego parametru to `' ,'`, co oznacza, że wszystkie wartości zawierające spacje i/lub przecinki między nimi są oddzielone. Jeśli musisz użyć separatorów innych niż spacje i przecinki, ponieważ wartości te zawierają te znaki, możesz określić alternatywne ograniczniki, takie jak `'|'` w tym parametrze. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Wydajność `search.in`

Jeśli używasz `search.in`, możesz oczekiwać, że czas odpowiedzi podsekundu, gdy drugi parametr zawiera listę setek lub tysięcy wartości. Nie ma żadnego jawnego limitu liczby elementów, które można przekazać do `search.in`, chociaż nadal są ograniczone przez maksymalny rozmiar żądania. Opóźnienie zostanie jednak powiększone w miarę zwiększania się liczby wartości.

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

- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;w interfejsie API REST usługi Azure wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
