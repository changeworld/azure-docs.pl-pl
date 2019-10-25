---
title: Informacje o filtrach kolekcji OData
titleSuffix: Azure Cognitive Search
description: Zrozumienie, jak filtry kolekcji OData działają w zapytaniach usługi Azure Wyszukiwanie poznawcze.
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
ms.openlocfilehash: 9a57e1d16b13d822b6f5b541a7f838b0dd3a69ad
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794388"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Informacje o filtrach kolekcji OData na platformie Azure Wyszukiwanie poznawcze

Aby [odfiltrować](query-odata-filter-orderby-syntax.md) pola kolekcji na platformie Azure wyszukiwanie poznawcze, można użyć [operatorów`any` i `all`](search-query-odata-collection-operators.md) razem z **wyrażeniami lambda**. Wyrażenia lambda są wyrażeniami logicznymi odwołującymi się do **zmiennej zakresu**. Operatory `any` i `all` są analogiczne do pętli `for` w większości języków programowania, przy czym zmienna zakresu przyjmuje rolę zmiennej pętli i wyrażenie lambda jako treść pętli. Zmienna zakresu przyjmuje wartość "Current" kolekcji podczas iteracji pętli.

Co najmniej na to, jak działa koncepcyjnie. W rzeczywistości platforma Azure Wyszukiwanie poznawcze implementuje filtry w bardzo różny sposób, w jaki działa `for` pętle. W idealnym przypadku ta różnica byłaby niewidoczna dla Ciebie, ale w niektórych sytuacjach nie jest. Wynik końcowy polega na tym, że istnieją reguły, które należy wykonać podczas pisania wyrażeń lambda.

W tym artykule wyjaśniono, dlaczego istnieją reguły dla filtrów kolekcji, dzięki czemu usługa Azure Wyszukiwanie poznawcze wykonuje te filtry. Jeśli piszesz Zaawansowane filtry z złożonymi wyrażeniami lambda, ten artykuł może być przydatny podczas tworzenia informacji o tym, co można zrobić w filtrach i dlaczego.

Aby uzyskać informacje na temat tego, jakie są reguły dla filtrów kolekcji, łącznie z przykładami, zobacz [Rozwiązywanie problemów z filtrami kolekcji OData na platformie Azure wyszukiwanie poznawcze](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Dlaczego filtry kolekcji są ograniczone

Istnieją trzy podstawowe powody, dla których nie wszystkie funkcje filtru są obsługiwane dla wszystkich typów kolekcji:

1. Tylko niektóre operatory są obsługiwane dla określonych typów danych. Na przykład nie ma sensu porównywania wartości logicznych `true` i `false` przy użyciu `lt`, `gt`i tak dalej.
1. Usługa Azure Wyszukiwanie poznawcze nie obsługuje **skorelowanego wyszukiwania** dla pól typu `Collection(Edm.ComplexType)`.
1. Usługa Azure Wyszukiwanie poznawcze używa odwróconych indeksów do wykonywania filtrów dla wszystkich typów danych, w tym kolekcji.

Pierwszym powodem jest to, że zdefiniowano język OData i system typów modelu EDM. Ostatnie dwa zostały omówione bardziej szczegółowo w dalszej części tego artykułu.

## <a name="correlated-versus-uncorrelated-search"></a>Skorelowane i nieskorelowane wyszukiwanie

W przypadku stosowania wielu kryteriów filtrowania względem kolekcji obiektów złożonych, kryteria są **skorelowane** , ponieważ dotyczą *poszczególnych obiektów w kolekcji*. Na przykład poniższy filtr zwróci Hotele, które mają co najmniej jeden pokój Deluxe o współczynniku mniejszym niż 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Jeśli filtrowanie zostało *nieskorelowane*, powyższy filtr może zwrócić Hotele, w których jedno pomieszczenie jest w trakcie Deluxe, a różne pomieszczenie ma stawkę bazową mniejszą niż 100. Nie byłoby to sensie, ponieważ obie klauzule wyrażenia lambda stosują się do tej samej zmiennej zakresu, a mianowicie `room`. Dlatego takie filtry są skorelowane.

Jednak w przypadku wyszukiwania pełnotekstowego nie ma sposobu odwoływania się do określonej zmiennej zakresu. Jeśli używasz wyszukiwania z polami, aby wystawić [pełne zapytanie Lucene](query-lucene-syntax.md) podobne do tego:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

Możesz uzyskać Hotele w przypadku, gdy w jednym pokoju jest Deluxe, a w opisie znajduje się w innym pokoju. Na przykład dokument poniżej z `Id`em `1` będzie pasować do zapytania:

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

Przyczyną jest to, że `Rooms/Type` odnosi się do wszystkich przeanalizowanych warunków pola `Rooms/Type` w całym dokumencie i podobnie dla `Rooms/Description`, jak pokazano w poniższej tabeli.

Jak `Rooms/Type` są przechowywane na potrzeby wyszukiwania pełnotekstowego:

| Termin w `Rooms/Type` | Identyfikatory dokumentów |
| --- | --- |
| Deluxe | 1, 2 |
| standard | 1 |

Jak `Rooms/Description` są przechowywane na potrzeby wyszukiwania pełnotekstowego:

| Termin w `Rooms/Description` | Identyfikatory dokumentów |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| elementy | 1 |
| znacznie | 1 |
| Motel | 2 |
| zmieścić | 1, 2 |
| standard | 1 |
| protokołów | 1 |
| wyświetl | 1 |

W przeciwieństwie do powyższego filtru, co oznacza, że "dopasowuje dokumenty, gdy pomieszczenie ma `Type` równe" Pokój Deluxe "i **że to samo miejsce** ma `BaseRate` mniejsze niż 100", zapytanie wyszukiwania ma postać "dopasowania dokumentów, w których `Rooms/Type` ma termin" Deluxe "i `Rooms/Description` ma frazę "widok miejscowości". Nie ma koncepcji pojedynczych pokojów, których pola mogą być skorelowane w tym ostatnim przypadku.

> [!NOTE]
> Jeśli chcesz uzyskać pomoc techniczną dla wyszukiwania skorelowanego dodanego do usługi Azure Wyszukiwanie poznawcze, zagłosuj na [ten element głosowy użytkownika](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Odwrócone indeksy i kolekcje

Być może zauważono, że istnieją znacznie mniej ograniczeń w wyrażeniach lambda w złożonych kolekcjach niż w przypadku prostych kolekcji, takich jak `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`i tak dalej. Wynika to z faktu, że platforma Azure Wyszukiwanie poznawcze przechowuje złożone kolekcje jako rzeczywiste kolekcje dokumentów podrzędnych, podczas gdy proste kolekcje nie są przechowywane jako kolekcje.

Rozważmy na przykład pole Kolekcja ciągów z możliwością filtrowania, takie jak `seasons` w indeksie dla sprzedawcy detalicznego w trybie online. Niektóre dokumenty przekazane do tego indeksu mogą wyglądać następująco:

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

Wartości pola `seasons` są przechowywane w strukturze o nazwie **odwrócony indeks**, który wygląda następująco:

| Okres obowiązywania Umowy | Identyfikatory dokumentów |
| --- | --- |
| Spring | 1, 2 |
| wakacj | 1 |
| znajduj | 1, 2 |
| zimow | 2, 3 |

Ta struktura danych została zaprojektowana tak, aby odpowiedzieć na jedno pytanie z dużą szybkością: w których dokumentach występuje dany termin? Odpowiedź na to pytanie działa podobnie jak zwykłe sprawdzanie równości niż pętla w kolekcji. W rzeczywistości jest to dlaczego dla kolekcji ciągów, platforma Azure Wyszukiwanie poznawcze zezwala tylko na `eq` jako operator porównania w wyrażeniu lambda w przypadku `any`.

Po powyższym czasie kompilowania z poziomu równości dowiesz się, jak można połączyć wiele kontroli równości dla tej samej zmiennej zakresu z `or`. Działa to z algebry i rozdzielną [właściwością kwantyfikatorów](https://en.wikipedia.org/wiki/Existential_quantification#Negation). To wyrażenie:

    seasons/any(s: s eq 'winter' or s eq 'fall')

jest równoważne:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

wszystkie dwa podwyrażenia `any` mogą być efektywnie wykonywane przy użyciu odwróconego indeksu. Ponadto, z podziękowaniami dla [kwantyfikatorów](https://en.wikipedia.org/wiki/Existential_quantification#Negation), to wyrażenie:

    seasons/all(s: s ne 'winter' and s ne 'fall')

jest równoważne:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

Dlatego można użyć `all` z `ne` i `and`.

> [!NOTE]
> Chociaż szczegóły wykraczają poza zakres tego dokumentu, te same zasady obejmują również [testy odległości i przecięć dla kolekcji punktów geoprzestrzennych](search-query-odata-geo-spatial-functions.md) . To dlatego, w `any`:
>
> - nie można `geo.intersects` negacji
> - `geo.distance` należy porównać przy użyciu `lt` lub `le`
> - wyrażenia muszą być połączone z `or`, a nie `and`
>
> Reguły reguł stosują się do `all`.

W przypadku filtrowania kolekcji typów danych, które obsługują operatory `lt`, `gt`, `le`i `ge`, na przykład `Collection(Edm.Int32)`. W tym celu można użyć `and`, a także `or` w `any`, tak długo, jak bazowe wyrażenia porównania są łączone w **porównywanie zakresów** przy użyciu `and`, które są następnie dodatkowo połączone przy użyciu `or`. Ta struktura wyrażeń logicznych jest nazywana [disjunctive Normal form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), w przeciwnym razie znanej jako "ORs of ANDs". Odwrotnie, wyrażenia lambda dla `all` dla tych typów danych muszą być w [conjunctive Normal form (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), w przeciwnym razie "ANDs of ORs". Platforma Azure Wyszukiwanie poznawcze umożliwia porównywanie zakresów, ponieważ może je wykonywać przy użyciu odwróconych indeksów wydajnie, podobnie jak w przypadku szybkiego wyszukiwania ciągów.

Podsumowując, poniżej przedstawiono reguły przycisku przewijania dla elementów, które są dozwolone w wyrażeniu lambda:

- W `any`, *pozytywne testy* są zawsze dozwolone, takich jak równość, porównywanie zakresów, `geo.intersects`lub `geo.distance` porównane z `lt` lub `le` (Pomyśl o tym, jak równość, gdy chodzi o sprawdzanie odległości).
- Wewnątrz `any`, `or` jest zawsze dozwolone. `and` można używać tylko dla typów danych, które mogą przeszukiwać zakres kontroli zakresu, i tylko wtedy, gdy używasz ORs of ANDs (DNF).
- W `all`, reguły są odwrócone — dozwolone są tylko *kontrole ujemne* , można użyć `and` zawsze i można użyć `or` tylko do sprawdzania zakresu, wyrażone jako ANDs of ORS (CNF).

W rzeczywistości są to typy filtrów, których najprawdopodobniej będziesz używać. Nadal pomocne jest zrozumienie granic tego, co jest możliwe.

Aby zapoznać się z konkretnymi przykładami, które rodzaje filtrów są dozwolone i które nie są, zobacz [jak napisać prawidłowe filtry kolekcji](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Następne kroki  

- [Rozwiązywanie problemów z filtrami kolekcji OData na platformie Azure Wyszukiwanie poznawcze](search-query-troubleshoot-collection-filters.md)
- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;w interfejsie API REST usługi Azure wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
