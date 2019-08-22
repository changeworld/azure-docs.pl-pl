---
title: Odwołanie operatora porównania OData — Azure Search
description: Operatory porównania OData, EQ, ne, gt, lt, GE i Le w Azure Search zapytaniach.
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
ms.openlocfilehash: a8bd8b05fd874e05e5e59042d461f4a4286c81e4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648058"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>Operatory porównania OData w Azure Search- `eq`, `ne`, `gt` `lt`,,, i `ge``le`

Najbardziej podstawową operacją w [wyrażeniu filtru OData](query-odata-filter-orderby-syntax.md) w Azure Search jest porównanie pola z daną wartością. Dwa typy porównania są możliwym porównaniem równości i porównywaniem zakresu. Aby porównać pole z wartością stałą, można użyć następujących operatorów:

Operatory równości:

- `eq`: Sprawdź, czy pole jest **równe** stałej wartości
- `ne`: Sprawdź, czy pole **nie jest równe** wartości stałej

Operatory zakresu:

- `gt`: Sprawdź, czy pole jest **większe niż** wartość stała
- `lt`: Sprawdź, czy pole jest **mniejsze niż** wartość stała
- `ge`: Sprawdź, czy pole jest **większe lub równe** wartości stałej
- `le`: Sprawdź, czy pole jest **mniejsze niż lub równe** wartości stałej

Można użyć operatorów zakresu w połączeniu z [operatorami logicznymi](search-query-odata-logical-operators.md) , aby sprawdzić, czy pole znajduje się w określonym zakresie wartości. Zobacz [przykłady](#examples) w dalszej części tego artykułu.

> [!NOTE]
> Jeśli wolisz, możesz umieścić wartość stałą po lewej stronie operatora i nazwę pola po prawej stronie. Dla operatorów zakresu, znaczenie porównania zostaje cofnięte. Na przykład, jeśli stała wartość jest po lewej stronie, `gt` należy sprawdzić, czy wartość stała jest większa niż pole. Można również użyć operatorów porównania, aby porównać wynik funkcji, np `geo.distance`., z wartością. W przypadku funkcji logicznych, `search.ismatch`takich jak, porównywanie `true` wyniku `false` z lub jest opcjonalne.

## <a name="syntax"></a>Składnia

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę wyrażenia OData, które korzysta z operatorów porównania.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Istnieją dwie formy wyrażeń porównania. Jedyną różnicą między nimi jest to, czy stała pojawia się po lewej lub prawej stronie operatora. Wyrażenie po drugiej stronie operatora musi być **zmienną** lub wywołaniem funkcji. Zmienna może być albo nazwą pola lub zmienną zakresu w przypadku [wyrażenia lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Typy danych do porównania

Typy danych po obu stronach operatora porównania muszą być zgodne. Na przykład, jeśli po lewej stronie jest polem typu `Edm.DateTimeOffset`, po prawej stronie musi być stała daty i godziny. Numeryczne typy danych są bardziej elastyczne. Można porównać zmienne i funkcje dowolnego typu liczbowego ze stałymi dowolnego innego typu liczbowego z kilkoma ograniczeniami, zgodnie z opisem w poniższej tabeli.

| Typ zmiennej lub funkcji | Typ stałej wartości | Ograniczenia |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Porównanie podlega specjalnym [regułom dla `NaN` ](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Stała jest konwertowana na `Edm.Double`, co spowodowało utratę dokładności dla wartości o dużej wielkości |
| `Edm.Double` | `Edm.Int32` | Nie dotyczy |
| `Edm.Int64` | `Edm.Double` | Porównania do `NaN`, `-INF`, lub `INF` są niedozwolone |
| `Edm.Int64` | `Edm.Int64` | Nie dotyczy |
| `Edm.Int64` | `Edm.Int32` | Stała jest konwertowana na `Edm.Int64` przed porównaniem |
| `Edm.Int32` | `Edm.Double` | Porównania do `NaN`, `-INF`, lub `INF` są niedozwolone |
| `Edm.Int32` | `Edm.Int64` | Nie dotyczy |
| `Edm.Int32` | `Edm.Int32` | Nie dotyczy |

W przypadku porównań, które nie są dozwolone, takich jak porównywanie `Edm.Int64` pola `NaN`typu z, interfejs API REST Azure Search zwróci komunikat "http 400: Złe żądanie — błąd.

> [!IMPORTANT]
> Mimo że porównania typu liczbowego są elastyczne, zdecydowanie zalecamy zapisanie porównań w filtrach, aby wartość stała była tego samego typu danych jak zmienna lub funkcja, do której jest porównywana. Jest to szczególnie ważne w przypadku mieszania wartości zmiennoprzecinkowych i liczb całkowitych, w przypadku których niejawne konwersje mające na celu utratę dokładności są możliwe.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Specjalne przypadki `null` i`NaN`

W przypadku korzystania z operatorów porównania należy pamiętać, że wszystkie pola niebędące kolekcjami w Azure Search mogą być `null`potencjalnie. W poniższej tabeli przedstawiono wszystkie możliwe wyniki dla wyrażenia porównania, które mogą być `null`:

| Operator | Wynik, gdy tylko pole lub zmienna jest`null` | Wynik, gdy tylko stała jest`null` | Wynik, gdy pole lub zmienna i stała są`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Błąd nieprawidłowego żądania | HTTP 400: Błąd nieprawidłowego żądania |
| `lt` | `false` | HTTP 400: Błąd nieprawidłowego żądania | HTTP 400: Błąd nieprawidłowego żądania |
| `ge` | `false` | HTTP 400: Błąd nieprawidłowego żądania | HTTP 400: Błąd nieprawidłowego żądania |
| `le` | `false` | HTTP 400: Błąd nieprawidłowego żądania | HTTP 400: Błąd nieprawidłowego żądania |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Podsumowując, `null` jest równa tylko samemu i nie jest mniejsza ani większa niż jakakolwiek inna wartość.

Jeśli indeks zawiera pola typu `Edm.Double` , a wartości są przekazywane `NaN` do tych pól, należy uwzględnić to przy pisaniu filtrów. Azure Search implementuje standard IEEE 754 do obsługi `NaN` wartości i porównania z takimi wartościami tworzą niejasne wyniki, jak pokazano w poniższej tabeli.

| Operator | Wynik, gdy co najmniej jeden operand jest`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

W obszarze Podsumowanie `NaN` nie jest równa żadnej wartości, łącznie z nią samą.

### <a name="comparing-geo-spatial-data"></a>Porównywanie danych przestrzennych geograficznie

Nie można bezpośrednio porównać pola typu `Edm.GeographyPoint` z wartością stałą, ale można `geo.distance` użyć funkcji. Ta funkcja zwraca wartość typu `Edm.Double`, więc można ją porównać ze stałą numeryczną w celu filtrowania na podstawie odległości od stałych współrzędnych geograficznych. Zapoznaj [](#examples) się z poniższymi przykładami.

### <a name="comparing-string-data"></a>Porównywanie danych ciągu

Ciągi można porównać w filtrach w celu uzyskania dokładnych `eq` dopasowań przy użyciu operatorów i `ne` . W tych porównaniach jest rozróżniana wielkość liter.

## <a name="examples"></a>Przykłady

Dopasowuje dokumenty, `Rating` gdy pole jest z zakresu od 3 do 5 włącznie:

    Rating ge 3 and Rating le 5

Dopasuj dokumenty, w `Location` których pole jest mniejsze niż 2 kilometry od danego położenia i długości geograficznej:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Dopasuj dokumenty, w `LastRenovationDate` których pole jest większe lub równe 1 stycznia, 2015, północ czasu UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Dopasuj dokumenty, w `Details/Sku` których pole nie `null`jest:

    Details/Sku ne null

Dopasuj dokumenty dla hoteli, w których co najmniej jedno pomieszczenie ma typ "Pokój Deluxe", gdzie ciąg `Rooms/Type` pola pasuje dokładnie do filtru:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Następne kroki  

- [Filtry w Azure Search](search-filters.md)
- [Przegląd języka wyrażenia OData dla Azure Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;Azure Search interfejs API REST usługi&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
