---
title: Odwołanie operatora logicznego OData — Azure Search
description: Operatory logiczne OData, i,, i nie, w Azure Search zapytaniach.
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
ms.openlocfilehash: bf4939a40a2fdf1c8fc6cf97beca0184b1604c98
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647995"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Operatory logiczne OData w Azure Search- `and`, `or`,`not`

[Wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) w Azure Search są wyrażeniami logicznymi, `true` które `false`są szacowane do lub. Filtr złożony można napisać, pisząc szereg [prostszych filtrów](search-query-odata-comparison-operators.md) i tworząc je przy użyciu operatorów logicznych z [algebry logicznego](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Operator binarny, który jest obliczany `true` , jeśli oba wyrażenia podrzędne i po prawej stronie są oceniane do. `true`
- `or`: Operator binarny, który jest obliczany `true` , jeśli jedno z jego lewy lub prawego podwyrażenia oblicza wartość. `true`
- `not`: Jednoargumentowy operator, który jest `true` obliczany w przypadku, gdy jego Podwyrażenie ma wartość, i na `false`odwrót.

Te, razem z [ `any` operatorami kolekcji i `all` ](search-query-odata-collection-operators.md), umożliwiają konstruowanie filtrów, które mogą wyrażać bardzo złożone kryteria wyszukiwania.

## <a name="syntax"></a>Składnia

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę wyrażenia OData, które używa operatorów logicznych.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Istnieją dwie formy wyrażeń`and`logicznych: binary (/`or`), gdzie istnieją dwa wyrażenia podrzędne i jednoargumentowy (`not`), gdzie istnieje tylko jeden. Wyrażenia podrzędne mogą być wyrażeniami logicznymi dowolnego rodzaju:

- Pola lub zmienne zakresu typu`Edm.Boolean`
- Funkcje, które zwracają wartości typu `Edm.Boolean`, takie jak `geo.intersects` lub`search.ismatch`
- [Wyrażenia porównania](search-query-odata-comparison-operators.md), takie jak`rating gt 4`
- [Wyrażenia kolekcji](search-query-odata-collection-operators.md), takie jak`Rooms/any(room: room/Type eq 'Deluxe Room')`
- Literały `true` logiczne lub `false`.
- Inne wyrażenia logiczne konstruowane przy `and`użyciu `or`,, `not`i.

> [!IMPORTANT]
> Istnieją sytuacje, w których nie można używać wszystkich rodzajów wyrażeń podrzędnych z `and` / `or`, szczególnie w wyrażeniach lambda. Aby uzyskać szczegółowe informacje, zobacz [Operatory kolekcji OData w Azure Search](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Operatory logiczne i`null`

Większość wyrażeń logicznych, takich jak Functions i porównania `null` , nie może generować wartości, a operatory logiczne nie mogą `null` być stosowane do literału `x and null` bezpośrednio (na przykład nie jest to dozwolone). Jednak pola `null`logiczne mogą być, dlatego należy wiedzieć, `and`jak operatory, `or`i `not` działają w obecności wartości null. Jest to podsumowanie w poniższej tabeli, gdzie `b` to pole typu: `Edm.Boolean`

| Wyrażenie | Wynik gdy `b` jest`null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Gdy `b` pole logiczne jest wyświetlane przez siebie w wyrażeniu filtru, zachowuje się tak, jakby zostało zapisaniu `b eq true`, więc jeśli `b` jest `null`, wyrażenie daje `false`w wyniku. Podobnie, `not b` zachowuje się jak `not (b eq true)`, `true`dlatego jest oceniane. W ten sposób `null` pola zachowują się tak samo `false`jak. Jest to zgodne z zachowaniem ich w połączeniu z innymi wyrażeniami `and` przy `or`użyciu i, jak pokazano w powyższej tabeli. Pomimo tego, bezpośrednie porównanie z `false` (`b eq false` `false`) będzie nadal oceniane. Innymi słowy, `null` nie jest `false`równe, chociaż zachowuje się tak jak w wyrażeniach logicznych.

## <a name="examples"></a>Przykłady

Dopasowuje dokumenty, `rating` gdy pole jest z zakresu od 3 do 5 włącznie:

    rating ge 3 and rating le 5

Dopasowuje dokumenty, w `ratings` których wszystkie elementy pola są mniejsze niż 3 lub większe niż 5:

    ratings/all(r: r lt 3 or r gt 5)

Dopasowuje dokumenty, `location` w których pole znajduje się w danym wielokąta, a dokument nie zawiera terminu "Public".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Dopasowuj dokumenty dla hoteli w Vancouver, Kanada, gdy istnieje Pokój Deluxe z stawką bazową mniejszą niż 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Następne kroki  

- [Filtry w Azure Search](search-filters.md)
- [Przegląd języka wyrażenia OData dla Azure Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;Azure Search interfejs API REST usługi&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
