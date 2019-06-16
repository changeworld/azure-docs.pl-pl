---
title: Objaśnienie filtrów kolekcji OData — usługa Azure Search
description: Zrozumienie, jak działa filtr kolekcji OData w zapytaniach usługi Azure Search.
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
ms.openlocfilehash: 7af1b0ab95d04249d6d74e3324dbeb30eda6e1de
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079602"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>Objaśnienie filtrów kolekcji OData w usłudze Azure Search

Aby [filtru](query-odata-filter-orderby-syntax.md) kolekcji pól w usłudze Azure Search, możesz użyć [ `any` i `all` operatory](search-query-odata-collection-operators.md) wraz z **wyrażeń lambda**. Wyrażenia lambda są wyrażenia logiczne, które odwołują się do **zmiennej zakresu**. `any` i `all` operatory są analogiczne do `for` pętli w większości języków programowania, ze zmienną zakresu, biorąc rolę zmienna pętli i wyrażenia lambda jako treść pętli. Zmienna zakresu przyjmuje wartość "bieżący" kolekcji podczas iteracji pętli.

Co najmniej, jak działa pod względem koncepcyjnym. W praktyce w usłudze Azure Search implementuje filtry w bardzo różnią się sposobem jak `for` pracy w pętli. W idealnym przypadku ta różnica będzie niewidoczna dla Ciebie, ale w niektórych sytuacjach nie jest. Wynik końcowy jest, że nie istnieją reguły, które należy wykonać podczas pisania wyrażeń lambda.

W tym artykule opisano, dlaczego reguły filtrów w kolekcji istnieje, eksplorując, jak usługa Azure Search wykonuje te filtry. Jeśli piszesz filtry zaawansowane za pomocą wyrażenia lambda złożonych, pomocne może być w tym artykule w tworzeniu zrozumieć, co jest możliwe w filtrach i dlaczego.

Aby uzyskać informacji o co reguły dla kolekcji filtrów, wraz z przykładami, zobacz [filtry kolekcji OData rozwiązywania problemów w usłudze Azure Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Dlaczego filtry gromadzenia są ograniczone

Istnieją trzy podstawowe powody Dlaczego nie wszystkie funkcje filtra są obsługiwane dla wszystkich typów kolekcji:

1. Tylko niektóre operatory są obsługiwane w przypadku niektórych typów danych. Na przykład, nie ma sensu porównanie wartości logicznych `true` i `false` przy użyciu `lt`, `gt`i tak dalej.
1. Usługa Azure Search nie obsługuje **skorelowany wyszukiwania** na polach typu `Collection(Edm.ComplexType)`.
1. Usługa Azure Search korzysta odwrócony indeksy, aby wykonać filtry za pośrednictwem wszystkich typów danych, łącznie z kolekcjami.

Pierwszym powodem jest po prostu konsekwencją sposób definiowania języka OData i system typu EDM. Ostatnie dwa są omówione bardziej szczegółowo w dalszej części tego artykułu.

## <a name="correlated-versus-uncorrelated-search"></a>Skorelowane w porównaniu z nieskorelowane wyszukiwania

Podczas stosowania wielu kryteriów filtrowania przez kolekcję obiektów złożonych, kryteria są **skorelowany** ponieważ mają one zastosowanie do *każdy obiekt w kolekcji*. Na przykład następujący filtr zwróci hotele, które mają co najmniej jeden deluxe pomieszczeniu o szybkości mniejszej niż 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Jeśli filtrowanie *nieskorelowane*, powyższy Filtr spowodował może zwrócić hotele, gdzie jednego miejsca jest deluxe, a innego pokoju ma ustawiony atrybut podstawowy szybkości mniej niż 100. Który nie miałoby sensu, ponieważ obie klauzule wyrażenia lambda z tą samą zmienną zakresu, to znaczy `room`. Jest to, dlaczego takie filtry są powiązane.

Dla wyszukiwania pełnotekstowego, istnieje jednak sposób odwoływać się do zmiennej określonego zakresu. Jeśli używasz fielded wyszukiwania do wystawiania [pełne zapytanie w języku Lucene](query-lucene-syntax.md) wskazanego, takich jak:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

hotele, ponownie gdzie jednego miejsca jest deluxe, a innego pokoju wspomniany "Miasto view" mogą być wyświetlane w opisie. Na przykład dokument poniżej z `Id` z `1` będzie odpowiadać na kwerendy:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

Przyczyną jest fakt, że `Rooms/Type` odnosi się do wszystkich analizowanych warunków `Rooms/Type` pola w całym dokumencie i podobnie dla `Rooms/Description`, jak pokazano w poniższych tabelach.

Jak `Rooms/Type` są przechowywane przez wyszukiwanie pełnotekstowe:

| Termin w `Rooms/Type` | Identyfikatory dokumentów |
| --- | --- |
| deluxe | 1, 2 |
| standard | 1 |

Jak `Rooms/Description` są przechowywane przez wyszukiwanie pełnotekstowe:

| Termin w `Rooms/Description` | Identyfikatory dokumentów |
| --- | --- |
| podwórze | 2 |
| city | 1 |
| Ogród | 1 |
| Duże | 1 |
| motel | 2 |
| Miejsca | 1, 2 |
| standard | 1 |
| pakiet | 1 |
| wyświetl | 1 |

Więc inaczej niż w przypadku filtr powyżej, który zasadniczo jest wyświetlany komunikat "odpowiada dokumentów, w której ma miejsce `Type` równa"Deluxe pokoju"i **tym samym pomieszczeniu** ma `BaseRate` mniej niż 100", zapytanie wyszukiwania jest wyświetlany komunikat "dopasowanie dokumenty, gdzie `Rooms/Type`ma termin "deluxe" i `Rooms/Description` widział frazę "Miasto". Nie ma żadnych koncepcji pomieszczenia, których pola mogą zostać skorelowane w drugim przypadku.

> [!NOTE]
> Jeśli chcesz zobaczyć obsługuje skorelowanych wyszukiwania dodane do usługi Azure Search, Zagłosuj na [tego elementu User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Odwrócony indeksy i kolekcji

Być może zauważono, że są znacznie mniejszej liczby ograniczenia dotyczące wyrażeń lambda za pośrednictwem złożonych kolekcji, niż jest dla prostych kolekcji, takich jak `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`i tak dalej. Jest to spowodowane usługi Azure Search przechowuje złożonych kolekcje jako rzeczywisty kolekcje podrzędne dokumentów, podczas gdy prostych kolekcji nie są przechowywane jako kolekcje w ogóle.

Rozważmy na przykład pola kolekcji można filtrować ciągu takich jak `seasons` systemu sklepie internetowym. Niektóre dokumenty, przekazywane do ten indeks może wyglądać następująco:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

Wartości `seasons` pola są przechowywane w strukturę o nazwie **odwrócony indeksu**, który wygląda następująco:

| Termin | Identyfikatory dokumentów |
| --- | --- |
| Spring | 1, 2 |
| lata | 1 |
| dzielą się | 1, 2 |
| Igrzyska | 2, 3 |

Ta struktura danych została zaprojektowana w odpowiedzi na jedno pytanie z dużą szybkością: W dokumentach, które ma się dany okres? Odpowiedź na to pytanie działa więcej takich jak sprawdzanie zwykły równości niż pętli za pośrednictwem kolekcji. W rzeczywistości, to dlaczego kolekcji ciągów, usługa Azure Search może składać się `eq` jako operator porównania wewnątrz wyrażenia lambda do `any`.

Budowanie z równości, obok Zapoznamy się jak jest możliwość łączenia wielu kontrolach równości w tej samej zmiennej zakresu `or`. Działa dzięki algebry i [podziału właściwość Kwantyfikatory](https://en.wikipedia.org/wiki/Existential_quantification#Negation). To wyrażenie:

    seasons/any(s: s eq 'winter' or s eq 'fall')

jest równoważne:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

a każdy z dwóch `any` wyrażeń podrzędnych można efektywnie wykonać przy użyciu indeksu odwrócony. Ponadto podziękowania dla [prawa negacji Kwantyfikatory](https://en.wikipedia.org/wiki/Existential_quantification#Negation), to wyrażenie:

    seasons/all(s: s ne 'winter' and s ne 'fall')

jest równoważne:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

Dlatego jest możliwe użycie `all` z `ne` i `and`.

> [!NOTE]
> Mimo że szczegółowe informacje wykraczają poza zakres tego dokumentu, te te same zasady dotyczyć [odległości i wspólną testów dla kolekcji punktów geoprzestrzenne](search-query-odata-geo-spatial-functions.md) także. To dlatego, `any`:
>
> - `geo.intersects` nie może być ujemna.
> - `geo.distance` należy porównać za pomocą `lt` lub `le`
> - wyrażenia musi być łączona z `or`, a nie `and`
>
> Przeciwny reguły dla `all`.

Szerszy zakres wyrażenia są dozwolone, gdy filtrowanie kolekcji danych typy obsługujące `lt`, `gt`, `le`, i `ge` operatorów, takich jak `Collection(Edm.Int32)` na przykład. W szczególności można użyć `and` także `or` w `any`, tak długo, jak bazowego wyrażeniach porównania są łączone w **zakresu porównania** przy użyciu `and`, które następnie są dodatkowe łączyć, używając `or`. Ta struktura wyrażenia logiczną nosi nazwę [rozłącznego normalny formularza (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), znanych także jako "ORs ANDs". Z drugiej strony, wyrażenia lambda do `all` dla tych danych typy muszą być w [opcją łącznej normalny formularza (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), znanych także jako "ANDs ORs". Usługa Azure Search umożliwia takie porównania zakresu, ponieważ może on wykonać je za pomocą odwrócony indeksów, tak samo, jak można wykonać szybkie termin wyszukiwania ciągów.

Podsumowując w tym miejscu są reguły akceptacji dla dozwolonych w wyrażeniu lambda:

- Wewnątrz `any`, *dodatnią kontroli* są zawsze dozwolone, takie jak równości, porównania zakresu `geo.intersects`, lub `geo.distance` w porównaniu z `lt` lub `le` (reakcji "bliskość", jak np. równość Jeśli chodzi o sprawdzanie odległość).
- Wewnątrz `any`, `or` jest zawsze dozwolona. Możesz użyć `and` tylko dla typów danych, które można wyrazić kontroli zakresu i tylko wtedy, gdy używasz ORs ANDs (DNF).
- Wewnątrz `all`, reguły zostały cofnięte — tylko *ujemne kontroli* są dozwolone, możesz użyć `and` zawsze można użyć `or` tylko dla zakresu kontroli, wyrażone jako ANDs z ORs (opcją CNF).

W praktyce są to typy filtrów, które są najbardziej prawdopodobne użyć mimo wszystko. Jest nadal pomocny, aby zrozumieć granic, co jest możliwe jednak.

Konkretne przykłady są dozwolone typy filtrów i które nie są zobacz [jak prawidłową kolekcję filtrów zapisu](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Kolejne kroki  

- [Rozwiązywanie problemów z filtrów kolekcji OData w usłudze Azure Search](search-query-troubleshoot-collection-filters.md)
- [Filtry w usłudze Azure Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
