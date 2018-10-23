---
title: Typy jednostek w aplikacjach usługi LUIS — Language Understanding
titleSuffix: Azure Cognitive Services
description: Dodaj jednostki (kluczowe dane w domenie aplikacji) w aplikacjach Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 92b4864f8991380740e6edb498328ce2eea98250
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650115"
---
# <a name="entities-in-luis"></a>Jednostki w usługi LUIS

Jednostki są wyrazy lub frazy w wypowiedzi, które są kluczowe dane w domenie aplikacji.

## <a name="entity-compared-to-intent"></a>Jednostki w porównaniu do intencji
Jednostka reprezentuje wyraz lub frazę w polu wypowiedź, który ma zostać wyodrębniony. Wypowiedź może zawierać wiele jednostek lub brak wcale. Jednostka reprezentuje klasę, łącznie z kolekcji podobnych obiektów (miejsc, rzeczy, osoby, zdarzenia lub pojęcia). Jednostki opisują informacje istotne dla intencji i czasami są istotne dla aplikacji w celu wykonania swojego zadania. Na przykład aplikacji wyszukiwania wiadomości mogą obejmować jednostki, takie jak "tematu", "źródło", "słowo kluczowe" i "publikowania daty" będące kluczowych danych do wyszukiwania wiadomości. W aplikacji rezerwacji podróży, "Lokalizacja", "Data", "linii lotniczych" "podróży class" i "bilety" są informacje o kluczu dla rezerwacji lotu (dotyczy na intencje "Bookflight").

Natomiast zamiar reprezentuje prognozowania całego wypowiedź. 

## <a name="entities-represent-data"></a>Jednostki reprezentujące dane
Jednostki są dane, które chcesz ściągnąć z wypowiedź. Może to być nazwy, daty, nazwa produktu lub dowolną grupę słów. 

|Wypowiedź|Jednostka|Dane|
|--|--|--|
|Kup 3 bilety w Nowym Jorku|Liczbę wstępnie<br>Location.Destination|3<br>Nowy Jork|
|Kup biletu z nowego Jorku do Londynu na 5 marca|Location.Origin<br>Location.Destination<br>Wstępnie utworzone datetimeV2|Nowy Jork<br>Londyn<br>5 marca 2018 r.|

## <a name="entities-are-optional-but-highly-recommended"></a>Jednostki są opcjonalne, ale zdecydowanie zalecane
Mimo że intencjami wymagane, jednostki są opcjonalne. Nie trzeba tworzyć jednostki dla każdej pojęcie w swojej aplikacji, ale tylko dla tych, które są wymagane dla aplikacji do podejmowania działań. 

Swoje wypowiedzi nie ma szczegółów, czego potrzebuje bota, aby kontynuować, nie muszą je dodać. Jak dojrzewa aplikacji, możesz je dodać później. 

Jeśli nie masz pewności, jak skorzystać z informacji, należy dodać kilka typowych wstępnie utworzone jednostki, takie jak datetimeV2 porządkowe, poczty e-mail i numer telefonu.

## <a name="label-for-word-meaning"></a>Etykieta znaczenie słowa
Jeśli wybór programu word lub rozmieszczeniu word jest taka sama, ale nie oznaczają to samo, nie przypisuj jej etykiety bez jednostki. 

Następujące wypowiedzi, wyraz `fair` jest Homogram. Została wpisana takie same, ale ma inne znaczenie:

|Wypowiedź|
|--|
|Jakiego rodzaju targach hrabstwa występują na terenie Seattle tego lata?|
|Jest bieżąca ocena do przeglądu Seattle uczciwe?|

Jeśli chce się jednostkę zdarzeń, aby znaleźć wszystkie dane zdarzeń, etykieta wyraz `fair` w pierwszym wypowiedź, ale nie w ciągu sekundy.

## <a name="entities-are-shared-across-intents"></a>Jednostki są współdzielone przez intencji
Jednostki są współużytkowane przez intencji. Nie należą one do dowolnego pojedynczego celem. Intencje i podmioty można skojarzyć semantycznie, ale nie jest wyłączne relacji.

W polu wypowiedź "Zarezerwuj mnie biletu do Paryż", "Paryż" jest jednostki typu lokalizacji. Przez rozpoznawanie jednostek, które są wymienione w danych wejściowych użytkownika, usługa LUIS pomaga wybrać konkretne akcje podejmowane w celu spełnienia intencji.

## <a name="assign-entities-in-none-intent"></a>Przypisać jednostek w Brak elementu intent
Wszystkie opcje, w tym **Brak** celem powinny mieć jednostek etykietą. Dzięki temu usługi LUIS, Dowiedz się więcej o których jednostek wypowiedzi i jakie słowa wokół jednostki. 

## <a name="entity-status-for-predictions"></a>Stan jednostki dla prognoz

Zobacz [prognozy stan jednostki](luis-how-to-add-example-utterances.md#entity-status-predictions) Aby uzyskać więcej informacji. 

## <a name="types-of-entities"></a>Typy jednostek
Usługa LUIS oferuje wiele typów jednostek; wstępnie utworzonych jednostek niestandardowych maszyn przedstawiono jednostek i listy jednostek.

| Nazwa | Można oznaczyć | Opis |
| -- |--|--|
| **Wstępnie utworzone** <br/>[Custom](#prebuilt)| |  **Definicja**<br>Wbudowane typy, które reprezentują typowe pojęcia. <br><br>**Lista**<br/>numer kluczowych, numer, temperatury, wymiar, pieniądze, wiek, procent, poczty e-mail, adres URL, numer telefonu i kluczowych. <br><br>Wstępnie utworzone jednostki nazwy są zarezerwowane. <br><br>Wszystkie wstępnie utworzonych jednostek, które są dodawane do aplikacji są zwracane w [punktu końcowego](luis-glossary.md#endpoint) zapytania. Aby uzyskać więcej informacji, zobacz [ze wstępnie utworzonych jednostek](./luis-prebuilt-entities.md). <br/><br/>[Przykładowa odpowiedź dla jednostki](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **Wyrażenie regularne**<br/>[Wyrażenie regularne](#regex)||**Definicja**<br>Niestandardowe wyrażenie regularne dla tekstu sformatowanego wypowiedź raw. On ignoruje wielkość liter i ignoruje wariant kultury.  <br><br>Ta jednostka jest dobrym słów i fraz, spójnie sformatowanych przy użyciu dowolnych wariantów, który również jest zgodny.<br><br>Dopasowywanie wyrażeń regularnych są stosowane po zmianach sprawdzania pisowni. <br><br>Jeśli wyrażenie regularne jest zbyt złożone, np. przy użyciu wielu nawiasie, nie jest możliwe dodać wyrażenie do modelu. <br><br>**Przykład**<br>`kb[0-9]{6,}` Dopasowuje kb123456.<br/><br/>[Szybki start](luis-quickstart-intents-regex-entity.md)<br>[Przykładowa odpowiedź dla jednostki](luis-concept-data-extraction.md)|
| **Proste** <br/>[Przedstawiono maszyny](#machine-learned) | ✔ | **Definicja**<br>Proste jednostka jest jednostce ogólnej, opisujący pojedynczego pojęcia i udostępnionej z kontekstu maszyny do opanowania. Kontekst obejmują wybór programu word, wyraz umieszczania i długość wypowiedź.<br/><br/>Jest to dobry jednostki słów i fraz, które nie są spójnie sformatowanych, ale wskazują ten sam efekt. <br/><br/>[Szybki start](luis-quickstart-primary-and-secondary-data.md)<br/>[Przykładowa odpowiedź dla jednostki](luis-concept-data-extraction.md#simple-entity-data)|  
| **Lista** <br/>[Dokładne dopasowanie](#exact-match)|| **Definicja**<br>Lista jednostek reprezentują zbiór powiązanych słów, wraz z ich synoymns stały, zamknięte w systemie. <br><br>Każda jednostka listy może mieć co najmniej jednego formularza. Najlepiej nadaje się do znanego zestawu zmian dotyczących sposobów reprezentują tego samego pojęcia.<br/><br/>Usługa LUIS nie wykrywa dodatkowe wartości dla jednostek z listy. Użyj **zaleca się** funkcji, aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy.<br/><br>Jeśli istnieje więcej niż jednej jednostki listy z taką samą wartość, każdy obiekt jest zwracany w kwerendy punktu końcowego. <br/><br/>[Szybki start](luis-quickstart-intent-and-list-entity.md)<br>[Przykładowa odpowiedź dla jednostki](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[Mieszane](#mixed) | ✔|**Definicja**<br>Patterns.any jest symbolem zastępczym o zmiennej długości, używana tylko w wypowiedź szablonu wzorca w do oznaczania, gdzie jednostka rozpoczyna się i kończy.  <br><br>**Przykład**<br>Biorąc pod uwagę wypowiedź Wyszukaj książki na podstawie tytułu, pattern.any wyodrębnia pełną tytuł. Jest wypowiedź szablonu, za pomocą pattern.any `Who wrote {BookTitle}[?]`.<br/><br/>[Samouczek](luis-tutorial-pattern.md)<br>[Przykładowa odpowiedź dla jednostki](luis-concept-data-extraction.md#composite-entity-data)|  
| **Złożone** <br/>[Przedstawiono maszyny](#machine-learned) | ✔|**Definicja**<br>Jednostka złożonego składa się z innych jednostek, takich jak wstępnie utworzonych jednostek, proste, aparat wyrażeń regularnych, listy hierarchicznej. Osobne jednostki tworzą całej jednostki. <br><br>**Przykład**<br>Jednostka złożone o nazwie PlaneTicketOrder może mieć wstępnie utworzone jednostki podrzędne `number` i `ToLocation`. <br/><br/>[Samouczek](luis-tutorial-composite-entity.md)<br>[Przykładowa odpowiedź dla jednostki](luis-concept-data-extraction.md#composite-entity-data)|  
| **Hierarchiczna** <br/>[Przedstawiono maszyny](#machine-learned) |✔ | **Definicja**<br>Hierarchiczna jednostki to kategoria kontekstowe nauczony proste jednostek.<br><br>**Przykład**<br>Danego hierarchicznych jednostki `Location` z elementami podrzędnymi `ToLocation` i `FromLocation`, każdego elementu podrzędnego można określić na podstawie **kontekstu** w ramach wypowiedź. W polu wypowiedź `Book 2 tickets from Seattle to New York`, `ToLocation` i `FromLocation` różnią się kontekstowe na podstawie słów wokół nich. <br/><br/>**Nie należy używać, jeśli**<br>Jeśli potrzebujesz jednostki, która ma dopasowania tekstu do dokładnego dopasowania dla dzieci, niezależnie od kontekstu, należy użyć jednostki listy. Jeśli szukasz relacji nadrzędny podrzędny przy użyciu innych typów jednostek, należy użyć złożonego jednostki.<br/><br/>[Szybki start](luis-quickstart-intent-and-hier-entity.md)<br>[Przykładowa odpowiedź dla jednostki](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
**Wstępnie utworzone** jednostki to jednostki niestandardowe, udostępniane przez usługi LUIS. Niektóre z tych jednostek są zdefiniowane w typu open-source [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text) projektu. Istnieje wiele [przykłady](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs) w bieżącym katalogu /Specs dla obsługiwanych kultur. Jeśli z określoną kulturę lub jednostki nie jest obecnie obsługiwane, przyczyniają się do projektu. 

<a name="machine-learned"></a>
**Maszyny do opanowania** jednostek działają najlepiej, jeśli testowany za pośrednictwem [kwerendy punktu końcowego](luis-concept-test.md#endpoint-testing) i [przeglądania punktu końcowego wypowiedzi](luis-how-to-review-endoint-utt.md). 

<a name="regex"></a>
**Wyrażenie regularne jednostek** są definiowane przez wyrażenie regularne, użytkownik udostępnia jako część definicji jednostki. 

<a name="exact-match"></a>
**Dokładne dopasowanie** jednostki używają tekst podany w jednostce, aby dopasować tekst dokładne.

<a name="mixed"></a>
**Mieszane** jednostki używają kombinacji metod wykrywania jednostki.

## <a name="entity-limits"></a>Limity jednostek
Przegląd [limity](luis-boundaries.md#model-boundaries) Aby dowiedzieć się, ile poszczególnych typów obiektu można dodać do modelu.

## <a name="entity-roles"></a>Role jednostki
Jednostka [role](luis-concept-roles.md) dotyczą jednostek niestandardowych i wbudowanych i są używane we wzorcach tylko. 

## <a name="composite-vs-hierarchical-entities"></a>Jednostki hierarchiczne złożonego programu vs
Composite jednostek i hierarchiczne jednostek zarówno mają relacji nadrzędny podrzędny i przedstawiono maszyny. Usługi machine learning umożliwia usługi LUIS do informacje o jednostkach, w oparciu o różne konteksty (rozmieszczenie wyrazów). Złożone jednostki są bardziej elastyczne, ponieważ umożliwiają one typów jednostek innej jako elementy podrzędne. Hierarchiczna jednostki podrzędne są tylko proste jednostki. 

|Typ|Przeznaczenie|Przykład|
|--|--|--|
|Hierarchiczna|Nadrzędny podrzędny, proste jednostek|Location.Origin=New Jorku<br>Location.Destination=London|
|Złożone|Jednostki nadrzędny podrzędny: lista wbudowanych, proste, hierarchiczne| liczba = 3<br>Lista najwyższej klasy =<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>Dopasowywanie wiele jednostek danych
Jeśli wyraz lub frazę, pasuje do więcej niż jednej jednostce, kwerendy punktu końcowego zwraca każdej jednostki. Jeśli dodasz wstępnie utworzone jednostki liczb i datetimeV2 wydarzenia i mieć wypowiedź `create meeting on 2018/03/12 for lunch with wayne`, LUIS rozpoznaje wszystkie jednostki i zwraca tablicę jednostki jako część odpowiedzi JSON punktu końcowego: 

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

## <a name="data-matching-multiple-list-entities"></a>Dane dopasowania wielu jednostek listy
Jeśli wyraz lub frazę, pasuje do więcej niż jednej jednostki listy, kwerendy punktu końcowego zwraca każdy obiekt listy.

Dla zapytania `when is the best time to go to red rock?`, a aplikacja ma słowo `red` w więcej niż jednej listy LUIS rozpoznaje wszystkie jednostki i zwraca tablicę jednostki jako część odpowiedzi JSON punktu końcowego: 

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

Może być konieczne użycie jednostek hierarchiczne i złożone. Hierarchiczna jednostek odzwierciedlają relacji między jednostkami, które mają właściwości lub należą do kategorii. Jednostki podrzędne są wszyscy członkowie ich nadrzędnej kategorii. Na przykład jednostka hierarchicznej, o nazwie PlaneTicketClass może być EconomyClass i FirstClass obiektów podrzędnych. Hierarchia obejmuje tylko jeden poziom głębi.  

Composite jednostek reprezentują części całości. Na przykład jednostka złożone o nazwie PlaneTicketOrder niewykluczone jednostki podrzędne linii lotniczych, miejsce docelowe, DepartureCity, DepartureDate i PlaneTicketClass. Możesz tworzyć złożone jednostki z istniejących jednostek proste dzieci hierarchiczne jednostek lub ze wstępnie utworzonych jednostek.  

Usługa LUIS także listy Typ jednostki obsługiwanej przez nie jest rozpoznana do maszyny, ale pozwala aplikacją usługi LUIS określić stałą listy wartości. Zobacz [granice LUIS](luis-boundaries.md) odwołania, aby zapoznać się ograniczenia typu listy jednostek. 

Jeśli zostały uznane za hierarchiczna, złożony i wyświetlanie list jednostek i nadal potrzebujesz więcej niż limit się z pomocą techniczną. Aby to zrobić, należy zebrać szczegółowe informacje o systemie, przejdź do [LUIS](luis-reference-regions.md#luis-website) witryny sieci Web, a następnie wybierz **pomocy technicznej**. Jeśli Twoja subskrypcja platformy Azure obejmują usługi pomocy technicznej, skontaktuj się z [technicznej platformy Azure](https://azure.microsoft.com/support/options/). 

## <a name="best-practices"></a>Najlepsze rozwiązania

Tworzenie [jednostki](luis-concept-entity-types.md) podczas wywoływania aplikacji lub dowolnemu botowi potrzebuje niektórych parametrów lub dane z wypowiedź wymagane do wykonania akcji. Jednostka jest słowo lub frazę w polu wypowiedź, które należy wyodrębnić — prawdopodobnie jako parametr dla funkcji. 

Aby można było wybrać poprawnego typu jednostki, aby dodać do swojej aplikacji, musisz wiedzieć, sposobu wprowadzania danych przez użytkowników. Każdy typ jednostki znajduje się za pomocą innego mechanizmu, takie jak listy uczenia maszynowego, zamknięte lub wyrażenie regularne. Jeśli wiesz, zaczynać się od prostego jednostki i etykiety wyraz lub frazę, reprezentujący dane w wszystkich wypowiedzi we wszystkich intencji, łącznie z Brak intencji.  

Przejrzyj wypowiedzi punktu końcowego w regularnych odstępach czasu, aby znaleźć wspólne użycie, w którym jednostki mogą być identyfikowane jako wyrażenie regularne lub znaleziono dopasowany tekstu do dokładnego dopasowania.  

W ramach przeglądu należy rozważyć dodanie listy frazę do dodania sygnał do usługi LUIS słów i fraz, które są istotne dla Twojej domeny, ale nie są dokładne dopasowania i dla którego usługa LUIS nie ma o dużej pewności.  

Zobacz [najlepsze praktyki](luis-concept-best-practices.md) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące dobrze [wypowiedzi](luis-concept-utterance.md). 

Zobacz [Dodaj jednostki](luis-how-to-add-entities.md) Aby dowiedzieć się więcej o sposobie dodawania jednostki z aplikacją usługi LUIS.