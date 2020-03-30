---
title: Opis filtrów kolekcji OData
titleSuffix: Azure Cognitive Search
description: Poznaj mechanikę działania filtrów kolekcji OData w zapytaniach usługi Azure Cognitive Search, w tym ograniczenia i zachowania unikatowe dla kolekcji.
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
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113073"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Opis filtrów kolekcji OData w usłudze Azure Cognitive Search

Aby [filtrować](query-odata-filter-orderby-syntax.md) pola kolekcji w usłudze Azure Cognitive Search, można użyć [ `any` operatorów `all` i razem](search-query-odata-collection-operators.md) z **wyrażeniami lambda**. Wyrażenia Lambda są wyrażeniami logicznymi, które odwołują się do **zmiennej zakresu**. I `any` `all` operatorów są analogiczne do `for` pętli w większości języków programowania, ze zmienną zakresu przy roli zmiennej pętli i wyrażenie lambda jako treść pętli. Zmienna zakresu przyjmuje "bieżącą" wartość kolekcji podczas iteracji pętli.

Przynajmniej tak to działa koncepcyjnie. W rzeczywistości usługa Azure Cognitive Search implementuje filtry `for` w zupełnie inny sposób niż działają pętle. Idealnie, różnica ta byłaby dla Ciebie niewidoczna, ale w niektórych sytuacjach tak nie jest. Efektem końcowym jest to, że istnieją zasady, których należy przestrzegać podczas pisania wyrażeń lambda.

W tym artykule wyjaśniono, dlaczego istnieją reguły filtrów zbierania danych, eksplorując sposób wykonywania tych filtrów przez usługę Azure Cognitive Search. Jeśli piszesz zaawansowane filtry ze złożonymi wyrażeniami lambda, możesz znaleźć ten artykuł przydatny w budowaniu zrozumienia, co jest możliwe w filtrach i dlaczego.

Aby uzyskać informacje na temat reguł filtrów zbierania danych, w tym przykłady, zobacz [Rozwiązywanie problemów z filtrami kolekcji OData w usłudze Azure Cognitive Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Dlaczego filtry kolekcji są ograniczone

Istnieją trzy podstawowe powody, dla których nie wszystkie funkcje filtru są obsługiwane dla wszystkich typów kolekcji:

1. Tylko niektóre operatory są obsługiwane dla niektórych typów danych. Na `true` przykład nie ma sensu porównywać wartości logicznych i `false` używać `lt`, `gt`i tak dalej.
1. Usługa Azure Cognitive Search nie obsługuje **skorelowanego wyszukiwania** w polach typu. `Collection(Edm.ComplexType)`
1. Usługa Azure Cognitive Search używa odwróconych indeksów do wykonywania filtrów we wszystkich typach danych, w tym kolekcjach.

Pierwszy powód jest tylko konsekwencją tego, jak zdefiniowano język OData i system typu EDM. Dwa ostatnie są wyjaśnione bardziej szczegółowo w dalszej części tego artykułu.

## <a name="correlated-versus-uncorrelated-search"></a>Skorelowane i nieskorelowane wyszukiwanie

Podczas stosowania wielu kryteriów filtrowania w kolekcji złożonych obiektów, kryteria są **skorelowane,** ponieważ mają zastosowanie do *każdego obiektu w kolekcji*. Na przykład następujący filtr zwróci hotele, które mają co najmniej jeden pokój typu deluxe o cenie mniejszej niż 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Jeśli filtrowanie było *nieskorelowane, powyższy*filtr może zwrócić hotele, w których jeden pokój jest deluxe, a inny pokój ma podstawową stawkę mniejszą niż 100. To nie miałoby sensu, ponieważ obie klauzule wyrażenia lambda dotyczą tej `room`samej zmiennej zakresu, a mianowicie . Dlatego takie filtry są skorelowane.

Jednak w przypadku wyszukiwania pełnotekstowego nie ma możliwości odwoływania się do określonej zmiennej zakresu. Jeśli używasz wyszukiwania fielded do wydania [pełnego zapytania Lucene](query-lucene-syntax.md) jak ten:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

możesz dostać hotele z powrotem, gdzie jeden pokój jest deluxe, a inny pokój wspomina "widok na miasto" w opisie. Na przykład poniższy `Id` dokument `1` z of będzie pasować do kwerendy:

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

Powodem jest `Rooms/Type` to, że odnosi się `Rooms/Type` do wszystkich analizowanych warunków pola `Rooms/Description`w całym dokumencie, a podobnie dla , jak pokazano w poniższych tabelach.

Jak `Rooms/Type` jest przechowywany do wyszukiwania pełnotekstowego:

| Termin w`Rooms/Type` | Identyfikatory dokumentów |
| --- | --- |
| Deluxe | 1, 2 |
| Standardowych | 1 |

Jak `Rooms/Description` jest przechowywany do wyszukiwania pełnotekstowego:

| Termin w`Rooms/Description` | Identyfikatory dokumentów |
| --- | --- |
| Courtyard | 2 |
| city | 1 |
| Ogród | 1 |
| Dużych | 1 |
| Motel | 2 |
| pokój | 1, 2 |
| Standardowych | 1 |
| Suite | 1 |
| widok | 1 |

Tak więc w przeciwieństwie do powyższego filtru, `Type` który w zasadzie mówi "dokumenty `BaseRate` meczowe, w których pokój ma równy `Rooms/Type` "Pokój Deluxe", a `Rooms/Description` **ten sam pokój** ma mniej niż 100", zapytanie mówi "dokumenty meczowe, w których ma termin "deluxe" i ma wyrażenie "widok na miasto". Nie ma pojęcia poszczególnych pomieszczeń, których pola mogą być skorelowane w tym ostatnim przypadku.

> [!NOTE]
> Jeśli chcesz zobaczyć obsługę skorelowanego wyszukiwania dodaną do usługi Azure Cognitive Search, zagłosuj na [ten element User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Odwrócone indeksy i kolekcje

Być może zauważyłeś, że istnieje znacznie mniej ograniczeń dotyczących wyrażeń lambda w `Collection(Edm.Int32)`odniesieniu `Collection(Edm.GeographyPoint)`do złożonych kolekcji niż w przypadku prostych kolekcji, takich jak , i tak dalej. Dzieje się tak, ponieważ usługa Azure Cognitive Search przechowuje złożone kolekcje jako rzeczywiste kolekcje dokumentów podrzędnych, podczas gdy kolekcje proste nie są w ogóle przechowywane jako kolekcje.

Rozważmy na przykład pole kolekcji `seasons` ciągów można filtrować, takie jak w indeksie dla sprzedawcy online. Niektóre dokumenty przesłane do tego indeksu mogą wyglądać następująco:

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

Wartości `seasons` pola są przechowywane w strukturze zwanej **odwróconym indeksem**, która wygląda mniej więcej tak:

| Termin | Identyfikatory dokumentów |
| --- | --- |
| Wiosna | 1, 2 |
| Letnich | 1 |
| Jesień | 1, 2 |
| Zimowe | 2, 3 |

Ta struktura danych ma na celu udzielenie odpowiedzi na jedno pytanie z dużą prędkością: W jakich dokumentach pojawia się dany termin? Odpowiedź na to pytanie działa bardziej jak zwykły sprawdzanie równości niż pętla nad kolekcją. W rzeczywistości jest to dlaczego dla kolekcji ciągów, Usługa Azure Cognitive Search `eq` `any`zezwala tylko jako operator porównania wewnątrz wyrażenia lambda dla .

Opierając się od równości, następnie przyjrzymy się, jak to możliwe, aby `or`połączyć wiele kontroli równości na tej samej zmiennej zakresu z . Działa dzięki algebrze i [właściwości dystrybucyjnej kwantyfikatorów.](https://en.wikipedia.org/wiki/Existential_quantification#Negation) To wyrażenie:

    seasons/any(s: s eq 'winter' or s eq 'fall')

odpowiada:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

i każde z `any` dwóch wyrażeń podrzędnych może być skutecznie wykonywane przy użyciu odwróconego indeksu. Również, dzięki [ustawie negacji kwantyfikatorów,](https://en.wikipedia.org/wiki/Existential_quantification#Negation)wyrażenie to:

    seasons/all(s: s ne 'winter' and s ne 'fall')

odpowiada:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

dlatego można używać `all` z `ne` i `and`.

> [!NOTE]
> Chociaż szczegóły wykraczają poza zakres tego dokumentu, te same zasady obejmują [również testy odległości i przecięcia dla zbiorów punktów geoprzestrzennych.](search-query-odata-geo-spatial-functions.md) Dlatego w `any`:
>
> - `geo.intersects`nie można zaprzeczyć
> - `geo.distance`należy porównać `lt` za pomocą lub`le`
> - wyrażenia muszą być `or`łączone z , a nie`and`
>
> Zasady odwrotne `all`mają zastosowanie do .

Szersza różnorodność wyrażeń jest dozwolona podczas filtrowania kolekcji `lt`typów `gt` `le`danych `ge` obsługujących `Collection(Edm.Int32)` program , , i operatorów, takich jak na przykład. W szczególności, można `and` użyć, `or` `any`jak również w , tak długo, jak podstawowe `and`wyrażenia porównania są `or`łączone w porównania **zakres przy** użyciu , które są następnie dalej łączone za pomocą . Ta struktura wyrażeń logicznych jest nazywana [odłączoną normalną formą (DNF),](https://en.wikipedia.org/wiki/Disjunctive_normal_form)inaczej zwaną "ORs of ANDs". Z drugiej strony wyrażenia lambda dla `all` tych typów danych muszą być w formie [normalnej (CNF) (](https://en.wikipedia.org/wiki/Conjunctive_normal_form) Usługa Azure Cognitive Search umożliwia takie porównania zakresu, ponieważ można je wykonać przy użyciu indeksów odwróconych wydajnie, podobnie jak można zrobić szybkie wyszukiwanie terminów dla ciągów.

Podsumowując, oto zasady kciuka dla tego, co jest dozwolone w wyrażeniu lambda:

- Wewnątrz, `any` *pozytywne kontrole* są zawsze dozwolone, jak `geo.intersects`równość, porównania zakresu, lub `geo.distance` w porównaniu z `lt` lub `le` (myśleć o "bliskość" jako równość, jeśli chodzi o sprawdzanie odległości).
- Wewnątrz `any` `or` , jest zawsze dozwolone. Można używać `and` tylko dla typów danych, które mogą wyrażać sprawdzanie zakresu i tylko wtedy, gdy używasz obiektów ORS (DNF).
- Wewnątrz `all`reguły są odwrócone - dozwolone są tylko `and` *kontrole negatywne,* można `or` używać zawsze i można używać tylko do kontroli zakresu wyrażonych jako identyfikatory AND regionów ORS (CNF).

W praktyce są to typy filtrów, które i tak najprawdopodobniej użyjesz. Nadal warto jednak zrozumieć granice tego, co jest możliwe.

Dla konkretnych przykładów, które rodzaje filtrów są dozwolone, a które nie, zobacz [Jak napisać prawidłowe filtry kolekcji](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Następne kroki  

- [Rozwiązywanie problemów z filtrami kolekcji OData w usłudze Azure Cognitive Search](search-query-troubleshoot-collection-filters.md)
- [Filtry w usłudze Azure Cognitive Search](search-filters.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
