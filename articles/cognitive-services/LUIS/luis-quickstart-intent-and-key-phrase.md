---
title: 'Samouczek 8: Wyodrębnianie kluczowych fraz w usłudze LUIS'
titleSuffix: Azure Cognitive Services
description: Użyj wstępnie utworzonej jednostki keyPhrase, aby wyodrębnić kluczową treść z wypowiedzi. Nie musisz oznaczać żadnych wypowiedzi za pomocą wstępnie utworzonych jednostek. Jednostka jest wykrywana automatycznie.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 8fa183c22b9b6830c57b0a16b7f5d20ca38e3ef3
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166524"
---
# <a name="tutorial-8-extract-key-phrases-of-utterance"></a>Samouczek 8: Wyodrębnianie kluczowych fraz wypowiedzi
W tym samouczku użyjesz wstępnie utworzonej jednostki keyPhrase w celu wyodrębnienia kluczowych treści z wypowiedzi. Nie musisz oznaczać żadnych wypowiedzi za pomocą wstępnie utworzonych jednostek. Jednostka jest wykrywana automatycznie.

Następujące wypowiedzi przedstawiają przykłady kluczowych fraz:

|Wypowiedź|Wartości jednostki KeyPhrase|
|--|--|
|Czy w następnym roku jest oferowany nowy plan opieki medycznej z mniejszym wkładem własnym?|„mniejszym wkładem własnym”<br>„nowy plan opieki medycznej”<br>„roku”|
|Czy leczenie wzroku jest objęte planem opieki medycznej z wysokim wkładem własnym?|„planem opieki medycznej z wysokim wkładem własnym”<br>„leczenie wzroku”|

Aplikacja kliencka może użyć tych wartości oraz innych wyodrębnionych jednostek w celu podjęcia decyzji dotyczącej kolejnego kroku rozmowy.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Korzystanie z istniejącej aplikacji samouczka
> * Dodawanie jednostki keyPhrase 
> * Szkolenie
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Korzystanie z istniejącej aplikacji

Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Jeśli nie masz aplikacji HumanResources z poprzedniego samouczka, wykonaj następujące kroki:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-simple-HumanResources.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `keyphrase`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

## <a name="add-keyphrase-entity"></a>Dodawanie jednostki keyPhrase 
Dodaj wstępnie utworzoną jednostkę keyPhrase, aby wyodrębnić treść z wypowiedzi.

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Wybierz pozycję **Entities** (Jednostki) w menu po lewej stronie.

3. Wybierz pozycję **Manage prebuilt entities** (Zarządzaj wstępnie utworzonymi jednostkami).

4. W podręcznym oknie dialogowym wybierz pozycję **keyPhrase**, a następnie wybierz pozycję **Done** (Gotowe). 

    [ ![Zrzut ekranu przedstawiający podręczne okno dialogowe z listą jednostek](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

5. Wybierz pozycję **Intents** (Intencje) z menu po lewej stronie, a następnie wybierz intencję **Utilities.Confirm**. Jednostka keyPhrase jest oznaczony etykietą w kilku wypowiedziach. 

    [ ![Zrzut ekranu przedstawiający intencję Utilities.Confirm z jednostkami keyPhrase oznaczonymi etykietą w wypowiedziach](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train"></a>Szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `does form hrf-123456 cover the new dental benefits and medical plan`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 
    
    ```JSON
    {
      "query": "does form hrf-123456 cover the new dental benefits and medical plan",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9300641
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9300641
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0359598845
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0141798034
        },
        {
          "intent": "MoveEmployee",
          "score": 0.0112197418
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00507669244
        },
        {
          "intent": "None",
          "score": 0.00238501839
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00202810857
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00102957746
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0008688423
        },
        {
          "intent": "Utilities.Confirm",
          "score": 3.557994E-05
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",git 
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "new dental benefits",
          "type": "builtin.keyPhrase",
          "startIndex": 31,
          "endIndex": 49
        },
        {
          "entity": "medical plan",
          "type": "builtin.keyPhrase",
          "startIndex": 55,
          "endIndex": 66
        },
        {
          "entity": "hrf",
          "type": "builtin.keyPhrase",
          "startIndex": 10,
          "endIndex": 12
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        }
      ]
    }
    ```

    Podczas wyszukiwania formularza użytkownik podał więcej informacji niż wymagał proces wyszukiwania formularza. Dodatkowe informacje są zwracane jako jednostka **builtin.keyPhrase**. Aplikacja kliencka może używać tych dodatkowych informacji na potrzeby kolejnego pytania, takiego jak „Czy chcesz porozmawiać z przedstawicielem działu zasobów ludzkich o zaletach nowej opieki stomatologicznej” lub udostępnić więcej opcji, w tym „Więcej informacji na temat zalet nowej opieki stomatologicznej lub plany medycznego”.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano wstępnie utworzoną jednostkę keyPhrase, dzięki czemu szybko udostępniono kluczowe frazy w wypowiedziach bez konieczności oznaczania wypowiedzi etykietami. 

> [!div class="nextstepaction"]
> [Dodawanie analizy tonacji do aplikacji](luis-quickstart-intent-and-sentiment-analysis.md)