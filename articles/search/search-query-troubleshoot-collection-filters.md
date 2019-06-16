---
title: Rozwiązywanie problemów z filtry kolekcji OData — usługa Azure Search
description: Rozwiązywanie problemów z OData kolekcji filtrować błędy w zapytaniach usługi Azure Search.
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
ms.openlocfilehash: c7fa00c82eea03a50bae22fcb1ad16e230aa5bcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079628"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>Rozwiązywanie problemów z filtrów kolekcji OData w usłudze Azure Search

Aby [filtru](query-odata-filter-orderby-syntax.md) kolekcji pól w usłudze Azure Search, możesz użyć [ `any` i `all` operatory](search-query-odata-collection-operators.md) wraz z **wyrażeń lambda**. Wyrażenie lambda jest filtr podrzędny, która jest stosowana do każdego elementu kolekcji.

Nie każda funkcja wyrażenia filtru jest dostępna wewnątrz wyrażenia lambda. Które funkcje są dostępne różni się w zależności od typu danych pola kolekcji, które chcesz filtrować. Może to spowodować błąd, jeśli użytkownik próbuje użyć funkcji w wyrażeniu lambda, która nie jest obsługiwana w tym kontekście. Jeśli występują takie błędy podczas próby zapisu złożony filtr, za pośrednictwem kolekcji pól, ten artykuł pomoże Ci rozwiązać problem.

## <a name="common-collection-filter-errors"></a>Typowe błędy filtr kolekcji

W poniższej tabeli wymieniono błędy, które można napotkać podczas próby wykonania filtr kolekcji. Te błędy się zdarzyć, gdy używana jest funkcja wyrażenia filtru, który nie jest obsługiwany wewnątrz wyrażenia lambda. Każdy błąd zawiera pewne wskazówki, w jaki sposób można napisać ponownie z filtrem, aby uniknąć tego błędu. Tabela zawiera również link do odpowiedniej sekcji tego artykułu, który zawiera więcej informacji na temat sposobu uniknięcia tego błędu.

| Komunikat o błędzie | Sytuacja | Aby uzyskać więcej informacji, zobacz |
| --- | --- | --- |
| Funkcja "ismatch" nie ma parametrów powiązany z zmiennej zakresu ". Powiązana tylko pola, które odwołania są obsługiwane wewnątrz wyrażenia lambda ("any" lub "all"). Zmień filtr, aby funkcja "ismatch" znajduje się poza wyrażenia lambda i spróbuj ponownie. | Za pomocą `search.ismatch` lub `search.ismatchscoring` wewnątrz wyrażenia lambda | [Reguły filtrowania złożonych kolekcji](#bkmk_complex) |
| Wyrażenie lambda nieprawidłowy. Znaleziono test pod względem równości lub nierówności, gdzie przeciwieństwo oczekiwano w wyrażeniu lambda, które wykonuje iterację na pola typu Collection(Edm.String). W przypadku "any" Użyj wyrażenia formularza "x eq y" lub "search.in(...)". Aby uzyskać "all", użyj wyrażenia w formie "x ne y", "nie (x eq y)" lub "nie search.in(...)". | Filtrowanie według pola typu `Collection(Edm.String)` | [Reguły filtrowania kolekcji ciągów](#bkmk_strings) |
| Wyrażenie lambda nieprawidłowy. Znaleziono nieobsługiwany rodzaj złożone wyrażenie logiczne. W przypadku "any" Użyj wyrażenia będące "ORs z ANDs", znany także jako rozłącznego normalny formularza. Na przykład: '(a and b) lub (c i d) "gdzie, b, c i d są porównania lub równości wyrażeń podrzędnych. Aby uzyskać "all", użyj wyrażenia będące "ANDs z ORs", nazywane także łącznej normalny formularza. Na przykład: '(a or b) i (c lub d) "gdzie, b, c i d są porównania lub nierówności wyrażeń podrzędnych. Przykłady wyrażeń porównania: "x gt 5', ' x le 2'. Przykładowe wyrażenie porównania: "x eq 5'. Przykład wyrażenia nierówności: "x ne 5'. | Filtrowanie według pola typu `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, lub `Collection(Edm.Int64)` | [Reguły filtrowania porównywanie kolekcji](#bkmk_comparables) |
| Wyrażenie lambda nieprawidłowy. Znaleziono nieobsługiwane użycie obiektu geo.distance() lub geo.intersects() w wyrażeniu lambda, które wykonuje iterację na pola typu Collection(Edm.GeographyPoint). Dla "any" Upewnij się, porównaj geo.distance() przy użyciu operatorów "lt" lub "le" i upewnij się, że za każde użycie geo.intersects() nie jest ujemna. Aby uzyskać "all", upewnij się, porównaj geo.distance() przy użyciu operatorów "gt" lub "ge" i upewnij się, że za każde użycie geo.intersects() jest ujemna. | Filtrowanie według pola typu `Collection(Edm.GeographyPoint)` | [Reguły filtrowania kolekcji GeographyPoint](#bkmk_geopoints) |
| Wyrażenie lambda nieprawidłowy. Złożonych wyrażeń logicznych nie są obsługiwane w wyrażeniach lambda, które przechodzą przez pola typu Collection(Edm.GeographyPoint). Dla "any", Dołącz podrzędną wyrażeniach przy użyciu 'lub'; "i" nie jest obsługiwane. Aby uzyskać "all", Dołącz podrzędną wyrażeniach przy użyciu 'i'; "lub" nie jest obsługiwane. | Filtrowanie według pola typu `Collection(Edm.String)` lub `Collection(Edm.GeographyPoint)` | [Reguły filtrowania kolekcji ciągów](#bkmk_strings) <br/><br/> [Reguły filtrowania kolekcji GeographyPoint](#bkmk_geopoints) |
| Wyrażenie lambda nieprawidłowy. Znaleziono operatora porównania (jeden "lt", "le", "gt" lub "ge"). Tylko operatory porównania są dozwolone w wyrażeniach lambda, które przechodzą przez pola typu Collection(Edm.String). W przypadku "any" Użyj wyrażenia w postaci "x eq y". Aby uzyskać "all", użyj wyrażenia w postaci "x ne y" lub "nie (x eq y)". | Filtrowanie według pola typu `Collection(Edm.String)` | [Reguły filtrowania kolekcji ciągów](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Jak prawidłową kolekcję filtrów zapisu

Zasady dotyczące pisania prawidłową kolekcję filtrów są różne dla każdego typu danych. W poniższych sekcjach opisano reguły, pokazując przykłady filtru, które funkcje są obsługiwane, a które nie są:

- [Reguły filtrowania kolekcji ciągów](#bkmk_strings)
- [Reguły filtrowania logiczna kolekcji](#bkmk_bools)
- [Reguły filtrowania kolekcji GeographyPoint](#bkmk_geopoints)
- [Reguły filtrowania porównywanie kolekcji](#bkmk_comparables)
- [Reguły filtrowania złożonych kolekcji](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Reguły filtrowania kolekcji ciągów

W wyrażeniach lambda kolekcji ciągów są tylko operatory, których można użyć `eq` i `ne`.

> [!NOTE]
> Usługa Azure Search nie obsługuje `lt` / `le` / `gt` / `ge` operatory ciągów, czy wewnątrz lub na zewnątrz wyrażenia lambda.

Treść `any` tylko przetestować pod kątem równości podczas treści `all` tylko przetestować pod kątem nierówności.

Istnieje również możliwość łączenia wielu wyrażeń za pośrednictwem `or` w treści `any`i za pośrednictwem `and` w treści `all`. Ponieważ `search.in` funkcji jest odpowiednikiem łączenie sprawdza kolejno równości `or`, również jest dozwolona w treści `any`. Z drugiej strony `not search.in` jest dozwolona w treści `all`.

Na przykład te wyrażenia są dozwolone:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

gdy te wyrażenia nie są dozwolone:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Reguły filtrowania logiczna kolekcji

Typ `Edm.Boolean` obsługuje tylko `eq` i `ne` operatorów. W efekcie nie ma sensu dużo umożliwia łączenie tych klauzul, sprawdzające tę samą zmienną zakresu o `and` / `or` od czasu, który zawsze doprowadziłoby do błędów tautologies lub sprzeczności.

Poniżej przedstawiono kilka przykładów filtrów w kolekcjach logiczne, które są dozwolone:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

W przeciwieństwie do kolekcji ciągów logiczną kolekcje mają bez ograniczeń, na których można używać operatora, który typ wyrażenia lambda. Zarówno `eq` i `ne` mogą być używane w treści `any` lub `all`.

Logiczna kolekcji nie jest dozwolone wyrażenia takie jak następujące:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Reguły filtrowania kolekcji GeographyPoint

Wartości typu `Edm.GeographyPoint` w kolekcji nie można porównać ze sobą bezpośrednio. Zamiast tego muszą być używane jako parametry `geo.distance` i `geo.intersects` funkcji. `geo.distance` Funkcji z kolei musi być porównywana z wartością odległość przy użyciu jednego z operatorów porównania `lt`, `le`, `gt`, lub `ge`. Te reguły dotyczą również pola Edm.GeographyPoint — do kolekcji.

Kolekcji ciągów, takich jak `Edm.GeographyPoint` kolekcje mają pewne zasady jak funkcje geoprzestrzenne może być używany i łączone w różne rodzaje wyrażeń lambda:

- Operatory porównania, których można używać z `geo.distance` funkcji zależy od typu wyrażenia lambda. Aby uzyskać `any`, można użyć tylko `lt` lub `le`. Aby uzyskać `all`, można użyć tylko `gt` lub `ge`. Można odwrócić wyrażeń obejmujących `geo.distance`, ale musisz zmienić operator porównania (`geo.distance(...) lt x` staje się `not (geo.distance(...) ge x)` i `geo.distance(...) le x` staje się `not (geo.distance(...) gt x)`).
- W treści `all`, `geo.intersects` funkcja musi być ujemna. Z drugiej strony, w treści `any`, `geo.intersects` — funkcja nie może być ujemna.
- W treści `any`, rozwiązania geoprzestrzenne wyrażenia można łączyć, używając `or`. W treści `all`, takie wyrażenia można łączyć, używając `and`.

Powyższe ograniczenia istnieje podobnych przyczyn jako ograniczenie równości i nierówności kolekcji ciągów. Zobacz [OData opis kolekcji filtrów w usłudze Azure Search](search-query-understand-collection-filters.md) Aby dowiedzieć się więcej o tych powodów.

Poniżej przedstawiono kilka przykładów filtrów na `Edm.GeographyPoint` kolekcje, które są dozwolone:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Wyrażenia podobny do następującego nie są dozwolone dla `Edm.GeographyPoint` kolekcji:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Reguły filtrowania porównywanie kolekcji

W tej sekcji mają zastosowanie do wszystkich następujących typów danych:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Typy takie jak `Edm.Int32` i `Edm.DateTimeOffset` obsługuje wszystkie sześć operatory porównania: `eq`, `ne`, `lt`, `le`, `gt`, i `ge`. Wyrażenia lambda za pośrednictwem kolekcji tego typu mogą zawierać proste wyrażenia przy użyciu dowolnej z tych operatorów. Dotyczy to zarówno `any` i `all`. Na przykład dozwolone są następujące filtry:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Jednak istnieją ograniczenia dotyczące sposobu określenia takie porównania mogą być połączone w taki sposób, w bardziej złożonych wyrażeń wewnątrz wyrażenia lambda:

- Reguły dla `any`:
  - Proste nierówności wyrażeń nie korzyścią łączyć z innymi wyrażeniami. Na przykład może to wyrażenie:
    - `ratings/any(r: r ne 5)`

    jednak nie jest to wyrażenie:
    - `ratings/any(r: r ne 5 and r gt 2)`

    a gdy wyrażenie jest dozwolony, nie jest przydatne, ponieważ warunków nakładają się:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Wyrażeniach porównania proste obejmujące `eq`, `lt`, `le`, `gt`, lub `ge` może być łączone z `and` / `or`. Na przykład:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Wyrażeniach porównania w połączeniu z `and` (spójniki) można dodatkowo łączyć, używając `or`. Ta forma jest znana w elemencie logiczne jako "[rozłącznego formularza normalny](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Na przykład:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Reguły dla `all`:
  - Równość proste wyrażenia nie można łączyć korzyścią z innymi wyrażeniami. Na przykład może to wyrażenie:
    - `ratings/all(r: r eq 5)`

    jednak nie jest to wyrażenie:
    - `ratings/all(r: r eq 5 or r le 2)`

    a gdy wyrażenie jest dozwolony, nie jest przydatne, ponieważ warunków nakładają się:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Wyrażeniach porównania proste obejmujące `ne`, `lt`, `le`, `gt`, lub `ge` może być łączone z `and` / `or`. Na przykład:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Wyrażeniach porównania w połączeniu z `or` (disjunctions) można dodatkowo łączyć, używając `and`. Ta forma jest znana w elemencie logiczne jako "[łącznej formularza normalny](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (opcją CNF). Na przykład:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Reguły filtrowania złożonych kolekcji

Wyrażenia lambda za pośrednictwem złożonych kolekcji obsługuje składnię znacznie bardziej elastyczne niż wyrażenia lambda za pośrednictwem kolekcji typów pierwotnych. Można użyć konstrukcji filtr wewnątrz takie wyrażenie lambda, których można używać poza jednym z tylko dwoma wyjątkami.

Funkcje First, `search.ismatch` i `search.ismatchscoring` nie są obsługiwane wewnątrz wyrażenia lambda. Aby uzyskać więcej informacji, zobacz [OData opis kolekcji filtrów w usłudze Azure Search](search-query-understand-collection-filters.md).

Po drugie, odwołuje się do pola, które nie są *powiązany* do zmiennej zakresu (tak zwane *bezpłatne zmienne*) nie jest dozwolone. Na przykład należy wziąć pod uwagę następujące dwa równoważne OData wyrażenia filtrowania:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

Pierwsze wyrażenie będzie możliwe, podczas gdy drugi formularz zostanie odrzucone, ponieważ `details/margin` nie jest związany ze zmienną zakresu `s`.

Ta zasada również rozciąga się do wyrażenia, które mają zmiennych powiązanych w zewnętrznym zakresie. Takie zmienne są bezpłatne, w odniesieniu do zakresu, w jakiej są wyświetlane. Na przykład, pierwsze wyrażenie jest dozwolony, podczas gdy drugi równoważne wyrażenie nie jest dozwolona, ponieważ `s/name` jest bezpłatna w odniesieniu do zakresu zmiennej zakresu `a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

To ograniczenie nie powinien być problemu w praktyce, ponieważ zawsze jest możliwe do utworzenia filtrów w taki sposób, że wyrażenia lambda zawiera tylko powiązanych zmiennych.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Ściągawka dotycząca kolekcji reguł filtru

Poniższa tabela zawiera podsumowanie reguł tworzenia prawidłowymi filtrami. dla każdego typu danych kolekcji.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Aby uzyskać przykłady sposobu tworzenia prawidłowymi filtrami w każdym przypadku, zobacz [jak prawidłową kolekcję filtrów zapisu](#bkmk_examples).

Jeśli często zapisu filtrów i opis zasady od pierwszego zasad umożliwia więcej niż tylko tylko ich, zobacz [OData opis kolekcji filtrów w usłudze Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Kolejne kroki  

- [Objaśnienie filtrów kolekcji OData w usłudze Azure Search](search-query-understand-collection-filters.md)
- [Filtry w usłudze Azure Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
