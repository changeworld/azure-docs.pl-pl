---
title: 'Samouczek 7: prosta jednostka z listą fraz w usłudze LUIS'
titleSuffix: Azure Cognitive Services
description: Wyodrębnianie danych nauczonych maszynowo z wypowiedzi
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: f3e931344d2d2294c03756d630c688df1e5da9a8
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425261"
---
# <a name="tutorial-7-extract-names-with-simple-entity-and-phrase-list"></a>Samouczek 7: wyodrębnianie nazw przy użyciu prostej jednostki i listy fraz

W tym samouczku wyodrębnisz z wypowiedzi dane nauczone maszynowo dotyczące nazwy stanowiska pracownika, używając **prostej** jednostki. Aby zwiększyć dokładność wyodrębniania, dodaj listę fraz zawierającą terminy specyficzne dla danej prostej jednostki.

W tym samouczku dodasz nową prostą jednostkę w celu wyodrębnienia nazwy stanowiska. Celem prostej jednostki w tej aplikacji LUIS jest nauczenie usługi LUIS, co to jest nazwa stanowiska i gdzie można ją znaleźć w wypowiedzi. Część wypowiedzi, która jest nazwą stanowiska, może się zmieniać dla różnych wypowiedzi i jest zależna od doboru wyrazów i długości wypowiedzi. Usługa LUIS potrzebuje przykładów nazw stanowisk dla wszystkich intencji używających nazw stanowisk.  

Prosta jednostka jest odpowiednia dla tego typu danych, gdy:

* Dane są pojedynczą koncepcją.
* Dane nie mają określonego formatu, tak jak w przypadku wyrażenia regularnego.
* Dane nie są typowe, tak jak w przypadku wstępnie skompilowanej jednostki dotyczącej numeru telefonu lub danych.
* Dane nie są dokładnie dopasowane do listy znanych wyrazów, takiej jak jednostka listy.
* Dane nie zawierają innych elementów danych, takich jak jednostki złożone lub hierarchiczne.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Korzystanie z istniejącej aplikacji samouczka
> * Dodawanie prostej jednostki do wyodrębnienia stanowisk z aplikacji
> * Dodawanie listy fraz w celu wzmocnienia sygnału wyrazów związanych ze stanowiskami
> * Szkolenie 
> * Publikowanie 
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Korzystanie z istniejącej aplikacji

Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Jeśli nie masz aplikacji HumanResources z poprzedniego samouczka, wykonaj następujące kroki:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-composite-HumanResources.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `simple`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

## <a name="simple-entity"></a>Prosta jednostka
Prosta jednostka wykrywa pojedynczą koncepcję danych zawartą w wyrazach lub frazach.

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

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na stronie **Intents** (Intencje) wybierz intencję **ApplyForJob**. 

3. W wypowiedzi `I want to apply for the new accounting job` wybierz pozycję `accounting`, wprowadź `Job` w górnym polu menu podręcznego, a następnie w menu podręcznym wybierz polecenie **Create new entity** (Utwórz nową jednostkę). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Zrzut ekranu usługi LUIS z intencją „ApplyForJob” i wyróżnionymi krokami tworzenia jednostki")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. W oknie podręcznym sprawdź nazwę i typ jednostki, a następnie wybierz pozycję **Done** (Gotowe).

    ![Modalne okno dialogowe tworzenia prostej jednostki o nazwie Job (Stanowisko) i typie Simple (Prosta)](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. W wypowiedzi `Submit resume for engineering position` oznacz etykietą wyraz `engineering` jako jednostkę Job (Stanowisko). Zaznacz wyraz `engineering`, a następnie wybierz pozycję **Job** (Stanowisko) w menu podręcznym. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Zrzut ekranu usługi LUIS z wyróżnioną etykietą jednostki Job (Stanowisko)")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    Wszystkie wypowiedzi są oznaczone etykietami, ale pięć wypowiedzi nie wystarcza, aby nauczyć usługę LUIS o powiązanych ze stanowiskiem wyrazach i frazach. Stanowiska, które używają wartości liczbowej, nie potrzebują większej liczby przykładów, ponieważ używają jednostki wyrażenia regularnego. Stanowiska, które są wyrazami lub frazami, wymagają co najmniej 15 dalszych przykładów. 

6. Dodaj więcej wypowiedzi i oznacz wyrazy lub frazy dotyczące stanowiska jako jednostkę **Job** (Stanowisko). Typy stanowisk są ogólnie używane w ramach zatrudnienia na potrzeby usługi obsługującej zatrudnienie. Jeśli potrzebujesz stanowisk związanych z określoną branżą, wyrazy związane ze stanowiskiem powinny to odzwierciedlać. 

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
    |Chcę otrzymywać do zastosowania dla pozycji w fotografii.|zdjęć|git 

## <a name="label-entity-in-example-utterances"></a>Oznaczanie jednostki etykietami w przykładowych wypowiedziach

Dodanie etykiet (_oznaczenie_) jednostki wskazuje usłudze LUIS, gdzie znajduje się ta jednostka w przykładowych wypowiedziach.

1. Wybierz pozycję **Intents** (Intencje) z menu po lewej.

2. Wybierz pozycję **GetJobInformation** z listy intencji. 

3. Oznacz etykietami stanowiska w przykładowych wypowiedziach:

    |Wypowiedź|Jednostka Job (Stanowisko)|
    |:--|:--|
    |Is there any work in databases?|bazy danych|
    |Looking for a new situation with responsibilities in accounting (Szukam nowego wyzwania w obszarze księgowości)|accounting|
    |What positions are available for senior engineers?|senior engineers|

    Istnieją inne przykładowe wypowiedzi, ale nie zawierają one wyrazów związanych ze stanowiskiem.

## <a name="train"></a>Szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Here is my c.v. for the programmer job`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić wypowiedzi `ApplyForJob`.

    ```JSON
    {
      "query": "Here is my c.v. for the programmer job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9826467
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9826467
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0218927357
        },
        {
          "intent": "MoveEmployee",
          "score": 0.007849265
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00349470088
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00348804821
        },
        {
          "intent": "None",
          "score": 0.00319909188
        },
        {
          "intent": "FindForm",
          "score": 0.00222647213
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00211193133
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00172086991
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00138010911
        }
      ],
      "entities": [
        {
          "entity": "programmer",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 33,
          "score": 0.5230502
        }
      ]
    }
    ```
    
    Usługa LUIS znalazła poprawną intencję **ApplyForJob** i wyodrębniła prawidłową jednostkę **Job** (Stanowisko) z wartością `programmer`.


## <a name="names-are-tricky"></a>Nazwy są trudne
Aplikacja LUIS znalazła prawidłową intencję z wysokim poziomem pewności i wyodrębniła nazwę stanowiska, ale nazwy mogą być skomplikowane. Spróbuj użyć wypowiedzi `This is the lead welder paperwork`.  

W poniższym kodzie JSON usługa LUIS odpowiada przy użyciu prawidłowej intencji `ApplyForJob`, ale nie wyodrębniła nazwy stanowiska `lead welder`. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

Ponieważ nazwa może być dowolna, usługa LUIS przewiduje jednostki dokładniej, jeśli ma listę fraz powiązanych z wyrazami, które umożliwiają wzmocnienie sygnału.

## <a name="to-boost-signal-add-phrase-list"></a>Dodawanie listy fraz w celu wzmocnienia sygnału

Otwórz plik [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) z repozytorium Github LUIS-Samples. Lista zawiera ponad tysiąc wyrazów i fraz związanych ze stanowiskami. Wyszukaj na liście istotne w Twoim przypadku wyrazy związane ze stanowiskami. Jeśli odpowiednich wyrazów lub fraz nie ma na liście, dodaj własne.

1. W sekcji **Build** (Kompilacja) aplikacji LUIS wybierz pozycję **Phrase lists** (Listy fraz) w menu **Improve app performance** (Zwiększ wydajność aplikacji).

2. Wybierz pozycję **Create new phrase list** (Utwórz nową listę fraz). 

3. Nadaj nowej liście fraz nazwę `Job` i skopiuj listę z pliku jobs-phrase-list.csv do pola tekstowego **Values** (Wartości). Wybierz klawisz Enter. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Zrzut ekranu podręcznego okna dialogowego tworzenia nowej listy fraz")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Jeśli chcesz dodać więcej wyrazów do listy fraz, przejrzyj pozycję **Powiązane wartości** i dodaj odpowiednie pozycje. 

4. Wybierz pozycję **Save** (Zapisz), aby aktywować listę fraz.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Zrzut ekranu podręcznego okna dialogowego tworzenia nowej listy fraz z wyrazami w polu wartości listy fraz")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Naucz](#train) i [opublikuj](#publish) aplikację ponownie, aby użyć listy fraz.

6. Wykonaj ponownie zapytanie w punkcie końcowym dla tej samej wypowiedzi: `This is the lead welder paperwork.`

    Odpowiedź JSON zawiera wyodrębnioną jednostkę:

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku aplikacja do obsługi kadr używa nauczonej maszynowo prostej jednostki, aby znajdować nazwy stanowisk w wypowiedziach. Nazwami stanowisk mogą być bardzo różne wyrazy i frazy, więc aplikacja potrzebuje listy fraz do wzmocnienia wyrazów z nazwami stanowisk. 

> [!div class="nextstepaction"]
> [Dodawanie wstępnie skompilowanej jednostki KeyPhrase](luis-quickstart-intent-and-key-phrase.md)
