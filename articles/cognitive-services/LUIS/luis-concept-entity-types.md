---
title: Opis typów jednostek w aplikacjach LUIS na platformie Azure | Dokumentacja firmy Microsoft
description: Dodaj jednostki (kluczowe dane w domenie aplikacji) w aplikacji usługi inteligentnego opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/28/2018
ms.author: v-geberr
ms.openlocfilehash: 01f451f7a3e09aacb029c2194044320717bfae96
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083253"
---
# <a name="entities-in-luis"></a>Jednostki w LUIS

Jednostki są słów ani fraz w zniesławiających, które są dane klucza w domenie aplikacji.

## <a name="entity-compared-to-intent"></a>Jednostki w porównaniu z celem
Jednostka reprezentuje słowo lub frazę w utterance, które mają zostać wyodrębnione. Utterance mogą obejmować cały wiele jednostek lub none. Jednostka reprezentuje klasę, łącznie z kolekcji podobnych obiektów (miejsc, rzeczy, osoby, zdarzenia lub pojęcia). Jednostek opisano informacje dotyczące zamiar, a czasami są istotne dla aplikacji w celu wykonania tego zadania. Na przykład aplikacji wyszukiwania wiadomości mogą obejmować jednostek, takich jak "temat", "source", "— słowo kluczowe" i "publikowania data", które danych klucza do wyszukania wiadomości. W aplikacji rezerwacji podróży, "Lokalizacja", "date", "linii lotniczych" "podróż class" i "bilety" znajdują się informacje o kluczu dla rezerwacji transmitowane (dotyczy celem "Bookflight").

W porównaniu zamiar reprezentuje Prognozowanie cały utterance. 

## <a name="entities-represent-data"></a>Jednostek reprezentacji danych
Jednostki są dane, które chcesz ściągnąć z utterance. Może to być nazwą, Data, nazwa produktu lub dowolną grupę słów. 

|Wypowiedź|Jednostka|Dane|
|--|--|--|
|Kup bilety 3 w Nowym Jorku|Numer wbudowane<br>Location.Destination|3<br>Nowy Jork|
|Kup biletu z nowego Jorku do Londynu na 5 marca|Location.Origin<br>Location.Destination<br>Wbudowane datetimeV2|Nowy Jork<br>Londyn<br>5 marca 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Jednostki są opcjonalne, ale zdecydowanie zalecane
Profile są wymagane, jednostki są opcjonalne. Jest konieczne tworzenie jednostki dla każdej koncepcji w aplikacji, ale tylko w przypadku wymaganych aplikacji podjęcia odpowiednich działań. 

Jeśli Twoje zniesławiających nie ma szczegółów potrzebne w Twojej bot aby kontynuować, nie trzeba je dodać. Miarę rozwoju aplikacji, należy je dodać później. 

Jeśli nie masz pewności, jak możesz skorzystać z informacji, należy dodać kilka typowych wbudowane jednostki na przykład datetimeV2 porządkowej, poczty e-mail i numer telefonu.

## <a name="label-for-word-meaning"></a>Etykieta dla programu word znaczenie
Jeśli wybór programu word lub word rozmieszczenie jest taki sam, ale nie oznacza to samo, nie etykietę go z jednostką. 

Następujące zniesławiających, wyraz `fair` jest Homogram. Została wpisana taki sam, ale ma inne znaczenie:

```
What kind of county fairs are happening in the Seattle area this summer?
Is the current rating for the Seattle review fair?
```

Jeśli potrzebujesz jednostki zdarzeń, aby znaleźć dane wszystkich zdarzeń etykietę wyraz `fair` w pierwszym utterance, ale nie w ciągu sekundy.

## <a name="entities-are-shared-across-intents"></a>Jednostki są współużytkowane przez intencje
Jednostki są współużytkowane przez lokalizacji docelowych. Nie należą do żadnych zamiarów pojedynczego. Można skojarzyć semantycznie intencje i jednostek, ale nie jest wyłączne relacji.

W utterance "Książka mnie bilet, aby Paryża", "Paryża" to jednostka typu lokalizacji. Przez rozpoznawanie jednostek, które są wymienione w danych wejściowych użytkownika, LUIS pomaga wybrać konkretne działania z celem.

## <a name="assign-entities-in-none-intent"></a>Przypisz jednostki w brak konwersji
Wszystkie opcje, w tym **Brak** przeznaczeniu powinny mieć jednostek z etykietą. Dzięki temu LUIS Dowiedz się więcej o którym jednostek w zniesławiających i wyrazy są wokół jednostek. 

## <a name="types-of-entities"></a>Typy jednostek
LUIS oferuje wiele typów jednostek; wbudowane jednostek niestandardowych maszyny pokazaliśmy jednostek i listy jednostek.

| Name (Nazwa) | Można opisać | Opis |
| -- |--|--|
| **Wbudowane** <br/>[Niestandardowe](#prebuilt)| |  **Definicja**<br>Wbudowane typy, które reprezentują wspólne pojęcia. <br><br>**Lista**<br/>numer frazy klucza, liczba porządkowa temperatury, wymiaru, pieniędzy, wieku, procent, poczty e-mail, adres URL, numer telefonu i hasło klucza. <br><br>Nazwy jednostek wbudowane są zastrzeżone. <br><br>Wszystkie wbudowane jednostki, które są dodawane do aplikacji są zwracane w [punktu końcowego](luis-glossary.md#endpoint) zapytania. Aby uzyskać więcej informacji, zobacz [wbudowane jednostek](./Pre-builtEntities.md). <br/><br/>[Przykład odpowiedzi dla jednostki](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **Wyrażenie regularne**<br/>[Wyrażenia regularnego](#regex)||**Definicja**<br>Niestandardowe wyrażenie regularne tekstu sformatowanego utterance raw. Ignoruje wielkość liter, a ignoruje kultury variant.  <br><br>Ta jednostka jest prawidłowa dla słów ani fraz spójnie sformatowane przy każdej zmianie, który również jest spójna.<br><br>Dopasowywanie wyrażeń regularnych są stosowane po zmiany sprawdzanie pisowni. <br><br>Jeśli wyrażenie regularne jest zbyt złożone, takiej jak dużo nawiasów, nie jest możliwe do dodania do modelu wyrażenie. <br><br>**Przykład**<br>`kb[0-9]{6,}` kb123456 dopasowań.<br/><br/>[Szybki start](luis-quickstart-intents-regex-entity.md)<br>[Przykład odpowiedzi dla jednostki](luis-concept-data-extraction.md)|
| **Proste** <br/>[Rozpoznane maszyny](#machine-learned) | ✔ | **Definicja**<br>Proste jednostka jest ogólny jednostki, która opisuje jednej koncepcji i jest zostały uzyskane na podstawie kontekstu rozpoznane maszyny. Kontekst obejmują wybór word, umieszczania programu word i utterance długości.<br/><br/>Jest to dobry jednostki dla słów ani fraz, które nie są spójnie sformatowane, ale wskazać to samo. <br/><br/>[Szybki start](luis-quickstart-primary-and-secondary-data.md)<br/>[Przykład odpowiedzi dla jednostki](luis-concept-data-extraction.md#simple-entity-data)|  
| **Lista** <br/>[Dokładnego dopasowania](#exact-match)|| **Definicja**<br>Listy jednostek reprezentują zestaw stały, zamkniętego powiązanych słów wraz z ich synoymns w systemie. <br><br>Każdy obiekt listy mogą mieć jeden lub więcej formularzy. Najlepiej nadaje się do znanego zestawu odmiany sposoby reprezentują tego samego pojęcia.<br/><br/>LUIS nie wykryje dodatkowe wartości dla jednostek z listy. Użyj wyświetlić [semantycznego słownika](luis-glossary.md#semantic-dictionary) propozycje dla nowych słów na podstawie bieżącej listy.<br/><br>Jeśli istnieje więcej niż jednej jednostki listy z tą samą wartością, każdy obiekt jest zwracany w zapytania punktu końcowego. <br/><br/>[Szybki start](luis-quickstart-intent-and-list-entity.md)<br>[Przykład odpowiedzi dla jednostki](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[Mieszane](#mixed) | ✔|**Definicja**<br>Patterns.any jest używana tylko w utterance szablonu wzorzec do oznaczania, gdy jednostka rozpoczęcia i zakończenia symbol zastępczy o zmiennej długości.  <br><br>**Przykład**<br>Podana utterance wyszukiwanie na podstawie tytułu książek, pattern.any wyodrębnia pełną tytułu. Jest utterance szablonu, za pomocą pattern.any `Who wrote {BookTitle}[?]`.<br/><br/>[Samouczek](luis-tutorial-pattern.md)<br>[Przykład odpowiedzi dla jednostki](luis-concept-data-extraction.md#composite-entity-data)|  
| **Złożone** <br/>[Rozpoznane maszyny](#machine-learned) | ✔|**Definicja**<br>Złożone jednostki składa się z innych jednostek, takich jak wbudowane jednostek i proste. Osobne jednostki tworzą całej jednostki. Lista jednostek nie są dozwolone w złożonych jednostek. <br><br>**Przykład**<br>Obiekt złożony o nazwie PlaneTicketOrder może mieć wbudowane jednostek podrzędnych `number` i `ToLocation`. <br/><br/>[Samouczek](luis-tutorial-composite-entity.md)<br>[Przykład odpowiedzi dla jednostki](luis-concept-data-extraction.md#composite-entity-data)|  
| **Hierarchiczna** <br/>[Rozpoznane maszyny](#machine-learned) |✔ | **Definicja**<br>Hierarchiczna jednostka jest kategorią kontekstowej zapamiętanych jednostek.<br><br>**Przykład**<br>Podane hierarchiczna jednostki `Location` z elementami podrzędnymi `ToLocation` i `FromLocation`, poszczególne elementy podrzędne można ustalić na podstawie **kontekstu** w utterance. W utterance `Book 2 tickets from Seattle to New York`, `ToLocation` i `FromLocation` różnią się kontekstowej na podstawie słowa wokół nich. <br/><br/>**Nie należy używać, jeśli**<br>Jeśli szukasz jednostki, która ma dokładny tekst dopasowań dla elementów podrzędnych, niezależnie od kontekstu, należy użyć jednostki listy. Jeśli szukasz relacji nadrzędny podrzędny z innych typów jednostek, należy użyć złożonego jednostki.<br/><br/>[Szybki start](luis-quickstart-intent-and-hier-entity.md)<br>[Przykład odpowiedzi dla jednostki](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
**Wbudowane** jednostki są udostępniane przez LUIS obiektów niestandardowych. Niektóre z tych obiektów, są zdefiniowane w open source [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text) projektu. Istnieje wiele [przykłady](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs) w katalogu /Specs obsługiwanych kultur. Jeśli z określoną kulturę lub jednostki nie jest obecnie obsługiwany, przyczyniają się do projektu. 

<a name="machine-learned"></a>
**Rozpoznane maszyny** jednostek najlepiej podczas testowania za pomocą [zapytań punktu końcowego](luis-concept-test.md#endpoint-testing) i [recenzowania zniesławiających punktu końcowego](label-suggested-utterances.md). 

<a name="regex"></a>
**Wyrażenie regularne jednostek** są definiowane przez wyrażenie regularne użytkownik podaje jako część definicji jednostek. 

<a name="exact-match"></a>
**Dopasowanie dokładna** jednostki używają tekstu w obiekcie aby dopasować dokładnie tekst.

<a name="mixed"></a>
**Mieszane** jednostek jest użycie kombinacji metod wykrywania jednostki.

## <a name="entity-limits"></a>Limity jednostki
Przegląd [limity](luis-boundaries.md#model-boundaries) zrozumieć, jak wiele każdego typu podmiotu można dodać do modelu.

## <a name="entity-roles"></a>Role jednostki
Jednostka [ról](luis-concept-roles.md) są używane we wzorcach tylko. 

## <a name="composite-vs-hierarchical-entities"></a>Hierarchiczna jednostek złożonego vs
Złożone jednostki i hierarchicznych jednostek zarówno mają relacji nadrzędny podrzędny i są rozpoznawane maszyny. Uczenie maszynowe umożliwia LUIS do zrozumienia jednostek, opartych na różnych kontekstach (rozmieszczenie wyrazów). Złożone jednostki są bardziej elastyczne, ponieważ umożliwiają one typy różnych jednostek jako elementy podrzędne. Hierarchiczna jednostki elementy podrzędne są tylko proste jednostki. 

|Typ|Przeznaczenie|Przykład|
|--|--|--|
|Hierarchiczna|Nadrzędny podrzędny, prosty jednostek|Location.Origin=New Jorku<br>Location.Destination=London|
|Złożone|Jednostek nadrzędny podrzędny: wbudowane, listy, prostego, hierarchicznych| liczba = 3<br>Lista = pierwszej klasy<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>Dopasowywanie wiele jednostek danych
Jeśli słowo lub frazę odpowiada więcej niż jednej jednostki, zapytanie punktu końcowego zwraca każdej jednostki. Jeśli dodać wbudowane numer jednostki i prebuild datetimeV2, a ma utterance `create meeting on 2018/03/12 for lunch with wayne`, LUIS rozpoznaje wszystkie jednostki i zwraca tablicę jednostek jako część odpowiedzi JSON punktu końcowego: 

```JSON
{
  "query": "create meeting on 2018/03/12 for lunch with wayne",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.9333419
  },
  "entities": [
    {
      "entity": "2018/03/12",
      "type": "builtin.datetimeV2.date",
      "startIndex": 18,
      "endIndex": 27,
      "resolution": {
        "values": [
          {
            "timex": "2018-03-12",
            "type": "date",
            "value": "2018-03-12"
          }
        ]
      }
    },
    {
      "entity": "2018",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 21,
      "resolution": {
        "value": "2018"
      }
    },
    {
      "entity": "03/12",
      "type": "builtin.number",
      "startIndex": 23,
      "endIndex": 27,
      "resolution": {
        "value": "0.25"
      }
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Dane pasujące wiele jednostek listy
Jeśli słowo lub frazę odpowiada więcej niż jednej jednostki listy, zapytanie punktu końcowego zwraca każdy obiekt listy.

Dla zapytania `when is the best time to go to red rock?`, a aplikacja ma słowo `red` w więcej niż jedną listę LUIS rozpoznaje wszystkie jednostki i zwraca tablicę jednostek jako część odpowiedzi JSON punktu końcowego: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Jeśli potrzebujesz więcej niż maksymalna liczba jednostek 

Być może należy użyć hierarchiczne i złożone jednostek. Hierarchiczna jednostek odzwierciedlają relacji między obiektami, które cechy lub należą do kategorii. Obiektów podrzędnych są wszystkie elementy członkowskie kategoria ich nadrzędnego. Na przykład podmiot hierarchiczną o nazwie PlaneTicketClass może być EconomyClass i FirstClass obiektów podrzędnych. Hierarchia obejmuje tylko jeden poziom głębokości.  

Złożone jednostek reprezentuje część całości. Na przykład obiekt złożony o nazwie PlaneTicketOrder może być jednostek podrzędnych linii lotniczych, docelowy DepartureCity, DepartureDate i PlaneTicketClass. Tworzysz złożonych jednostki z istniejącym jednostek proste elementy podrzędne hierarchiczna jednostki lub jednostek wbudowane.  

LUIS także listy typu jednostki, który nie jest rozpoznawane do komputera, ale umożliwia aplikacji LUIS określić listę stałych wartości. Zobacz [granice LUIS](luis-boundaries.md) odwołania, aby przejrzeć limity listy typu jednostki. 

Jeśli zostały uznane za hierarchiczna, złożone i wyświetlanie list jednostek i nadal potrzebujesz więcej niż limit się z pomocą techniczną. Aby to zrobić, Zbierz szczegółowe informacje o systemie, przejdź do [LUIS] [ LUIS] witryny sieci Web, a następnie wybierz **Obsługa**. Jeśli subskrypcja platformy Azure zawiera usług pomocy technicznej, skontaktuj się z [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/). 

## <a name="best-practices"></a>Najlepsze praktyki

Utwórz [jednostki](luis-concept-entity-types.md) gdy aplikacja wywołująca lub bot potrzebuje niektóre parametry lub dane z utterance wymagane do wykonania akcji. Jednostka jest słowo lub frazę w utterance, który należy wyodrębnić — prawdopodobnie jako parametru dla funkcji. 

Aby wybrać poprawny typ jednostki do dodawania do aplikacji, musisz wiedzieć, wprowadzania danych przez użytkowników. Znaleziono poszczególnych typów jednostek przy użyciu różnych mechanizmu, takie jak listy uczenia maszynowego, zamknięte lub wyrażenie regularne. Jeśli nie wiesz, rozpoczynać się od podmiotu prostego i etykiety wyraz lub frazę reprezentuje dane w wszystkich zniesławiających, we wszystkich opcji tym brak konwersji.  

Przejrzyj zniesławiających punktu końcowego regularnie odnaleźć wspólnej użycia, gdzie jednostki można zidentyfikowane jako wyrażenie regularne lub Znaleziono dopasowanie dokładne tekstu.  

W ramach przeglądu należy rozważyć dodanie listy frazy, aby dodać sygnał do LUIS wyrazów lub wyrażenia, które są istotne dla domeny, ale nie są dokładne dopasowania i dla którego LUIS nie ma wysokiego zaufania.  

Zobacz [najlepsze rozwiązania](luis-concept-best-practices.md) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się pojęć dotyczących dobrej [zniesławiających](luis-concept-utterance.md). 

Zobacz [Dodaj jednostki](luis-how-to-add-entities.md) Aby dowiedzieć się więcej o sposobie dodawania do aplikacji LUIS jednostek.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website