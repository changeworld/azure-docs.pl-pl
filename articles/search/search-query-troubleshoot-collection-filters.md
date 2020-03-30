---
title: Rozwiązywanie problemów z filtrami kolekcji OData
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak rozwiązać błędy filtru kolekcji OData w zapytaniach usługi Azure Cognitive Search.
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
ms.openlocfilehash: e82fa00226c964d5ba774cdf06f5b0f3898bdc55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113083"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Rozwiązywanie problemów z filtrami kolekcji OData w usłudze Azure Cognitive Search

Aby [filtrować](query-odata-filter-orderby-syntax.md) pola kolekcji w usłudze Azure Cognitive Search, można użyć [ `any` operatorów `all` i razem](search-query-odata-collection-operators.md) z **wyrażeniami lambda**. Wyrażenie lambda jest filtrem podrzędnym, który jest stosowany do każdego elementu kolekcji.

Nie każda funkcja wyrażeń filtru jest dostępna wewnątrz wyrażenia lambda. Które funkcje są dostępne różni się w zależności od typu danych pola kolekcji, które chcesz filtrować. Może to spowodować błąd, jeśli spróbujesz użyć funkcji w wyrażeniu lambda, który nie jest obsługiwany w tym kontekście. Jeśli napotkasz takie błędy podczas próby zapisania złożonego filtru nad polami kolekcji, ten artykuł pomoże Ci rozwiązać problem.

## <a name="common-collection-filter-errors"></a>Typowe błędy filtru kolekcji

W poniższej tabeli wymieniono błędy, które mogą wystąpić podczas próby wykonania filtru kolekcji. Te błędy występują, gdy używasz funkcji wyrażeń filtru, która nie jest obsługiwana wewnątrz wyrażenia lambda. Każdy błąd zawiera pewne wskazówki dotyczące sposobu przepisywania filtru, aby uniknąć błędu. Tabela zawiera również łącze do odpowiedniej sekcji tego artykułu, który zawiera więcej informacji na temat unikania tego błędu.

| Komunikat o błędzie | Sytuacji | Aby uzyskać więcej informacji, zobacz |
| --- | --- | --- |
| Funkcja "ismatch" nie ma parametrów powiązanych ze zmienną zakresu 's'. Tylko odwołania do pól powiązanych są obsługiwane wewnątrz wyrażeń lambda ("dowolne" lub "wszystkie"). Zmień filtr tak, aby funkcja "ismatch" znajduje się poza wyrażeniem lambda i spróbuj ponownie. | Używanie `search.ismatch` `search.ismatchscoring` wyrażenia lambda lub wewnątrz niego | [Reguły filtrowania kolekcji złożonych](#bkmk_complex) |
| Nieprawidłowe wyrażenie lambda. Znaleziono test równości lub nierówności, w którym oczekiwano odwrotnego stanu w wyrażeniu lambda, które iteruje nad polem typu Collection(Edm.String). W przypadku "dowolnych" należy użyć wyrażeń formularza "x eq y" lub "search.in(...)". W przypadku "wszystkich" należy użyć wyrażeń w formie "x ne y", "not (x eq y)" lub "not search.in(...)". | Filtrowanie w polu typu`Collection(Edm.String)` | [Reguły filtrowania kolekcji ciągów](#bkmk_strings) |
| Nieprawidłowe wyrażenie lambda. Znaleziono nieobsługiconą formę złożonego wyrażenia logicznego. W przypadku "dowolnych" należy użyć wyrażeń, które są "ORs ands", znany również jako disjunctive normalnej formie. Na przykład: 'a i b) lub (c i d)", gdzie a, b, c i d są porównania lub równości podwyrażeń. W przypadku "wszystkich" należy użyć wyrażeń, które są "ANDs of ORs", znany również jako conjunctive normalnej formie. Na przykład: 'a lub b) i (c lub d)", gdzie a, b, c i d są porównaniami lub wyrażeniami podrzędnymi nierówności. Przykłady wyrażeń porównawczych: 'x gt 5', 'x le 2'. Przykład wyrażenia równości: 'x eq 5'. Przykład wyrażenia nierówności: 'x ne 5'. | Filtrowanie pól typu `Collection(Edm.DateTimeOffset)` `Collection(Edm.Double)`, `Collection(Edm.Int32)`, lub`Collection(Edm.Int64)` | [Reguły filtrowania porównywalnych kolekcji](#bkmk_comparables) |
| Nieprawidłowe wyrażenie lambda. Znaleziono nieobsługitowane użycie geo.distance() lub geo.intersects() w wyrażeniu lambda, które iteruje nad polem typu Kolekcja(Edm.GeographyPoint). W przypadku "dowolnych" należy porównać geo.distance() przy użyciu operatorów 'lt' lub 'le' i upewnić się, że jakiekolwiek użycie geo.intersects() nie jest negowane. W przypadku "wszystkich" należy porównać geo.distance() za pomocą operatorów 'gt' lub 'ge' i upewnić się, że jakiekolwiek użycie geo.intersects() jest zanegowane. | Filtrowanie w polu typu`Collection(Edm.GeographyPoint)` | [Reguły filtrowania kolekcji programu GeographyPoint](#bkmk_geopoints) |
| Nieprawidłowe wyrażenie lambda. Złożone wyrażenia logiczne nie są obsługiwane w wyrażeniach lambda, które iterują względem pól typu Kolekcja(Edm.GeographyPoint). W przypadku "dowolnych" proszę dołączyć do podwyrażeń z "lub"; "i" nie jest obsługiwany. W przypadku "wszystkich" proszę dołączyć do wyrażeń podrzędnych z "i"; "lub" nie jest obsługiwany. | Filtrowanie pól typu `Collection(Edm.String)` lub`Collection(Edm.GeographyPoint)` | [Reguły filtrowania kolekcji ciągów](#bkmk_strings) <br/><br/> [Reguły filtrowania kolekcji programu GeographyPoint](#bkmk_geopoints) |
| Nieprawidłowe wyrażenie lambda. Znaleziono operatora porównania (jeden z "lt", "le", "gt" lub "ge"). Tylko operatory równości są dozwolone w wyrażeniach lambda, które iterują nad polami typu Kolekcja(Edm.String). W przypadku "dowolnych" należy użyć wyrażeń formularza "x eq y". W przypadku "wszystkich" należy użyć wyrażeń formularza "x ne y" lub "not (x eq y)". | Filtrowanie w polu typu`Collection(Edm.String)` | [Reguły filtrowania kolekcji ciągów](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Jak napisać prawidłowe filtry kolekcji

Reguły pisania prawidłowych filtrów kolekcji są różne dla każdego typu danych. W poniższych sekcjach opisano reguły, przedstawiając przykłady, które funkcje filtru są obsługiwane, a które nie:

- [Reguły filtrowania kolekcji ciągów](#bkmk_strings)
- [Reguły filtrowania kolekcji logicznych](#bkmk_bools)
- [Reguły filtrowania kolekcji programu GeographyPoint](#bkmk_geopoints)
- [Reguły filtrowania porównywalnych kolekcji](#bkmk_comparables)
- [Reguły filtrowania kolekcji złożonych](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Reguły filtrowania kolekcji ciągów

Wewnątrz wyrażeń lambda dla kolekcji ciągów jedynymi `eq` `ne`operatorami porównania, które mogą być używane, są i .

> [!NOTE]
> Usługa Azure Cognitive Search `lt` / `le` / `gt` / `ge` nie obsługuje operatorów ciągów, zarówno wewnątrz, jak i na zewnątrz wyrażenia lambda.

Treść `any` może tylko test równości, podczas gdy `all` treść może tylko test na nierówności.

Możliwe jest również łączenie wielu wyrażeń `or` za `any`pośrednictwem w `and` ciele , `all`i za pośrednictwem w ciele . Ponieważ `search.in` funkcja jest równoważna łączeniu `or`kontroli równości z , jest również `any`dozwolona w treści . Z drugiej `not search.in` strony, jest dozwolone `all`w treści .

Na przykład te wyrażenia są dozwolone:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

podczas gdy te wyrażenia nie są dozwolone:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Reguły filtrowania kolekcji logicznych

Typ `Edm.Boolean` obsługuje tylko `eq` `ne` operatorów i. W związku z tym nie ma większego sensu zezwalać na łączenie takich `and` / `or` klauzul, które sprawdzają tę samą zmienną zakresu, ponieważ zawsze prowadziłoby to do tautologii lub sprzeczności.

Oto kilka przykładów filtrów w kolekcjach logicznych, które są dozwolone:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

W przeciwieństwie do kolekcji ciągów kolekcje logiczne nie mają żadnych ograniczeń, na których operator może być używany, w którym typ wyrażenia lambda. Zarówno `eq` `ne` i może być stosowany `any` `all`w organizmie lub .

Wyrażenia, takie jak następujące, nie są dozwolone dla kolekcji logicznych:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Reguły filtrowania kolekcji programu GeographyPoint

Wartości typu `Edm.GeographyPoint` w kolekcji nie można porównywać bezpośrednio ze sobą. Zamiast tego muszą być używane jako `geo.distance` `geo.intersects` parametry i funkcje. Funkcja `geo.distance` z kolei musi być porównywana z wartością `lt` `le`odległości `gt`przy `ge`użyciu jednego z operatorów porównania , lub . Reguły te mają również zastosowanie do pól Edm.GeographyPoint niezbierania.

Podobnie jak kolekcje ciągów, `Edm.GeographyPoint` kolekcje mają pewne reguły dotyczące sposobu, w jaki funkcje geoprzestrzenne mogą być używane i łączone w różnych typach wyrażeń lambda:

- Operatory porównania, których `geo.distance` można używać z funkcją, zależą od typu wyrażenia lambda. Dla `any`, można `lt` używać `le`tylko lub . Dla `all`, można `gt` używać `ge`tylko lub . Można zanegować wyrażenia `geo.distance`z udziałem , ale trzeba będzie`geo.distance(...) lt x` `not (geo.distance(...) ge x)` zmienić operator porównania ( staje się i `geo.distance(...) le x` staje się `not (geo.distance(...) gt x)`).
- W treści `all`, `geo.intersects` funkcja musi być zanegowana. Z drugiej strony, w `any`treści `geo.intersects` , funkcja nie może być zanegowana.
- W treści `any`wyrażenia geoprzestrzenne można łączyć za pomocą . `or` W treści `all`, takie wyrażenia mogą być `and`łączone za pomocą .

Powyższe ograniczenia istnieją z podobnych powodów, jak ograniczenie równości/nierówności w kolekcjach ciągów. Zobacz [Opis filtrów kolekcji OData w usłudze Azure Cognitive Search, aby](search-query-understand-collection-filters.md) dokładniej przyjrzeć się tym przyczynom.

Oto kilka przykładów filtrów `Edm.GeographyPoint` w kolekcjach, które są dozwolone:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Wyrażenia, takie jak następujące nie `Edm.GeographyPoint` są dozwolone dla kolekcji:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Reguły filtrowania porównywalnych kolekcji

Ta sekcja dotyczy wszystkich następujących typów danych:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Typy `Edm.Int32` takie `Edm.DateTimeOffset` jak i obsługują wszystkie `eq` `ne`sześć `lt` `le`operatorów porównawczych: , , , , `gt`i `ge`. Wyrażenia Lambda nad kolekcjami tych typów mogą zawierać proste wyrażenia przy użyciu dowolnego z tych operatorów. Dotyczy to zarówno `any` `all`i . Na przykład te filtry są dozwolone:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Istnieją jednak ograniczenia dotyczące sposobu łączenia takich wyrażeń porównawczych w bardziej złożone wyrażenia wewnątrz wyrażenia lambda:

- Zasady `any`:
  - Proste wyrażenia nierówności nie mogą być pożytecznie łączone z innymi wyrażeniami. Na przykład to wyrażenie jest dozwolone:
    - `ratings/any(r: r ne 5)`

    ale to wyrażenie nie jest:
    - `ratings/any(r: r ne 5 and r gt 2)`

    i chociaż to wyrażenie jest dozwolone, nie jest przydatne, ponieważ warunki nakładają się na siebie:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Proste wyrażenia porównawcze `eq` `lt`obejmujące `gt`, `ge` , `and` / `or` `le`, lub mogą być łączone z . Przykład:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Wyrażenia porównawcze `and` w połączeniu z (spójniki) można dalej łączyć za pomocą `or`. Ten formularz jest znany w logice logicznej jako "[Disjunctive Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)( DNF). Przykład:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Zasady `all`:
  - Proste wyrażenia równości nie mogą być pożytecznie łączone z innymi wyrażeniami. Na przykład to wyrażenie jest dozwolone:
    - `ratings/all(r: r eq 5)`

    ale to wyrażenie nie jest:
    - `ratings/all(r: r eq 5 or r le 2)`

    i chociaż to wyrażenie jest dozwolone, nie jest przydatne, ponieważ warunki nakładają się na siebie:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Proste wyrażenia porównawcze `ne` `lt`obejmujące `gt`, `ge` , `and` / `or` `le`, lub mogą być łączone z . Przykład:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Wyrażenia porównawcze `or` w połączeniu z (rozłączaniem) `and`można dalej łączyć za pomocą . Ten formularz jest znany w logice logicznej jako "[Conjunctive Normal Form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Przykład:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Reguły filtrowania kolekcji złożonych

Wyrażenia Lambda nad złożonymi kolekcjami obsługują znacznie bardziej elastyczną składnię niż wyrażenia lambda nad kolekcjami typów pierwotnych. Można użyć dowolnej konstrukcji filtru wewnątrz takiego wyrażenia lambda, którego można użyć poza jednym, z dwoma wyjątkami.

Po pierwsze `search.ismatch` funkcje i `search.ismatchscoring` nie są obsługiwane wewnątrz wyrażeń lambda. Aby uzyskać więcej informacji, zobacz [Opis filtrów kolekcji OData w usłudze Azure Cognitive Search](search-query-understand-collection-filters.md).

Po drugie, odwoływanie się do pól, które nie są *powiązane ze* zmienną zakresu (tak zwane *wolne zmienne)* nie jest dozwolone. Rozważmy na przykład następujące dwa równoważne wyrażenia filtru OData:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

Pierwsze wyrażenie będzie dozwolone, podczas gdy drugi formularz `details/margin` zostanie odrzucony, ponieważ `s`nie jest powiązany ze zmienną zakresu .

Ta reguła obejmuje również wyrażenia, które mają zmienne powiązane w zakresie zewnętrznym. Takie zmienne są wolne w odniesieniu do zakresu, w jakim się pojawiają. Na przykład pierwsze wyrażenie jest dozwolone, podczas gdy drugie wyrażenie `s/name` równoważne nie jest dozwolone, ponieważ `a`jest wolne w odniesieniu do zakresu zmiennej zakresu:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

To ograniczenie nie powinno być problemem w praktyce, ponieważ zawsze jest możliwe do konstruowania filtrów, takie, że wyrażenia lambda zawierają tylko zmienne powiązane.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Ściągawka dla reguł filtrowania kolekcji

W poniższej tabeli podsumowano reguły konstruowania prawidłowych filtrów dla każdego typu danych kolekcji.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Aby zapoznać się z przykładami sposobu konstruowania prawidłowych filtrów dla każdego przypadku, zobacz [Jak napisać prawidłowe filtry kolekcji](#bkmk_examples).

Jeśli często piszesz filtry, a zrozumienie reguł na podstawie pierwszych zasad pomoże Ci bardziej niż tylko zapamiętywanie ich, zobacz [Opis filtrów kolekcji OData w usłudze Azure Cognitive Search.](search-query-understand-collection-filters.md)

## <a name="next-steps"></a>Następne kroki  

- [Opis filtrów kolekcji OData w usłudze Azure Cognitive Search](search-query-understand-collection-filters.md)
- [Filtry w usłudze Azure Cognitive Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
