---
title: Odwołanie do funkcji search.in OData — usługa Azure Search
description: Funkcja search.in OData w zapytaniach usługi Azure Search.
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
ms.openlocfilehash: a61291e547021077341a5f1b3db7422afa5b9440
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449987"
---
# <a name="odata-searchin-function-in-azure-search"></a>OData `search.in` funkcji w usłudze Azure Search

Typowy scenariusz w [wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) jest sprawdzenie, czy jednego pola w poszczególnych dokumentów jest równa jedną z wielu możliwych wartości. Na przykład, jest to, jak zaimplementować niektóre aplikacje [dostosowanie do zabezpieczeń](search-security-trimming-for-azure-search.md) —, zaznaczając pole zawierające jeden lub więcej identyfikatorów jednostki z listą identyfikatorów jednostki reprezentujący użytkownika zapytania. Jednym ze sposobów, aby zapisać zapytanie takie jak to jest użycie [ `eq` ](search-query-odata-comparison-operators.md) i [ `or` ](search-query-odata-logical-operators.md) operatory:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Istnieje jednak krótszy sposobem pisania tego za pomocą `search.in` funkcji:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Oprócz są krótsze i łatwiejsze do odczytania, za pomocą `search.in` udostępnia również [korzyści wydajności](#bkmk_performance) i pozwalają uniknąć pewnych [rozmiar ograniczenia filtry](search-query-odata-filter.md#bkmk_limits) przypadku setek lub nawet tysięcy wartości Aby uwzględnić w filtrze. Z tego powodu zdecydowanie zalecamy używanie `search.in` zamiast alternatywa bardziej złożonych wyrażeń równości.

> [!NOTE]
> Ostatnio wprowadził wersji 4.01 ze standardu OData [ `in` operator](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), który ma podobne zachowanie jako `search.in` funkcji w usłudze Azure Search. Jednak usługi Azure Search nie obsługuje tego operatora, więc należy użyć `search.in` zamiast tego funkcji.

## <a name="syntax"></a>Składnia

Następujące EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatyki `search.in` funkcji:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

`search.in` Funkcja sprawdza, czy pole dany ciąg znaków lub zmienna zakresu jest równa jeden z danej listy wartości. Równość zmiennej i każdej wartości na liście jest określana w czasie wielkość liter, taki sam sposób jak w przypadku `eq` operatora. W związku z tym, takie jak wyrażenie `search.in(myfield, 'a, b, c')` jest odpowiednikiem `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, chyba że `search.in` przyniesie znacznie lepszej wydajności.

Istnieją dwa przeciążenia `search.in` funkcji:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametry są zdefiniowane w poniższej tabeli:

| Nazwa parametru | Typ | Opis |
| --- | --- | --- |
| `variable` | `Edm.String` | Odwołanie do pola ciągu (lub zmiennej zakresu nad polem ciągu kolekcji, w przypadku których `search.in` jest używana wewnątrz `any` lub `all` wyrażenia). |
| `valueList` | `Edm.String` | Ciąg zawierający rozdzielaną listę wartości, które mają być dopasowywane `variable` parametru. Jeśli `delimiters` parametr nie zostanie określony, domyślne ograniczniki są miejsce i przecinkami. |
| `delimiters` | `Edm.String` | Ciąg, gdzie każdy znak jest traktowany jako separator, podczas analizowania `valueList` parametru. Wartość domyślna tego parametru to `' ,'` co oznacza, że będzie rozdzielone wartościami miejsca do magazynowania i/lub oddzielając je przecinkami. Należy użyć separatory niż spacje i przecinki, ponieważ wartości zawiera tych znaków, możesz takich jak określić alternatywne ograniczniki `'|'` w tym parametrze. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Wydajność `search.in`

Jeśli używasz `search.in`, można oczekiwać, że gdy drugi parametr zawiera listę setek lub tysięcy wartości czas odpowiedzi sekundy. Nie ma żadnego limitu jawne na liczbę elementów, które można przekazać do `search.in`, chociaż można nadal obowiązują ograniczenia rozmiaru maksymalnego żądania. Jednak opóźnienie rośnie wraz ze wzrostem natężenia liczbę wartości.

## <a name="examples"></a>Przykłady

Znajdź wszystkie hotele o nazwie równa "Widok Morza motel" lub "Budżetu hotel". Zwroty zawierają spacje, który jest domyślnym ogranicznikiem. Możesz określić alternatywne ogranicznika w pojedynczym cudzysłowie jako trzeci parametr ciągu:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Znajdź wszystkie hotele o nazwie równa "Widok Morza motel" lub "Budżetu hotel" rozdzielone "|"):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Znajdź wszystkie hotele z pokoje, do których mają tag "Wi-Fi" lub "miarki":

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Znajdź dopasowanie fraz w obrębie kolekcji, takie jak "podgrzewanego ręczników stojakami" lub uwzględnione hairdryer w znacznikach.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Znajdź wszystkie hotele bez tagu "motel" i "podręcznego":

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Kolejne kroki  

- [Filtry w usłudze Azure Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
