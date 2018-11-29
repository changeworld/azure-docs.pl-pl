---
title: 'Samouczek 2: wstępnie utworzone intencje i jednostki — używanie wstępnie utworzonych typowych wypowiedzi — wyodrębnianie typowych danych w usłudze LUIS'
titleSuffix: Azure Cognitive Services
description: Dodaj wstępnie utworzone intencje i jednostki do aplikacji samouczka Human Resources, aby szybko zyskać funkcje przewidywania intencji i wyodrębniania danych. Nie musisz oznaczać żadnych wypowiedzi za pomocą wstępnie utworzonych jednostek. Jednostka jest wykrywana automatycznie.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 3bad68d1a388a5bc8780df633313206afaadcef9
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422426"
---
# <a name="tutorial-2-identify-common-intents-and-entities"></a>Samouczek 2: identyfikowanie typowych intencji i jednostek
W ramach tego samouczka zmodyfikujesz aplikację Human Resources. Dodaj wstępnie utworzone intencje i jednostki do aplikacji samouczka Human Resources, aby szybko zyskać funkcje przewidywania intencji i wyodrębniania danych. Nie musisz oznaczać żadnych wypowiedzi za pomocą wbudowanych jednostek, ponieważ jednostka jest wykrywana automatycznie.

Wbudowane modele typowych domen podmiotów i typów danych ułatwiają szybkie tworzenie modelu i pokazują wygląd modelu na przykładzie. 

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Korzystanie z istniejącej aplikacji samouczka
> * Dodawanie wstępnie utworzonych intencji 
> * Dodawanie wstępnie utworzonych jednostek 
> * Szkolenie 
> * Publikowanie 
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Korzystanie z istniejącej aplikacji
Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Jeśli nie masz aplikacji HumanResources z poprzedniego samouczka, wykonaj następujące kroki:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-intent-only-HumanResources.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `prebuilts`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL. 

## <a name="add-prebuilt-intents"></a>Dodawanie wstępnie utworzonych intencji
Usługa LUIS zapewnia kilka wstępnie utworzonych intencji, których celem jest ułatwienie realizacji typowych intencji użytkownika.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Wybierz pozycję **Add prebuilt intent** (Dodaj wstępnie utworzoną intencję). 

3. Wyszukaj `Utilities`. 

    [ ![Zrzut ekranu przedstawiający okno dialogowe wstępnie utworzonych intencji z wyrazem Utilities (Narzędzia) w polu wyszukiwania](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Wybierz następujące intencje i wybierz opcję **Done** (Gotowe): 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>Dodawanie wstępnie utworzonych jednostek
Usługa LUIS zawiera kilka wstępnie utworzonych jednostek na potrzeby typowych działań związanych z wyodrębnianiem danych. 

1. Wybierz pozycję **Entities** (Jednostki) w menu nawigacji po lewej stronie.

2. Naciśnij przycisk **Manage prebuilt entity** (Zarządzaj wstępnie utworzoną jednostką).

3. Wybierz pozycję **number** (liczba) oraz **datetimeV2** z listy wstępnie skompilowanych jednostek, a następnie wybierz pozycję **Done (Gotowe)**.

    ![Zrzut ekranu przedstawiający pozycję number (liczba) wybraną w oknie dialogowym wstępnie skompilowanych jednostek](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train"></a>Szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec adresu URL na pasku adresu przeglądarki i wprowadź ciąg `I want to cancel on March 3`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

    ```JSON
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    Wynik przewidział intencję Utilities.Cancel i wyodrębnił dane dla 3 marca i liczby 3. 

    Istnieją dwie wartości dla 3 marca, ponieważ wypowiedź nie określała, czy 3 marca jest datą w przeszłości, czy przyszłości. Aplikacja kliencka musi wykonać założenie lub poprosić o wyjaśnienie, jeśli jest ono potrzebne. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

Dodając wstępnie utworzone intencje i jednostki, aplikacja kliencka może określić typowe intencje użytkowników i wyodrębnić wspólne typy danych. 

> [!div class="nextstepaction"]
> [Dodawanie jednostki wyrażenia regularnego do aplikacji](luis-quickstart-intents-regex-entity.md)

