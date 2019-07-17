---
title: Wstępnie utworzone intencje i jednostki
titleSuffix: Azure Cognitive Services
description: W tym samouczku dodasz wstępnie utworzone intencje i jednostki do aplikacji, aby szybko zyskać funkcje przewidywania intencji i wyodrębniania danych. Nie musisz oznaczać żadnych wypowiedzi za pomocą wstępnie utworzonych jednostek. Jednostka jest wykrywana automatycznie.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2018
ms.author: diberry
ms.openlocfilehash: 189514c51451dedfaab7b07f28d79f674b32f83a
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277543"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Samouczek: identyfikowanie typowych intencji i jednostek

W tym samouczku dodasz wstępnie utworzone intencje i jednostki do aplikacji samouczka Human Resources, aby szybko zyskać funkcje przewidywania intencji i wyodrębniania danych. Nie musisz oznaczać żadnych wypowiedzi za pomocą wbudowanych jednostek, ponieważ jednostka jest wykrywana automatycznie.

Wstępnie utworzone modele (domeny, intencje i jednostki) ułatwiają szybkie tworzenie modelu.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie nowej aplikacji
> * Dodawanie wstępnie utworzonych intencji 
> * Dodawanie wstępnie utworzonych jednostek 
> * Szkolenie 
> * Publikowanie 
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Dodawanie wstępnie utworzonych intencji w celu ułatwienia realizacji typowych intencji użytkownika

Usługa LUIS zapewnia kilka wstępnie utworzonych intencji, których celem jest ułatwienie realizacji typowych intencji użytkownika.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Wybierz opcję **Add prebuilt domain intent** (Dodaj wstępnie skompilowaną intencję domeny). 

1. Wyszukaj `Utilities`. 

    [![Zrzut ekranu przedstawiający okno dialogowe wstępnie utworzonych intencji z wyrazem Utilities (Narzędzia) w polu wyszukiwania](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. Wybierz następujące intencje i wybierz opcję **Done** (Gotowe): 

   * Utilities.Cancel
   * Utilities.Confirm
   * Utilities.Help
   * Utilities.StartOver
   * Utilities.Stop

     Te intencje są przydatne do określenia miejsca w konwersacji, w jakim znajduje się użytkownik, oraz jakie jest ich przeznaczenie. Te opcje obejmują użycie jednostek. Te jednostki są automatycznie dodawane do aplikacji usługi LUIS: numer, numer i Utilities.DirectionalReference. 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Dodawanie wstępnie utworzonych jednostek ułatwiających wyodrębnianie wspólnych typów danych

Usługa LUIS zawiera kilka wstępnie utworzonych jednostek na potrzeby typowych działań związanych z wyodrębnianiem danych. 

1. Wybierz pozycję **Entities** (Jednostki) w menu nawigacji po lewej stronie.

1. Naciśnij przycisk **Add prebuilt entity** (Dodaj wstępnie utworzoną jednostkę).

1. Wybierz następujące pozycje z listy wstępnie utworzonych jednostek, a następnie wybierz pozycję **Done** (Gotowe).

   * **[PersonName](luis-reference-prebuilt-person.md)** 
   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     ![Zrzut ekranu przedstawiający pozycję number (liczba) wybraną w oknie dialogowym wstępnie skompilowanych jednostek](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

     Te jednostki ułatwią dodawanie funkcji rozpoznawania nazwy i miejsca do aplikacji klienckiej.

## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trenowanie aplikacji w celu umożliwienia testowania zmian w intencji 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikowanie aplikacji w celu umożliwienia wysyłania zapytań z punktu końcowego do trenowanego modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Przejdź na koniec adresu URL na pasku adresu przeglądarki i wprowadź ciąg `I want to cancel my trip to Seattle to see Bob Smith`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    Wynik przewidział intencję Utilities.Cancel z 80% ufnością i wyodrębnił dane o mieście i nazwie osoby. 


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

Dowiedz się więcej o wstępnie utworzonych modelach:

* [Wstępnie utworzone domeny](luis-reference-prebuilt-domains.md): są to wspólne domeny, które zmniejszają ogólny nakład pracy związany z tworzeniem aplikacji usługi LUIS
* Wstępnie utworzone intencje: są to poszczególne intencje wspólnych domen. Zamiast dodawać całą domenę, możesz dodawać pojedyncze intencje.
* [Wstępnie utworzone jednostki](luis-prebuilt-entities.md): są to wspólne typy danych używane w większości aplikacji usługi LUIS.

Dowiedz się więcej o pracy z aplikacją usługi LUIS:

* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Kolejne kroki

Dodając wstępnie utworzone intencje i jednostki, aplikacja kliencka może określić typowe intencje użytkowników i wyodrębnić wspólne typy danych.  

> [!div class="nextstepaction"]
> [Dodawanie jednostki wyrażenia regularnego do aplikacji](luis-quickstart-intents-regex-entity.md)

