---
title: Odwołanie operatora porównania OData — usługa Azure Search
description: Operatorów porównania protokołu OData, eq, ne, gt, lt, ge i le w zapytaniach usługi Azure Search.
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
ms.openlocfilehash: b51bf3d77283ae828f47fdb0355d2deb43f071a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079927"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>Operatorów porównania protokołu OData w usłudze Azure Search - `eq`, `ne`, `gt`, `lt`, `ge`, i `le`

Najbardziej podstawowa operacji [wyrażenie filtru OData](query-odata-filter-orderby-syntax.md) w usłudze Azure Search jest porównanie pola do podanej wartości. Porównanie dwa typy są możliwe — porównanie równości i porównanie zakresu. Następujące operatory służy do porównywania pole stałej wartości:

Operatory porównania:

- `eq`: Sprawdź, czy pole jest **równa** wartością stałą
- `ne`: Sprawdź, czy pole jest **nie jest równa** wartością stałą

Operatory zakresu:

- `gt`: Sprawdź, czy pole jest **większa** wartością stałą
- `lt`: Sprawdź, czy pole jest **mniej niż** wartością stałą
- `ge`: Sprawdź, czy pole jest **większa lub równa** wartością stałą
- `le`: Sprawdź, czy pole jest **mniejsze niż lub równe** wartością stałą

Operatory zakresu można używać w połączeniu z [operatorów logicznych](search-query-odata-logical-operators.md) do sprawdzenia, czy pole jest w zakresie wartości. Zobacz [przykłady](#examples) w dalszej części tego artykułu.

> [!NOTE]
> Jeśli wolisz, możesz umieścić wartości stałej po lewej stronie operatora i nazwy pola po prawej stronie. Operatory zakresu znaczenie porównania jest odwrócona. Na przykład, jeśli wartość stała jest po lewej stronie `gt` może sprawdzić, czy wartość stała jest większa niż wartość pola. Umożliwia także operatory porównania do porównania z wynikiem funkcji, takich jak `geo.distance`, z wartością. Logiczne akceptują funkcje takie jak `search.ismatch`, porównanie wyniku do `true` lub `false` jest opcjonalne.

## <a name="syntax"></a>Składnia

Następujące EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatyki wyrażenia OData, która korzysta z operatorów porównania.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

Istnieją dwa rodzaje wyrażeniach porównania. Jedyną różnicą między nimi jest, czy stała jest wyświetlany na lewej - lub po prawej stronie operatora. Wyrażenie po drugiej stronie operatora musi być **zmiennej** lub wywołania funkcji. Zmienna może być nazwa pola lub zmiennej zakresu, w przypadku właściwości [wyrażenia lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Typy danych dla porównania

Typy danych po obu stronach operatora porównania muszą być zgodne. Na przykład, jeśli po lewej stronie jest polem typu `Edm.DateTimeOffset`, a następnie po prawej stronie musi być stałą daty i godziny. Typy danych liczbowych są bardziej elastyczne. Zmienne i funkcje dowolnego typu liczbowego, za pomocą stałych dowolnego innego typu liczbowego, z kilkoma ograniczeniami można porównać, zgodnie z opisem w poniższej tabeli.

| Typ zmiennej lub funkcji | Typ stałej wartości | Ograniczenia |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Porównanie jest podlegają [specjalne reguły dla `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Stała jest konwertowany na `Edm.Double`, co spowoduje utratę precyzji wartości typu duża wartość |
| `Edm.Double` | `Edm.Int32` | Nie dotyczy |
| `Edm.Int64` | `Edm.Double` | Porównanie z typem `NaN`, `-INF`, lub `INF` nie są dozwolone |
| `Edm.Int64` | `Edm.Int64` | Nie dotyczy |
| `Edm.Int64` | `Edm.Int32` | Stała jest konwertowany na `Edm.Int64` przed porównania |
| `Edm.Int32` | `Edm.Double` | Porównanie z typem `NaN`, `-INF`, lub `INF` nie są dozwolone |
| `Edm.Int32` | `Edm.Int64` | Nie dotyczy |
| `Edm.Int32` | `Edm.Int32` | Nie dotyczy |

Dla porównania, które nie są dozwolone, takie jak porównywanie pola typu `Edm.Int64` do `NaN`, zwróci interfejsu API REST usługi Azure Search "HTTP 400: Nieprawidłowe żądanie"błąd.

> [!IMPORTANT]
> Mimo że typu numerycznego porównania są elastyczne, zdecydowanie zaleca się zapisywania porównania w filtrach, tak, aby stała wartość jest typu danych jako zmienna lub funkcja, która porównywane. Jest to szczególnie ważne podczas mieszania zmiennoprzecinkowych i wartości całkowitych, gdzie możliwe są niejawne konwersje, które utratę precyzji.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Specjalne przypadki dla `null` i `NaN`

Korzystając z operatorów porównania, to trzeba pamiętać, że wszystkie pola-collection w usłudze Azure Search może potencjalnie być `null`. W poniższej tabeli przedstawiono wszystkie możliwe wyniki wyrażenia porównania, w których może być po obu stronach `null`:

| Operator | Wynik po tylko pola lub zmiennej `null` | Wynik, gdy jest tylko — stała `null` | Wynik w przypadku pola lub zmiennej i stałej `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Zły Błąd żądania | HTTP 400: Zły Błąd żądania |
| `lt` | `false` | HTTP 400: Zły Błąd żądania | HTTP 400: Zły Błąd żądania |
| `ge` | `false` | HTTP 400: Zły Błąd żądania | HTTP 400: Zły Błąd żądania |
| `le` | `false` | HTTP 400: Zły Błąd żądania | HTTP 400: Zły Błąd żądania |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Podsumowując `null` jest taki sam, tylko do samego siebie i jest nie mniejsza lub większa od innych wartości.

Jeśli indeks zawiera pola typu `Edm.Double` i przekażesz `NaN` wartości do tych pól, należy uwzględnić, podczas zapisywania filtrów. Usługa Azure Search implementuje standardu IEEE 754 dla obsługi `NaN` wartości i porównania z wartością takich wartości należy wygenerować wyniki oczywiste, jak pokazano w poniższej tabeli.

| Operator | Wynik po co najmniej jeden argument `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Podsumowując `NaN` nie jest równy dowolne wartości, w tym sam.

### <a name="comparing-geo-spatial-data"></a>Porównywanie danych geoprzestrzenne

Nie można bezpośrednio porównywać pól typu `Edm.GeographyPoint` z wartością stałą, ale można użyć `geo.distance` funkcji. Ta funkcja zwraca wartość typu `Edm.Double`, tak aby można ją porównać liczbowym stałą do filtrowania na podstawie odległości ze stałą współrzędnych geoprzestrzenne. Zobacz [przykłady](#examples) poniżej.

### <a name="comparing-string-data"></a>Porównanie ciągu danych

Ciągi mogą być porównywane w filtrach dokładnego dopasowania za pomocą `eq` i `ne` operatorów. Te porównania uwzględniają wielkość liter.

## <a name="examples"></a>Przykłady

Dopasowanie dokumenty, gdzie `Rating` pole jest od 3 do 5 (włącznie):

    Rating ge 3 and Rating le 5

Dopasowanie dokumenty, gdzie `Location` pole jest mniejsza niż 2 kilometrów od danej szerokości i długości geograficznej:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Dopasowanie dokumenty, gdzie `LastRenovationDate` pola jest większa lub równa 1 stycznia 2015 r. o północy czasu UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Dopasowanie dokumenty, gdzie `Details/Sku` pole nie jest `null`:

    Details/Sku ne null

Odpowiada dokumenty, by hotele, w którym co najmniej jednego miejsca ma typ "Deluxe pokoju", gdzie ciąg `Rooms/Type` pola dokładnie pasuje do filtru:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Kolejne kroki  

- [Filtry w usłudze Azure Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
