---
title: Prosta jednostka i lista fraz
titleSuffix: Azure Cognitive Services
description: W tym samouczku wyodrębnisz z wypowiedzi dane nauczone maszynowo dotyczące nazwy stanowiska pracownika, używając prostej jednostki. Aby zwiększyć dokładność wyodrębniania, dodaj listę fraz zawierającą terminy specyficzne dla danej prostej jednostki.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: ea9a2df1f06ba6836ef88bc57dc3f95fd31e1ee9
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526572"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Samouczek: wyodrębnianie nazw przy użyciu prostej jednostki i listy fraz

W tym samouczku wyodrębnisz z wypowiedzi dane nauczone maszynowo dotyczące nazwy stanowiska pracownika, używając **prostej** jednostki. Aby zwiększyć dokładność wyodrębniania, dodaj listę fraz zawierającą terminy specyficzne dla danej prostej jednostki.

Prosta jednostka wykrywa pojedynczą koncepcję danych zawartą w wyrazach lub frazach.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Dodawanie prostej jednostki 
> * Dodawanie listy fraz w celu wzmocnienia sygnału wyrazów
> * Szkolenie 
> * Publikowanie 
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>Prosta jednostka

W tym samouczku dodasz nową prostą jednostkę w celu wyodrębnienia nazwy stanowiska. Celem prostej jednostki w tej aplikacji LUIS jest nauczenie usługi LUIS, co to jest nazwa stanowiska i gdzie można ją znaleźć w wypowiedzi. Część wypowiedzi, która jest nazwą stanowiska, może się zmieniać dla różnych wypowiedzi i jest zależna od doboru wyrazów i długości wypowiedzi. Usługa LUIS potrzebuje przykładów nazw stanowisk dla wszystkich intencji używających nazw stanowisk.  

Prosta jednostka jest odpowiednia dla tego typu danych, gdy:

* Dane są pojedynczą koncepcją.
* Dane nie mają określonego formatu, tak jak w przypadku wyrażenia regularnego.
* Dane nie są typowe, tak jak w przypadku wstępnie skompilowanej jednostki dotyczącej numeru telefonu lub danych.
* Dane nie są dokładnie dopasowane do listy znanych wyrazów, takiej jak jednostka listy.
* Dane nie zawierają innych elementów danych, takich jak złożone jednostki lub ról kontekstowych.

Przeanalizujmy następujące wypowiedzi z czatbota:

|Wypowiedź|Nazwa stanowiska możliwa do wyodrębnienia|
|:--|:--|
|Chcę, aby poprosić o nowe zadanie ewidencjonowania aktywności.|księgowość|
|Prześlij życiorysem dla inżynierów pozycji.|inżynierów|
|Wypełnij aplikacji dla zadania 123456|123456|

Nazwa stanowiska jest trudna do określenia, ponieważ może być rzeczownikiem, czasownikiem lub frazą składającą się z kilku wyrazów. Na przykład:

|Stanowiska|
|--|
|inżynier ds|
|inżynier ds oprogramowania|
|starszy programista|
|zespół inżynieryjny |
|kontrolera ruchu lotniczego|
|operator pojazdu|
|Sterownik ambulance|
|metody płatności|
|extruder|
|millwright|

Ta aplikacja LUIS ma nazwy stanowisk w kilku intencjach. Dzięki oznaczeniu tych wyrazów w wypowiedziach dla wszystkich intencji usługa LUIS uczy się, jaka jest nazwa stanowiska i gdzie znajduje się ona w wypowiedziach.

Po oznaczeniu jednostek w przykładowych wypowiedziach ważne jest dodanie listy fraz w celu wzmocnienia sygnału prostej jednostki. Lista fraz **nie** jest używana jako dokładne dopasowanie i nie musi uwzględniać każdej możliwej wartości, jakiej oczekujesz. 

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) z samouczka dotyczącego intencji.

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `simple`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Oznaczanie jednostek w przykładowych wypowiedziach intencji

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Na stronie **Intents** (Intencje) wybierz intencję **ApplyForJob**. 

1. W wypowiedzi `I want to apply for the new accounting job` wybierz pozycję `accounting`, wprowadź `Job` w górnym polu menu podręcznego, a następnie w menu podręcznym wybierz polecenie **Create new entity** (Utwórz nową jednostkę). 

    [![Zrzut ekranu przedstawiający usługę LUIS z intencją „ApplyForJob” z wyróżnionymi krokami tworzenia intencji](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Zrzut ekranu przedstawiający usługę LUIS z intencją „ApplyForJob” z wyróżnionymi krokami tworzenia intencji")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. W oknie podręcznym sprawdź nazwę i typ jednostki, a następnie wybierz pozycję **Done** (Gotowe).

    ![Modalne okno dialogowe tworzenia prostej jednostki o nazwie Job (Stanowisko) i typie Simple (Prosta)](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. W pozostałych wypowiedziach oznacz wyrazy powiązane ze stanowiskiem za pomocą jednostki **Job** (Stanowisko), wybierając wyraz lub frazę, a następnie wybierając z menu podręcznego pozycję **Job** (Stanowisko). 

    [![Zrzut ekranu przedstawiający wyróżnioną jednostkę zadania usługi LUIS](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Zrzut ekranu przedstawiający wyróżnioną jednostkę zadania usługi LUIS")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>Dodawanie większej liczby przykładowych wypowiedzi i oznaczanie jednostki

Proste jednostki wymagają wielu przykładów do zapewnienia wysokiego poziomu pewności przewidywania. 
 
1. Dodaj więcej wypowiedzi i oznacz wyrazy lub frazy dotyczące stanowiska jako jednostkę **Job** (Stanowisko). 

    |Wypowiedź|Jednostka Job (Stanowisko)|
    |:--|:--|
    |Jestem I stosowania dla działu Kierownik ds. programów w programie R & D|Kierownik programów|
    |Oto Moja aplikacja Cooka wiersza.|Cooka wiersza|
    |Życiorysem dla kamping counselor jest dołączony.|kamping counselor|
    |To jest Moja doświadczeń Aby uzyskać Asystenta administracyjnego.|Asystent|
    |Czy chcesz zastosować dla zadania zarządzania w sprzedaży.|Zarządzanie, sprzedaży|
    |Jest to życiorysem dla nowej pozycji ewidencjonowania aktywności.|Księgowość|
    |Moja aplikacja dla barback jest dołączony.|barback|
    |Składam Moja aplikacja roofer i framer.|roofer, framer|
    |Moje doświadczeń Service bus sterownik jest tutaj.|Sterownik magistrali|
    |Jestem pielęgniarki zarejestrowana. Oto życiorysem.|pielęgniarki zarejestrowane|
    |Chcę przesłać Moje dokumentacji dla pozycji nauczania, który wystąpił w dokumencie.|nauczania|
    |To jest Moja doświadczeń wpis stocker owoców i warzyw.|stocker|
    |Mają zastosowanie do pracy kafelka.|kafelek|
    |Dołączone wznowienie dla architektury pozioma.|Architekt pozioma|
    |Moje życiorys dla Profesor biologii jest ujęte.|Profesor związane z biologią|
    |Chcę otrzymywać do zastosowania dla pozycji w fotografii.|zdjęć|

## <a name="mark-job-entity-in-other-intents"></a>Oznaczanie jednostki stanowiska w innych intencjach

1. Wybierz pozycję **Intents** (Intencje) z menu po lewej.

1. Wybierz pozycję **GetJobInformation** z listy intencji. 

1. Oznacz etykietami stanowiska w przykładowych wypowiedziach

    Jeśli w jednej intencji znajduje się więcej przykładowych wypowiedzi niż w innej, dla tej intencji występuje wyższe prawdopodobieństwo, że będzie najczęściej przewidywana w tekście. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trenowanie aplikacji w celu umożliwienia testowania zmian w intencji 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikowanie aplikacji w celu umożliwienia wysyłania zapytań z punktu końcowego do trenowanego modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Here is my c.v. for the engineering job`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić wypowiedzi `ApplyForJob`.

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    Usługa LUIS znalazła poprawną intencję **ApplyForJob** i wyodrębniła prawidłową jednostkę **Job** (Stanowisko) z wartością `engineering`.


## <a name="names-are-tricky"></a>Nazwy są trudne
Aplikacja LUIS znalazła prawidłową intencję z wysokim poziomem pewności i wyodrębniła nazwę stanowiska, ale nazwy mogą być skomplikowane. Spróbuj użyć wypowiedzi `This is the lead welder paperwork`.  

W poniższym kodzie JSON usługa LUIS odpowiada przy użyciu prawidłowej intencji `ApplyForJob`, ale nie wyodrębniła nazwy stanowiska `lead welder`. 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

Ponieważ nazwa może być dowolna, usługa LUIS przewiduje jednostki dokładniej, jeśli ma listę fraz powiązanych z wyrazami, które umożliwiają wzmocnienie sygnału.

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>Dodawanie listy fraz wyrazów związanych ze stanowiskami w celu wzmocnienia sygnału wyrazów związanych ze stanowiskami

Otwórz plik [jobs-phrase-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) z repozytorium GitHub Azure-Samples. Lista zawiera ponad 1000 wyrazów i fraz związanych ze stanowiskami. Wyszukaj na liście istotne w Twoim przypadku wyrazy związane ze stanowiskami. Jeśli odpowiednich wyrazów lub fraz nie ma na liście, dodaj własne.

1. W sekcji **Build** (Kompilacja) aplikacji LUIS wybierz pozycję **Phrase lists** (Listy fraz) w menu **Improve app performance** (Zwiększ wydajność aplikacji).

1. Wybierz pozycję **Create new phrase list** (Utwórz nową listę fraz). 

1. Nadaj nowej liście fraz nazwę `JobNames` i skopiuj listę z pliku jobs-phrase-list.csv do pola tekstowego **Values** (Wartości). Wybierz klawisz Enter. 

    [![Zrzut ekranu przedstawiający wyskakujące okno dialogowe Tworzenie nowej listy fraz](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Zrzut ekranu przedstawiający wyskakujące okno dialogowe Tworzenie nowej listy fraz")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Jeśli chcesz dodać więcej wyrazów do listy fraz, wybierz opcję **Recommand (Zalecane)**, a następnie przejrzyj nowe elementy w pozycji **Related Values (Powiązane wartości)** i dodaj odpowiednie pozycje. 

    Upewnij się, że pole **These values are interchangeable (Te wartości są wymienne)** jest zaznaczone, ponieważ wszystkie te wartości powinny być traktowane jako synonimy dla zadań. Dowiedz się więcej o [koncepcjach z zakresu listy pojęć](luis-concept-feature.md#how-to-use-phrase-lists) związanych wymiennością i niewymiennością.

1. Wybierz pozycję **Save** (Zapisz), aby aktywować listę fraz.

    [![Zrzut ekranu przedstawiający wyskakujące okno dialogowe Tworzenie nowej listy fraz ze słowami w polu wartości listy fraz](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Zrzut ekranu przedstawiający wyskakujące okno dialogowe Tworzenie nowej listy fraz ze słowami w polu wartości listy fraz")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. Wytrenuj i opublikuj aplikację ponownie, aby użyć listy fraz.

1. Wykonaj ponownie zapytanie w punkcie końcowym dla tej samej wypowiedzi: `This is the lead welder paperwork.`

    Odpowiedź JSON zawiera wyodrębnioną jednostkę:

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Intencje bez jednostek — samouczek](luis-quickstart-intents-only.md)
* [Proste jednostki](luis-concept-entity-types.md) — informacje koncepcyjne
* [Lista fraz](luis-concept-feature.md) — informacje koncepcyjne
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku aplikacja do obsługi kadr używa nauczonej maszynowo prostej jednostki, aby znajdować nazwy stanowisk w wypowiedziach. Nazwami stanowisk mogą być bardzo różne wyrazy i frazy, więc aplikacja potrzebuje listy fraz do wzmocnienia wyrazów z nazwami stanowisk. 

> [!div class="nextstepaction"]
> [Dodawanie wstępnie skompilowanej jednostki KeyPhrase](luis-quickstart-intent-and-key-phrase.md)
