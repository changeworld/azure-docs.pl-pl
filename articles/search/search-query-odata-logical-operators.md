---
title: Operator logiczny OData — odwołanie do usługi Azure Search
description: Operatory logiczne OData, a, lub i nie jest, zapytania usługi Azure Search.
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
ms.openlocfilehash: de93765117b4cafe70e5ad277e32ca0a1fa8d90a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079771"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Operatory logiczne OData w usłudze Azure Search - `and`, `or`, `not`

[Wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) w usłudze Azure Search są wyrażenia logiczne, które dają `true` lub `false`. Piszesz złożony filtr, pisząc szereg [prostsze filtry](search-query-odata-comparison-operators.md) i tworzenie ich za pomocą operatorów logicznych z [algebry atrybut typu wartość logiczna](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Binarny operator, który daje w wyniku `true` jeśli mają zarówno jego lewe i prawe Podwyrażenie `true`.
- `or`: Binarny operator, który daje w wyniku `true` Jeśli jeden z jego lewą lub prawą wyrażeń podrzędnych daje w wyniku `true`.
- `not`: Jednoargumentowy operator, który daje w wyniku `true` jeśli jego podrzędnych wyrażenie ma `false`i na odwrót.

Te, wraz z [operatorów kolekcji `any` i `all` ](search-query-odata-collection-operators.md), umożliwiający utworzenie filtry, które można wyrazić bardzo złożone kryteria wyszukiwania.

## <a name="syntax"></a>Składnia

Następujące EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatyki wyrażenia OData, która używa operatorów logicznych.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

Istnieją dwie formy wyrażeń logicznych: binarny (`and`/`or`), jeżeli istnieją dwa wyrażeń podrzędnych i jednoargumentowe (`not`), w którym występuje tylko jeden. Wyrażeń podrzędnych może być wyrażeń logicznych dowolnego rodzaju:

- Zmienne typu pola lub zakresu `Edm.Boolean`
- Funkcje, które zwracają wartości typu `Edm.Boolean`, takie jak `geo.intersects` lub `search.ismatch`
- [Wyrażeniach porównania](search-query-odata-comparison-operators.md), takich jak `rating gt 4`
- [Wyrażenia kolekcji](search-query-odata-collection-operators.md), takich jak `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Wartość logiczna literały `true` lub `false`.
- Inne wyrażenia logiczne, które zostały zbudowane przy użyciu `and`, `or`, i `not`.

> [!IMPORTANT]
> Istnieje kilka sytuacji, gdy nie wszystkie rodzaje wyrażeń podrzędnych może być używany z `and` / `or`, szczególnie w obrębie wyrażenia lambda. Zobacz [operatorów kolekcji OData w usłudze Azure Search](search-query-odata-collection-operators.md#limitations) Aby uzyskać szczegółowe informacje.

### <a name="logical-operators-and-null"></a>Operatory logiczne i `null`

Większość wyrażeń logicznych, takich jak funkcje i porównania nie może utworzyć `null` wartości i operatorów logicznych nie można zastosować do `null` bezpośrednio literał (na przykład `x and null` nie jest dozwolona). Jednak może być pola logicznych `null`, co musisz wiedzieć, jak `and`, `or`, i `not` operatory zachowują się obecności o wartości null. Jest to podsumowane w poniższej tabeli, gdzie `b` jest polem typu `Edm.Boolean`:

| Wyrażenie | Wynik przypadku `b` jest `null` |
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

Gdy pola logicznych `b` pojawia się samodzielnie w wyrażeniu filtru działa tak, jakby były napisane `b eq true`, więc jeśli `b` jest `null`, wyrażenie ma `false`. Podobnie `not b` zachowuje się jak `not (b eq true)`, co daje w wyniku `true`. W ten sposób `null` pól, działa tak samo jak `false`. Jest to zgodne z ich zachowania w połączeniu z innymi wyrażeniami przy użyciu `and` i `or`, jak pokazano w powyższej tabeli. Mimo to bezpośrednie porównanie z `false` (`b eq false`) będą nadal oceniać do `false`. Innymi słowy `null` nie jest równa `false`, nawet jeśli zachowuje się jak je w wyrażeniach logicznych.

## <a name="examples"></a>Przykłady

Dopasowanie dokumenty, gdzie `rating` pole jest od 3 do 5 (włącznie):

    rating ge 3 and rating le 5

Dopasowanie dokumenty, gdzie wszystkie elementy `ratings` pola są mniej niż 3 lub większą niż 5:

    ratings/all(r: r lt 3 or r gt 5)

Dopasowanie dokumenty, gdzie `location` pola znajduje się w danym wielokąta, a dokument nie zawiera termin "publiczne".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Odpowiada dokumentów hoteli w Vancouver, Kanada w przypadku, gdy jest deluxe miejsca na bazie szybkości mniejsza niż 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Kolejne kroki  

- [Filtry w usłudze Azure Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
