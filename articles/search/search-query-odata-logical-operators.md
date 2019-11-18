---
title: Odwołanie operatora logicznego OData
titleSuffix: Azure Cognitive Search
description: Składnia i dokumentacja referencyjna dotycząca korzystania z operatorów logicznych OData, oraz, lub, a nie w przypadku zapytań usługi Azure Wyszukiwanie poznawcze.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113186"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Operatory logiczne OData w usłudze Azure Wyszukiwanie poznawcze — `and`, `or``not`

[Wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) na platformie Azure wyszukiwanie poznawcze są wyrażeniami logicznymi, które są szacowane do `true` lub `false`. Filtr złożony można napisać, pisząc szereg [prostszych filtrów](search-query-odata-comparison-operators.md) i tworząc je przy użyciu operatorów logicznych z [algebry logicznego](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: operator binarny, który jest obliczany do `true`, jeśli oba wyrażenia podrzędne i po prawej stronie są oceniane do `true`.
- `or`: operator binarny, który jest obliczany do `true`, jeśli jedno z jego lewy lub prawego podwyrażenia szacuje się w `true`.
- `not`: jednoargumentowy operator, który oblicza `true`, jeśli jego Podwyrażenie ma wartość `false`i na odwrót.

Są to między innymi [Operatory kolekcji `any` i `all`](search-query-odata-collection-operators.md)umożliwiają konstruowanie filtrów, które mogą wyrażać bardzo złożone kryteria wyszukiwania.

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
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla usługi Azure wyszukiwanie poznawcze](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Istnieją dwie formy wyrażeń logicznych: dane binarne (`and`/`or`), gdzie istnieją dwa wyrażenia podrzędne i jednoargumentowe (`not`), gdzie istnieje tylko jeden. Wyrażenia podrzędne mogą być wyrażeniami logicznymi dowolnego rodzaju:

- Pola lub zmienne zakresów typu `Edm.Boolean`
- Funkcje, które zwracają wartości typu `Edm.Boolean`, takie jak `geo.intersects` lub `search.ismatch`
- [Wyrażenia porównania](search-query-odata-comparison-operators.md), takie jak `rating gt 4`
- [Wyrażenia kolekcji](search-query-odata-collection-operators.md), takie jak `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Literały logiczne `true` lub `false`.
- Inne wyrażenia logiczne konstruowane przy użyciu `and`, `or`i `not`.

> [!IMPORTANT]
> Istnieją sytuacje, w których nie można używać wszystkich rodzajów wyrażeń podrzędnych z `and`/`or`, w szczególności w wyrażeniach lambda. Aby uzyskać szczegółowe informacje, zobacz [Operatory kolekcji OData w usłudze Azure wyszukiwanie poznawcze](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Operatory logiczne i `null`

Większość wyrażeń logicznych, takich jak Functions i porównań, nie może generować wartości `null` i operatory logiczne nie mogą być stosowane bezpośrednio do `null` Literal (na przykład `x and null` nie jest dozwolone). Jednak pola logiczne mogą być `null`, dlatego należy wiedzieć, jak operatory `and`, `or`i `not` działają w obecności wartości null. Jest to podsumowanie w poniższej tabeli, gdzie `b` jest polem typu `Edm.Boolean`:

| Wyrażenie | Wynik po `null` `b` |
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

Gdy pole logiczne `b` wyświetlane w wyrażeniu filtru, działa tak, jakby zostało zapisaną `b eq true`, więc jeśli `b` jest `null`, wyrażenie oblicza `false`. Podobnie `not b` zachowuje się jak `not (b eq true)`, dlatego zostanie oszacowany `true`. W ten sposób pola `null` zachowują się tak samo jak `false`. Jest to zgodne z zachowaniem ich w połączeniu z innymi wyrażeniami przy użyciu `and` i `or`, jak pokazano w powyższej tabeli. Pomimo tego bezpośrednie porównanie `false` (`b eq false`) będzie nadal oceniane do `false`. Innymi słowy, `null` nie jest równa `false`, chociaż zachowuje się tak jak w wyrażeniach logicznych.

## <a name="examples"></a>Przykłady

Dopasuj dokumenty, w których pole `rating` ma wartość od 3 do 5 włącznie:

    rating ge 3 and rating le 5

Dopasuj dokumenty, w których wszystkie elementy pola `ratings` są mniejsze niż 3 lub większe niż 5:

    ratings/all(r: r lt 3 or r gt 5)

Dopasowuje dokumenty, w których pole `location` znajduje się w obrębie danego wielokąta, a dokument nie zawiera terminu "Public".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Dopasowuj dokumenty dla hoteli w Vancouver, Kanada, gdy istnieje Pokój Deluxe z stawką bazową mniejszą niż 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Następne kroki  

- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;w interfejsie API REST usługi Azure wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
