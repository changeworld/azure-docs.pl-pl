---
title: Odwołanie do operatora logicznego OData
titleSuffix: Azure Cognitive Search
description: Składnia i dokumentacja referencyjna do korzystania z operatorów logicznych OData i lub i nie w zapytaniach usługi Azure Cognitive Search.
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
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113186"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Operatory logiczne OData `and`w `or`usłudze Azure Cognitive Search - , ,`not`

[Wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) w usłudze Azure Cognitive `true` Search `false`są wyrażeniami logicznymi, które oceniają lub . Można napisać złożony filtr, pisząc serię [prostszych filtrów](search-query-odata-comparison-operators.md) i redagując je za pomocą operatorów logicznych z [algebry logicznej:](https://en.wikipedia.org/wiki/Boolean_algebra)

- `and`: Operator binarny, `true` który ocenia, czy zarówno jego `true`lewe, jak i prawe wyrażenia podrzędne oceniają na .
- `or`: Operator binarny, `true` który ocenia, czy jedno z lewych lub `true`prawych wyrażeń podrzędnych ocenia wartość .
- `not`: Operator dwuwyrazowy, `true` który ocenia, czy `false`jego podwyrażenie ocenia na , i odwrotnie.

Te, wraz z [operatorami `any` kolekcji i `all` ](search-query-odata-collection-operators.md), umożliwiają konstruowanie filtrów, które mogą wyrażać bardzo złożone kryteria wyszukiwania.

## <a name="syntax"></a>Składnia

Następujący EBNF[(Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę wyrażenia OData, które używa operatorów logicznych.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

Istnieją dwie formy wyrażeń logicznych: binarne (`and`/`or`), gdzie istnieją`not`dwa wyrażenia podrzędne i jednoarzędne ( ), gdzie istnieje tylko jedna. Wyrażenia podrzędne mogą być wyrażeniami logicznymi dowolnego rodzaju:

- Pola lub zmienne zakresu typu`Edm.Boolean`
- Funkcje zwracające `Edm.Boolean`wartości typu, takie jak `geo.intersects` lub`search.ismatch`
- [Wyrażenia porównawcze,](search-query-odata-comparison-operators.md)takie jak`rating gt 4`
- [Wyrażenia kolekcji,](search-query-odata-collection-operators.md)takie jak`Rooms/any(room: room/Type eq 'Deluxe Room')`
- Literały logiczne `true` lub `false`.
- Inne wyrażenia logiczne `and`skonstruowane przy użyciu , `or`i `not`.

> [!IMPORTANT]
> Istnieją sytuacje, w których nie wszystkie rodzaje wyrażenia `and` / `or`podrzędnego mogą być używane z , szczególnie wewnątrz lambda wyrażeń. Zobacz [operatorów kolekcji OData w usłudze Azure Cognitive Search, aby](search-query-odata-collection-operators.md#limitations) uzyskać szczegółowe informacje.

### <a name="logical-operators-and-null"></a>Operatory logiczne i`null`

Większość wyrażeń logicznych, `null` `null` `x and null` takich jak funkcje i porównania nie może tworzyć wartości, a operatory logiczne nie mogą być stosowane bezpośrednio do literału (na przykład nie jest dozwolone). Jednak pola logiczne `null`mogą być , więc trzeba `and`być `or`świadomym, jak , i `not` operatorów zachowują się w obecności null. Jest to podsumowane w poniższej tabeli, gdzie `b` jest pole typu: `Edm.Boolean`

| Wyrażenie | Wynik, `b` gdy jest`null` |
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

`b` Gdy pole logiczne pojawia się samo w wyrażeniu filtru, `b eq true`zachowuje `b` się `null`tak, jakby `false`zostało napisane , więc jeśli tak jest, wyrażenie jest obliczane na . Podobnie zachowuje `not b` się `not (b eq true)`jak , więc `true`ocenia . W ten `null` sposób pola zachowują `false`się tak samo jak . Jest to zgodne z zachowaniem ich zachowania `and` `or`w połączeniu z innymi wyrażeniami przy użyciu i , jak pokazano w powyższej tabeli. Mimo to, bezpośrednie `false` porównanie`b eq false`do ( `false`) będzie nadal oceniać . Innymi słowy, `null` nie jest `false`równa , nawet jeśli zachowuje się jak w wyrażeniach logicznych.

## <a name="examples"></a>Przykłady

Dopasuj `rating` dokumenty, w których pole znajduje się od 3 do 5, włącznie:

    rating ge 3 and rating le 5

Dopasuj dokumenty, `ratings` w których wszystkie elementy pola są mniejsze niż 3 lub większe niż 5:

    ratings/all(r: r lt 3 or r gt 5)

Dopasuj `location` dokumenty, w których pole znajduje się w obrębie danego wielokąta, a dokument nie zawiera terminu "public".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Dokumenty meczowe dla hoteli w Vancouver, Kanada, gdzie znajduje się pokój deluxe o podstawowej cenie mniejszej niż 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Następne kroki  

- [Filtry w usłudze Azure Cognitive Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
