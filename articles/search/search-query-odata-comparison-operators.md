---
title: Odwołanie do operatora porównania OData
titleSuffix: Azure Cognitive Search
description: Składnia i dokumentacja referencyjna do korzystania z operatorów porównania OData (eq, ne, gt, lt, ge i le) w zapytaniach usługi Azure Cognitive Search.
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
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113223"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Operatory porównawcze OData `eq`w `ne` `gt`usłudze Azure Cognitive Search - , , `lt`, `ge`, i`le`

Najbardziej podstawową operacją w [wyrażeniu filtru OData](query-odata-filter-orderby-syntax.md) w usłudze Azure Cognitive Search jest porównanie pola z daną wartością. Możliwe są dwa typy porównania — porównanie równości i porównanie zakresu. Do porównania pola ze stałą wartością można użyć następujących operatorów:

Operatory równości:

- `eq`: Sprawdź, czy pole jest **równe** wartości stałej
- `ne`: Sprawdź, czy pole nie jest **równe** wartości stałej

Operatorzy asortymentu:

- `gt`: Sprawdź, czy pole jest **większe niż** wartość stała
- `lt`: Sprawdź, czy pole jest **mniejsze niż** wartość stała
- `ge`: Sprawdź, czy pole jest **większe lub równe** wartości stałej
- `le`: Sprawdź, czy pole jest **mniejsze lub równe** wartości stałej

Operatory zakresu można użyć w połączeniu z [operatorami logicznymi,](search-query-odata-logical-operators.md) aby sprawdzić, czy pole mieści się w określonym zakresie wartości. Zobacz [przykłady](#examples) w dalszej części tego artykułu.

> [!NOTE]
> Jeśli wolisz, możesz umieścić stałą wartość po lewej stronie operatora i nazwę pola po prawej stronie. W przypadku operatorów zakresów znaczenie porównania jest odwrócone. Na przykład jeśli stała wartość znajduje `gt` się po lewej stronie, można sprawdzić, czy stała wartość jest większa niż pole. Operatory porównania można również użyć do porównania wyniku `geo.distance`funkcji, takich jak , z wartością. W przypadku funkcji `search.ismatch`logicznych, takich `true` jak `false` porównywanie wyniku z lub opcjonalne.

## <a name="syntax"></a>Składnia

Następujące EBNF[(Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę wyrażenia OData, który używa operatorów porównania.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

Istnieją dwie formy wyrażeń porównania. Jedyną różnicą między nimi jest to, czy stała pojawia się po lewej czy prawej stronie operatora. Wyrażenie po drugiej stronie operatora musi być **zmienną** lub wywołaniem funkcji. Zmienną może być nazwa pola lub zmienna zakresu w przypadku [wyrażenia lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Typy danych do porównań

Typy danych po obu stronach operatora porównania muszą być zgodne. Na przykład, jeśli lewa strona `Edm.DateTimeOffset`jest polem typu, po prawej stronie musi być stała daty i godziny. Typy danych liczbowych są bardziej elastyczne. Można porównać zmienne i funkcje dowolnego typu liczbowego ze stałymi dowolnego innego typu liczbowego, z kilkoma ograniczeniami, jak opisano w poniższej tabeli.

| Zmienna lub typ funkcji | Typ wartości stałej | Ograniczenia |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Porównanie podlega [specjalnym zasadom `NaN` ](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Stała jest konwertowana na `Edm.Double`, co powoduje utratę precyzji dla wartości o dużej wielkości |
| `Edm.Double` | `Edm.Int32` | Nie dotyczy |
| `Edm.Int64` | `Edm.Double` | Porównania z `NaN` `-INF`, `INF` lub są niedozwolone |
| `Edm.Int64` | `Edm.Int64` | Nie dotyczy |
| `Edm.Int64` | `Edm.Int32` | Stała jest konwertowana przed `Edm.Int64` porównaniem |
| `Edm.Int32` | `Edm.Double` | Porównania z `NaN` `-INF`, `INF` lub są niedozwolone |
| `Edm.Int32` | `Edm.Int64` | Nie dotyczy |
| `Edm.Int32` | `Edm.Int32` | Nie dotyczy |

Dla porównań, które nie są dozwolone, takie `Edm.Int64` jak `NaN`porównywanie pola typu do interfejsu API REST usługi Azure Cognitive Search zwróci błąd "HTTP 400: Bad Request".

> [!IMPORTANT]
> Mimo że porównania typów liczbowych są elastyczne, zdecydowanie zaleca się zapisywanie porównań w filtrach, tak aby stała wartość jest tego samego typu danych co zmienna lub funkcja, z którą jest porównywana. Jest to szczególnie ważne podczas mieszania wartości zmiennoprzecinkowych i całkowitych, gdzie możliwe są konwersje niejawne, które tracą precyzję.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Szczególne przypadki `null` dotyczące`NaN`

Podczas korzystania z operatorów porównania, ważne jest, aby pamiętać, że `null`wszystkie pola niezbierania w usłudze Azure Cognitive Search potencjalnie może być. W poniższej tabeli przedstawiono wszystkie możliwe wyniki wyrażenia `null`porównania, w którym każda ze stron może być:

| Operator | Wynik, gdy tylko pole lub zmienna jest`null` | Wynik, gdy tylko stała jest`null` | Wynik, gdy zarówno pole, jak i zmienna są`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Błąd nieprawidłowego żądania | HTTP 400: Błąd nieprawidłowego żądania |
| `lt` | `false` | HTTP 400: Błąd nieprawidłowego żądania | HTTP 400: Błąd nieprawidłowego żądania |
| `ge` | `false` | HTTP 400: Błąd nieprawidłowego żądania | HTTP 400: Błąd nieprawidłowego żądania |
| `le` | `false` | HTTP 400: Błąd nieprawidłowego żądania | HTTP 400: Błąd nieprawidłowego żądania |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Podsumowując, `null` jest równa tylko dla siebie i nie jest mniejsza lub większa niż jakakolwiek inna wartość.

Jeśli indeks ma pola `Edm.Double` typu `NaN` i przekazujesz wartości do tych pól, należy to uwzględnić podczas pisania filtrów. Usługa Azure Cognitive Search implementuje standard IEEE `NaN` 754 do obsługi wartości, a porównania z takimi wartościami dają nieoczywiste wyniki, jak pokazano w poniższej tabeli.

| Operator | Wynik, gdy co najmniej jeden operand jest`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Podsumowując, `NaN` nie jest równa żadnej wartości, w tym siebie.

### <a name="comparing-geo-spatial-data"></a>Porównywanie danych geoprzestrzennych

Nie można bezpośrednio porównać pola `Edm.GeographyPoint` typu ze stałą wartością, `geo.distance` ale można użyć funkcji. Ta funkcja zwraca wartość `Edm.Double`typu, dzięki czemu można ją porównać ze stałą numeryczną do filtrowania na podstawie odległości od stałych współrzędnych geoprzestrzennych. Zobacz [poniższe przykłady.](#examples)

### <a name="comparing-string-data"></a>Porównywanie danych ciągu

Ciągi można porównać w filtrach `eq` dla `ne` dokładnych dopasowań przy użyciu operatorów i. W tych porównaniach rozróżniana jest wielkość liter.

## <a name="examples"></a>Przykłady

Dopasuj `Rating` dokumenty, w których pole znajduje się od 3 do 5, włącznie:

    Rating ge 3 and Rating le 5

Dopasuj `Location` dokumenty, w których pole znajduje się mniej niż 2 kilometry od danej szerokości i długości geograficznej:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Dopasuj `LastRenovationDate` dokumenty, w których pole jest większe lub równe 1 stycznia 2015 r., o północy UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Dopasuj `Details/Sku` dokumenty, `null`w których pole nie jest:

    Details/Sku ne null

Dopasuj dokumenty do hoteli, w których co najmniej `Rooms/Type` jeden pokój ma typ "Pokój deluxe", gdzie ciąg pola dokładnie pasuje do filtru:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Następne kroki  

- [Filtry w usłudze Azure Cognitive Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
