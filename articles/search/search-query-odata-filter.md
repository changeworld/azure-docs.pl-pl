---
title: Filtr OData — odwołanie do usługi Azure Search
description: Dokumentacja języka OData dla filtru składni zapytań usługi Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: c44645ebcbf8808cc929bfaa0c3cb0d3ee9c90cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079914"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Składnia OData $filter w usłudze Azure Search

Usługa Azure Search używa [wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) mają dotyczyć dodatkowe kryteria zapytania wyszukiwania oprócz terminy wyszukiwania pełnotekstowego. W tym artykule opisano składnię filtry szczegółowo. Aby uzyskać więcej ogólnych informacji na temat filtry są i jak ich używać do osiągnięcia określonej kwerendy scenariuszy, zobacz [filtry w usłudze Azure Search](search-filters.md).

## <a name="syntax"></a>Składnia

Filtr w języku OData jest wyrażenie logiczne, które z kolei może być jednym z kilku typów wyrażeń, jak to przedstawiono w następujących EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

Typy wyrażeń logicznych między innymi:

- Za pomocą wyrażenia filtru kolekcji `any` lub `all`. Kryteria filtrowania te dotyczą kolekcji pól. Aby uzyskać więcej informacji, zobacz [operatorów kolekcji OData w usłudze Azure Search](search-query-odata-collection-operators.md).
- Wyrażenia logiczne, które łączą innych wyrażeń logicznych przy użyciu operatorów `and`, `or`, i `not`. Aby uzyskać więcej informacji, zobacz [OData operatorów logicznych w usłudze Azure Search](search-query-odata-logical-operators.md).
- Wyrażeniach porównania, które porównywać pól lub należeć do zakresu zmiennych na stałe wartości, przy użyciu operatorów `eq`, `ne`, `gt`, `lt`, `ge`, i `le`. Aby uzyskać więcej informacji, zobacz [operatorów porównania protokołu OData w usłudze Azure Search](search-query-odata-comparison-operators.md). Wyrażeniach porównania są również używane do porównywania odległości między geoprzestrzenne współrzędnych przy użyciu `geo.distance` funkcji. Aby uzyskać więcej informacji, zobacz [funkcje geoprzestrzenne OData w usłudze Azure Search](search-query-odata-geo-spatial-functions.md).
- Wartość logiczna literały `true` i `false`. Te stałe mogą być przydatne czasami podczas generowania programowo filtry, ale w przeciwnym razie nie mają tendencję do użycia w praktyce.
- Wywołania funkcji logicznych, w tym:
  - `geo.intersects`, która sprawdza, czy dany punkt znajduje się w danym wielokąta. Aby uzyskać więcej informacji, zobacz [funkcje geoprzestrzenne OData w usłudze Azure Search](search-query-odata-geo-spatial-functions.md).
  - `search.in`, która porównuje pola lub zakres zmiennej z każdej wartości na liście wartości. Aby uzyskać więcej informacji, zobacz [OData `search.in` funkcji w usłudze Azure Search](search-query-odata-search-in-function.md).
  - `search.ismatch` i `search.ismatchscoring`, której wykonywanie operacji wyszukiwania pełnotekstowego w kontekście filtru. Aby uzyskać więcej informacji, zobacz [funkcji wyszukiwania pełnotekstowego OData w usłudze Azure Search](search-query-odata-full-text-search-functions.md).
- Pole ścieżki, lub należeć do zakresu zmiennych typu `Edm.Boolean`. Na przykład, jeśli indeksu jest polem o nazwie `IsEnabled` i chcesz zwrócić wszystkie dokumenty, gdy to pole jest `true`, wyrażenia filtru można po prostu nazwę `IsEnabled`.
- Wartość logiczna wyrażenia w nawiasach. Za pomocą nawiasów może pomóc jawnie określić kolejność operacji w filtrze. Aby uzyskać więcej informacji na domyślny pierwszeństwo operatorów OData zobacz następną sekcję.

### <a name="operator-precedence-in-filters"></a>Kolejność wykonywania filtrów

Jeśli piszesz wyrażenia filtru nie nawiasów wokół jego wyrażeń podrzędnych, usługa Azure Search oceni je zgodnie z zestawem reguł pierwszeństwo operatora. Te reguły są oparte na operatory, które są używane do łączenia wyrażeń podrzędnych. Poniższa tabela zawiera listę grup operatorów w kolejności od najwyższego do najniższego pierwszeństwa:

| Grupa | Operatorem(-ami) |
| --- | --- |
| Operatory logiczne | `not` |
| Operatory porównania | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Operatory logiczne | `and` |
| Operatory logiczne | `or` |

Operator, który jest większa w powyższej tabeli będzie "związać precyzyjniejsze" do argumentów niż inne operatory. Na przykład `and` ma wyższy priorytet niż `or`, i operatory porównania mają wyższy priorytet niż każdej z nich, dzięki czemu następujących dwóch wyrażeń są równoważne:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not` Operator ma najwyższy priorytet wszystkich — nawet wyższe niż operatory porównania. To dlatego, jeśli zostanie podjęta próba zapisu filtr następująco:

    not Rating gt 5

Otrzymasz komunikat o błędzie:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Ten błąd występuje, ponieważ operator jest skojarzony z tylko `Rating` pola, które jest typu `Edm.Int32`, a nie z wyrażenia porównania całego. Obejście polega na umieszczeniu operand `not` w nawiasach:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Ograniczenia rozmiaru filtru

Istnieją limity rozmiaru i złożoności wyrażenia filtru, których można wysłać do usługi Azure Search. Limity są mniej więcej zależy od liczby klauzul w wyrażeniu filtru. Dobrą praktyką jest, że w przypadku setek klauzule są zagrożone przekroczenia limitu. Firma Microsoft zaleca, projektowania aplikacji w taki sposób, nie generować filtry niepowiązane rozmiar.

> [!TIP]
> Za pomocą [ `search.in` funkcja](search-query-odata-search-in-function.md) zamiast długich disjunctions równości porównania mogą pomóc uniknąć limit klauzuli filtru, ponieważ wywołanie funkcji jest liczone jako jedna klauzula.

## <a name="examples"></a>Przykłady

Znajdź wszystkie hotele za pomocą pokoju co najmniej jeden ze stawką podstawową mniejsza niż 200 USD, które są oceniane co najmniej 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Znajdź wszystkie hotele innych niż "Motel widoku morza", które zostały renovated od 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Znajdź wszystkie hotele, które zostały renovated w 2010 lub nowszej. Literał daty/godziny zawiera informacje o strefie czasowej dla pacyficznego czasu standardowego:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Znajdź wszystkie hotele parkowania uwzględnione, dla których wszystkie sale palenie:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- LUB —  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Znajdź wszystkie hotele, które mają luksusowe lub obejmują parkowania ocena, 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Znajdź wszystkie hotele z tagiem "Wi-Fi" w co najmniej jednym pomieszczeniu (gdzie każdego pomieszczenia tagami przechowywane w `Collection(Edm.String)` pole):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Znajdź wszystkie hotele przy użyciu dowolnej pokojów:  

    $filter=Rooms/any()

Znajdź wszystkie hotele, które nie mają pokojów:

    $filter=not Rooms/any()

Znajdź wszystkie hoteli w ciągu 10 km od punktu danego odwołania (gdzie `Location` jest polem typu `Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Znajdź wszystkie hotele, w ramach danego okienka ekranu, określane jako wielokąta (gdzie `Location` jest polem typu Edm.GeographyPoint). Wielokąt musi być zamknięty, czyli pierwszy i ostatni punkt zestawów musi być taka sama. Ponadto [punkty muszą być wymienione w kolejności do ruchu wskazówek zegara](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Znajdź wszystkie hotele, w którym pole "Opis" ma wartość null. Pole będzie mieć wartości null, jeśli nigdy nie została ustawiona lub została jawnie ustawiona na wartość null:  

    $filter=Description eq null

Znajdź wszystkie hotele o nazwie równa "Widok Morza motel" lub "Budżetu hotel"). Te zwroty zawierać spacji, a ilość miejsca jest domyślnym ogranicznikiem. Możesz określić alternatywne ogranicznika w pojedynczym cudzysłowie jako trzeci parametr ciągu:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Znajdź wszystkie hotele o nazwie równa "Widok Morza motel" lub "Budżetu hotel" rozdzielone "|"):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Znajdź wszystkie hotele, w których wszystkie sale mają tag "Wi-Fi" lub "miarki":

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Znajdź dopasowanie fraz w obrębie kolekcji, takie jak "podgrzewanego ręczników stojakami" lub uwzględnione hairdryer w znacznikach.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Znajdowanie dokumentów z wyrazem "waterfront". To zapytanie filtru jest taka sama jak [żądania wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) z `search=waterfront`.

    $filter=search.ismatchscoring('waterfront')

Znajdź dokumenty zawierające słowo "hostel" i ocena większa lub równa 4 czy dokumenty zawierające słowo "motel" i ocena równy 5. To żądanie nie może być wyrażona bez `search.ismatchscoring` działać, ponieważ łączy z filtru operacje przy użyciu wyszukiwania pełnotekstowego `or`.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Znajdź dokumenty bez słowa "luksusowe".

    $filter=not search.ismatch('luxury')

Znajdź dokumenty zawierające frazę "view ocean" lub klasyfikacji równa 5. `search.ismatchscoring` Kwerenda zostanie wykonana tylko względem pól `HotelName` i `Description`. Dokumenty, pasujących do drugiej klauzuli rozłączenia zostaną zwrócone zbyt — hotele z `Rating` równy 5. Te dokumenty zostaną zwrócone z wynikiem jest równa zero, aby stał się wyczyścić, że ich nie pasuje do żadnego z ocenami części wyrażenia.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Znaleźć hotele, gdzie warunki "hotel" i "Kuwejcie" są od siebie w opisie nie więcej niż pięć słów i gdzie wszystkie sale palenie. To zapytanie używa [pełny język zapytań Lucene](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Kolejne kroki  

- [Filtry w usłudze Azure Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
