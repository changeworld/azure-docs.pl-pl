---
title: Wyszukiwanie rozmyte
titleSuffix: Azure Cognitive Search
description: Zaimplementuj wyszukiwanie "czy masz na myśli", aby automatycznie poprawić błędnie napisany termin lub literówkę.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262438"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Wyszukiwanie rozmyte w celu skorygowania błędów ortograficznych i literówek

Usługa Azure Cognitive Search obsługuje wyszukiwanie rozmyte, typ kwerendy, która kompensuje literówki i błędnie napisane terminy w ciągu wejściowym. Czyni to poprzez skanowanie w poszukiwaniu terminów o podobnym składzie. Rozszerzenie wyszukiwania na bliskie mecze skutkuje automatyczną poprawianiem literówki, gdy rozbieżność to tylko kilka zagubionych znaków. 

## <a name="what-is-fuzzy-search"></a>Co to jest wyszukiwanie rozmyte?

Jest to ćwiczenie ekspansji, które tworzy dopasowanie na warunkach o podobnym składzie. Po określeniu wyszukiwania rozmytego aparat tworzy wykres (oparty na [deterministycznej teorii skończonego automatu)](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)podobnie skomponowanych terminów dla wszystkich całych terminów w zapytaniu. Na przykład, jeśli zapytanie zawiera trzy terminy "uniwersytet w Waszyngtonie", `search=university~ of~ washington~` wykres jest tworzony dla każdego terminu w kwerendzie (nie ma usuwania słowa stop w wyszukiwaniu rozmytym, więc "z" pobiera wykres).

Wykres składa się z maksymalnie 50 rozszerzeń lub permutacji każdego terminu, przechwytując zarówno poprawne, jak i niepoprawne warianty w procesie. Aparat następnie zwraca najwyższej odpowiednich dopasowań w odpowiedzi. 

Dla terminu takiego jak "uniwersytet", wykres może mieć "unversty, universty, uniwersytet, wszechświat, odwrotność". Wszystkie dokumenty, które pasują do tych na wykresie są uwzględniane w wynikach. W przeciwieństwie do innych zapytań, które analizują tekst do obsługi różnych form tego samego słowa ("myszy" i "myszy"), porównania w rozmytej kwerendzie są przyjmowane według wartości nominalnej bez żadnej analizy językowej tekstu. "Wszechświat" i "odwrotność", które są semantycznie różne, będą pasować, ponieważ różnice składniowe są małe.

Dopasowanie zakończy się pomyślnie, jeśli rozbieżności są ograniczone do dwóch lub mniej zmian, gdzie edycja jest wstawiony, usunięty, podstawiony lub transponowany znak. Algorytm korekcji ciągu, który określa różnicę jest [Damerau-Levenshtein metryki odległości,](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) opisane jako "minimalna liczba operacji (wstawienia, usunięcia, podstawienia lub transpozycji dwóch sąsiednich znaków) wymagane do zmiany jednego wyrazu do drugiego". 

W usłudze Azure Cognitive Search:

+ Rozmyte zapytanie dotyczy całych terminów, ale można obsługiwać frazy za pośrednictwem konstrukcji AND. Na przykład "Unviersty~ of~ "Wshington~" będzie pasować do "University of Washington".

+ Domyślna odległość edycji to 2. Wartość `~0` oznacza brak rozszerzenia (tylko dokładny termin jest uważany za dopasowanie), ale można określić `~1` dla jednego stopnia różnicy lub jednej edycji. 

+ Rozmyte zapytanie można rozwinąć termin do 50 dodatkowych permutacji. Ten limit nie jest konfigurowalny, ale można skutecznie zmniejszyć liczbę rozszerzeń, zmniejszając odległość edycji do 1.

+ Odpowiedzi składają się z dokumentów zawierających odpowiednie dopasowanie (do 50).

Łącznie wykresy są przesyłane jako kryteria dopasowania względem tokenów w indeksie. Jak można sobie wyobrazić, rozmyte wyszukiwanie jest z natury wolniejsze niż inne formularze zapytań. Rozmiar i złożoność indeksu można określić, czy korzyści są wystarczające, aby zrównoważyć opóźnienie odpowiedzi.

> [!NOTE]
> Ponieważ wyszukiwanie rozmyte wydaje się być powolne, warto zbadać alternatywy, takie jak indeksowanie n-gram, z postępem sekwencji krótkich znaków (dwie i trzy sekwencje znaków dla tokenów bigram i trygramu). W zależności od języka i powierzchni zapytania n-gram może zapewnić lepszą wydajność. Kompromis polega na tym, że indeksowanie n-gram jest bardzo intensywne i generuje znacznie większe indeksy.
>
> Inną alternatywą, którą można rozważyć, jeśli chcesz obsłużyć tylko najbardziej rażące przypadki, będzie [mapa synonimem](search-synonyms.md). Na przykład mapowanie "szukaj" na "serach, serch, sarch" lub "retrieve" na "retreive".

## <a name="indexing-for-fuzzy-search"></a>Indeksowanie wyszukiwania rozmytego

Analizatory nie są używane podczas przetwarzania zapytań do tworzenia wykresu rozszerzenia, ale to nie znaczy, analizatory powinny być ignorowane w scenariuszach wyszukiwania rozmyte. Po tym wszystkim analizatory są używane podczas indeksowania do tworzenia tokenów, względem których dopasowywanie jest wykonywane, czy kwerenda jest formularz swobodny, filtrowane wyszukiwanie lub rozmyte wyszukiwania z wykresem jako dane wejściowe. 

Ogólnie rzecz biorąc podczas przypisywania analizatorów na podstawie pola, decyzja o dostrojeniu łańcucha analizy opiera się na podstawowym przypadku użycia (filtr lub wyszukiwanie pełnotekstowe), a nie wyspecjalizowanych formach zapytań, takich jak wyszukiwanie rozmyte. Z tego powodu nie ma konkretnego zalecenia analizatora dla wyszukiwania rozmytego. 

Jeśli jednak kwerendy testowe nie tworzą oczekiwanych dopasowań, możesz spróbować zmienić analizator indeksowania, ustawiając go na [analizator języka,](index-add-language-analyzers.md)aby sprawdzić, czy uzyskasz lepsze wyniki. Niektóre języki, szczególnie te z mutacjami samogłosek, mogą korzystać z przegięcia i nieregularnych formularzy wyrazowych generowanych przez procesory języka naturalnego firmy Microsoft. W niektórych przypadkach przy użyciu analizatora języka prawo może mieć wpływ na to, czy termin jest tokenized w sposób, który jest zgodny z wartością dostarczoną przez użytkownika.

## <a name="how-to-use-fuzzy-search"></a>Jak korzystać z wyszukiwania rozmytego

Rozmyte kwerendy są konstruowane przy użyciu pełnej składni kwerendy Lucene, wywołując [analizator zapytania Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Ustaw pełną analizator lucene na`queryType=full`kwerendzie ( ).

1. Opcjonalnie zakres żądania do określonych pól,`searchFields=<field1,field2>`przy użyciu tego parametru ( ). 

1. Dołącz operatora tyldy (`~`) na koniec`search=<string>~`całego okresu ( ).

   Dołącz parametr opcjonalny, liczbę z 0 do 2 (domyślnie),`~1`jeśli chcesz określić odległość edycji ( ). Na przykład "blue~" lub "blue~1" zwróci "niebieski", "blues" i "glue".

W usłudze Azure Cognitive Search, oprócz terminu i odległości (maksymalnie 2), nie ma żadnych dodatkowych parametrów, aby ustawić w kwerendzie.

> [!NOTE]
> Podczas przetwarzania zapytań rozmyte zapytania nie są poddawane [analizie leksykalne](search-lucene-query-architecture.md#stage-2-lexical-analysis). Dane wejściowe kwerendy są dodawane bezpośrednio do drzewa zapytań i rozwijane w celu utworzenia wykresu terminów. Jedyną wykonawaną transformacją jest niższa obudowa.

## <a name="testing-fuzzy-search"></a>Testowanie wyszukiwania rozmytego

W przypadku prostego testowania zalecamy [Eksploratora wyszukiwania](search-explorer.md) lub [postmana](search-get-started-postman.md) do iteracji za pomocą wyrażenia zapytania. Oba narzędzia są interaktywne, co oznacza, że można szybko przejść przez wiele wariantów terminu i ocenić odpowiedzi, które wracają.

Gdy wyniki są niejednoznaczne, [wyróżnianie trafień](search-pagination-page-layout.md#hit-highlighting) może pomóc zidentyfikować dopasowanie w odpowiedzi. 

> [!Note]
> Użycie podświetlania trafień do identyfikacji rozmytych dopasowań ma ograniczenia i działa tylko w przypadku podstawowego wyszukiwania rozmytego. Jeśli indeks ma profile oceniania lub jeśli warstwa kwerendy z dodatkową składnią, naciśnięcie wyróżniania może nie zidentyfikować dopasowania. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Przykład 1: wyszukiwanie rozmyte z dokładnym terminem

Załóżmy, że w `"Description"` polu w dokumencie wyszukiwania istnieje następujący ciąg znaków:`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Zacznij od wyszukiwania rozmytego na "specjalne" i dodaj podświetlenie trafień do pola Opis:

    search=special~&highlight=Description

W odpowiedzi, ponieważ dodano wyróżnienie, formatowanie jest stosowane do "specjalne" jako pasujący termin.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Spróbuj ponownie, błędnie ortograficzny "specjalne" przez wyjęcie kilku liter ("pe"):

    search=scial~&highlight=Description

Jak dotąd, nie ma zmian w odpowiedzi. Użycie domyślnej odległości 2 stopni, usunięcie dwóch znaków "pe" z "special" nadal pozwala na pomyślne dopasowanie tego terminu.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Próbując jeszcze jednego żądania, zmodyfikuj wyszukiwany termin, wyjmując ostatni znak w sumie za trzy skreślenia (od "specjalnego" do "scal"):

    search=scal~&highlight=Description

Należy zauważyć, że ta sama odpowiedź jest zwracana, ale teraz zamiast dopasowywania na "specjalne", rozmyte dopasowanie jest na "SQL".

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

Punktem tego rozszerzonego przykładu jest zilustrowanie jasności, że naciśnięcie podkreślając może doprowadzić do niejednoznacznych wyników. We wszystkich przypadkach zwracany jest ten sam dokument. Gdyby oparcie się na identyfikatorach dokumentu, aby zweryfikować dopasowanie, być może przegapiłeś przejście z "specjalnego" na "SQL".

## <a name="see-also"></a>Zobacz też

+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search (architektura analizowania zapytań)](search-lucene-query-architecture.md)
+ [Eksplorator wyszukiwania](search-explorer.md)
+ [Jak kwerendy w .NET](search-query-dotnet.md)
+ [Jak zapytać w REST](search-create-index-rest-api.md)
