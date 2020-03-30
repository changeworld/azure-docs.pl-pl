---
title: Odwołanie do filtru OData
titleSuffix: Azure Cognitive Search
description: Odwołanie do języka OData i pełna składnia używana do tworzenia wyrażeń filtru w zapytaniach usługi Azure Cognitive Search.
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282889"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Składnia $filter OData w usłudze Azure Cognitive Search

Usługa Azure Cognitive Search używa [wyrażeń filtru OData](query-odata-filter-orderby-syntax.md) do zastosowania dodatkowych kryteriów do zapytania wyszukiwania oprócz haseł wyszukiwania pełnotekstowego. Ten artykuł zawiera szczegółowe opis składni filtrów. Aby uzyskać bardziej ogólne informacje o tym, czym są filtry i jak ich używać do realizacji określonych scenariuszy zapytań, zobacz [Filtry w usłudze Azure Cognitive Search](search-filters.md).

## <a name="syntax"></a>Składnia

Filtr w języku OData jest wyrażeniem logicznym, które z kolei może być jednym z kilku typów ekspresji, jak pokazano na poniższym EBNF ([Rozszerzony formularz Backus-Naur):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

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

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

Typy wyrażeń logicznych obejmują:

- Wyrażenia filtru `any` kolekcji przy użyciu lub `all`. Te kryteria filtrowania stosuje się do pól kolekcji. Aby uzyskać więcej informacji, zobacz [Operatory kolekcji OData w usłudze Azure Cognitive Search](search-query-odata-collection-operators.md).
- Wyrażenia logiczne łączące inne wyrażenia logiczne `or`przy `not`użyciu operatorów `and`, i . Aby uzyskać więcej informacji, zobacz [Operatory logiczne OData w usłudze Azure Cognitive Search](search-query-odata-logical-operators.md).
- Wyrażenia porównawcze, które porównują pola lub `eq`zmienne zakresu ze wartościami stałymi przy użyciu operatorów `ne`, `gt`, `lt`, `ge`i `le`. Aby uzyskać więcej informacji, zobacz [Operatory porównywania OData w usłudze Azure Cognitive Search](search-query-odata-comparison-operators.md). Wyrażenia porównania są również używane do porównywania odległości między `geo.distance` współrzędnymi geoprzestrzennymi za pomocą funkcji. Aby uzyskać więcej informacji, zobacz [Funkcje geoprzestrzenne OData w usłudze Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).
- Literały logiczne `true` i `false`. Te stałe mogą być przydatne czasami podczas programowego generowania filtrów, ale w przeciwnym razie nie są używane w praktyce.
- Wywołania funkcji logicznych, w tym:
  - `geo.intersects`, który sprawdza, czy dany punkt znajduje się w obrębie danego wielokąta. Aby uzyskać więcej informacji, zobacz [Funkcje geoprzestrzenne OData w usłudze Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).
  - `search.in`, która porównuje zmienną pola lub zakresu z każdą wartością na liście wartości. Aby uzyskać więcej informacji, zobacz [Funkcja OData `search.in` w usłudze Azure Cognitive Search](search-query-odata-search-in-function.md).
  - `search.ismatch`i `search.ismatchscoring`, które wykonują operacje wyszukiwania pełnotekstowego w kontekście filtru. Aby uzyskać więcej informacji, zobacz [Funkcje wyszukiwania pełnotekstowego OData w usłudze Azure Cognitive Search](search-query-odata-full-text-search-functions.md).
- Ścieżki pól lub zmienne `Edm.Boolean`zakresu typu . Jeśli na przykład indeks ma wywoływane `IsEnabled` pole logiczne i chcesz zwrócić `true`wszystkie dokumenty, w których `IsEnabled`znajduje się to pole, wyrażenie filtru może być po prostu nazwą .
- Wyrażenia logiczne w nawiasach. Za pomocą nawiasów może pomóc jawnie określić kolejność operacji w filtrze. Aby uzyskać więcej informacji na temat domyślnego pierwszeństwa operatorów OData, zobacz następną sekcję.

### <a name="operator-precedence-in-filters"></a>Pierwszeństwo operatora w filtrach

Jeśli piszesz wyrażenie filtru bez nawiasów wokół jego wyrażeń podrzędnych, usługa Azure Cognitive Search oceni je zgodnie z zestawem reguł pierwszeństwa operatora. Reguły te są oparte na operatorach, które są używane do łączenia wyrażeń podrzędnych. W poniższej tabeli wymieniono grupy operatorów w kolejności od najwyższego do najniższego priorytetu:

| Grupa | Operatorzy |
| --- | --- |
| Operatory logiczne | `not` |
| Operatory porównania | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Operatory logiczne | `and` |
| Operatory logiczne | `or` |

Operator, który jest wyższy w powyższej tabeli będzie "wiązać się bardziej szczelnie" do jego operands niż inne operatory. Na przykład `and` ma wyższy priorytet `or`niż operatory porównania mają wyższy priorytet niż którykolwiek z nich, więc następujące dwa wyrażenia są równoważne:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

Operator `not` ma najwyższy priorytet ze wszystkich — nawet wyższy niż operatory porównania. Dlatego, jeśli spróbujesz napisać filtr w ten sposób:

    not Rating gt 5

Zostanie wyświetlony ten komunikat o błędzie:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Ten błąd występuje, ponieważ operator jest `Rating` skojarzony tylko `Edm.Int32`z polem, które jest typu, a nie z całym wyrażeniem porównania. Poprawka polega na umieszczeniu `not` operandu w nawiasach:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Ograniczenia rozmiaru filtra

Istnieją ograniczenia dotyczące rozmiaru i złożoności wyrażeń filtru, które można wysłać do usługi Azure Cognitive Search. Limity są w przybliżeniu oparte na liczbie klauzul w wyrażeniu filtru. Dobrą wytyczną jest to, że jeśli masz setki klauzul, jesteś na ryzyko przekroczenia limitu. Zaleca się projektowanie aplikacji w taki sposób, aby nie generować filtry o rozmiarze nieograniczonym.

> [!TIP]
> Za pomocą [ `search.in` funkcji](search-query-odata-search-in-function.md) zamiast długich rozłączenia porównań równości może pomóc uniknąć limitu klauzuli filtru, ponieważ wywołanie funkcji liczy się jako pojedyncza klauzula.

## <a name="examples"></a>Przykłady

Znajdź wszystkie hotele z co najmniej jednym pokojem o podstawowej stawce mniejszej niż 200 USD, które są oceniane na poziomie lub wyższym od 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Znajdź wszystkie hotele inne niż "Sea View Motel", które zostały odnowione od 2010 roku:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Znajdź wszystkie hotele, które zostały odnowione w 2010 roku lub później. Literał datetime zawiera informacje o strefie czasowej dla czasu pacyficznego:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Znajdź wszystkie hotele, które mają parking wliczony w cenę i gdzie wszystkie pokoje są dla niepalących:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \-LUB -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Znajdź wszystkie hotele, które są luksusowe lub obejmują parking i mają ocenę 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Znajdź wszystkie hotele z tagiem "wifi" w co najmniej jednym `Collection(Edm.String)` pokoju (gdzie każdy pokój ma znaczniki przechowywane w polu):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Znajdź wszystkie hotele z dowolnymi pokojami:  

    $filter=Rooms/any()

Znajdź wszystkie hotele, które nie mają pokoi:

    $filter=not Rooms/any()

Znajdź wszystkie hotele w odległości 10 `Location` kilometrów od danego `Edm.GeographyPoint`punktu odniesienia (gdzie jest pole typu):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Znajdź wszystkie hotele w danej rzutni opisanej `Location` jako wielokąt (gdzie jest pole typu Edm.GeographyPoint). Wielokąt musi być zamknięty, co oznacza, że pierwszy i ostatni zestaw punktów muszą być takie same. Ponadto [punkty muszą być wymienione w kolejności przeciwnej do ruchu wskazówek zegara](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Znajdź wszystkie hotele, w których pole "Opis" ma wartość null. Pole będzie null, jeśli nigdy nie został ustawiony lub jeśli został jawnie ustawiony na null:  

    $filter=Description eq null

Znajdź wszystkie hotele o nazwie "Motel z widokiem na morze" lub "Hotel budżetowy"). Te frazy zawierają spacje, a spacja jest domyślnym ogranicznikiem. Można określić alternatywny ogranicznik w pojedynczych cudzysłowach jako trzeci parametr ciągu:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Znajdź wszystkie hotele o nazwie równej "Motel z widokiem na morze" lub "Hotel budżetowy" oddzielone "|"):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Znajdź wszystkie hotele, w których wszystkie pokoje mają metkę "wifi" lub "wanna":

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Znajdź dopasowanie do zwrotów w kolekcji, takich jak "podgrzewane wieszaki na ręczniki" lub "suszarka do włosów w zestawie" w tagach.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Znajdź dokumenty ze słowem "nabrzeże". Ta kwerenda filtrująca jest `search=waterfront`identyczna z [żądaniem wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) z programem .

    $filter=search.ismatchscoring('waterfront')

Znajdź dokumenty ze słowem "hostel" i ocena większa lub równa 4, lub dokumenty ze słowem "motel" i ocena równa 5. To żądanie nie może być `search.ismatchscoring` wyrażone bez funkcji, ponieważ łączy wyszukiwanie `or`pełnotekstowe z operacjami filtrowania za pomocą .

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Znajdź dokumenty bez słowa "luksus".

    $filter=not search.ismatch('luxury')

Znajdź dokumenty z frazą "widok oceanu" lub oceną równą 5. Kwerenda `search.ismatchscoring` będzie wykonywana tylko `HotelName` `Description`względem pól i . Dokumenty, które pasowały tylko do drugiej klauzuli rozłączenia, również `Rating` zostaną zwrócone - hotele o wysokości 5. Dokumenty te zostaną zwrócone z wynikiem równym zero, aby było jasne, że nie pasują do żadnej z ocenionych części wyrażenia.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Znajdź hotele, w których terminy "hotel" i "lotnisko" znajdują się w opisie nie więcej niż pięć słów i w których wszystkie pokoje są dla niepalących. Ta kwerenda używa [pełnego języka zapytania Lucene](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Następne kroki  

- [Filtry w usłudze Azure Cognitive Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
