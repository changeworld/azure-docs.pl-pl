---
title: Przewodnik Szybki start dotyczący dodawania wypowiedzi do aplikacji usługi LUIS przy użyciu języka JavaScript — Azure Cognitive Services | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak wywołać aplikację usługi LUIS przy użyciu języka JavaScript.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: 0920a194d3e9c93883b88b7131f7e81dc8fb3302
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159694"
---
# <a name="quickstart-change-model-using-javascript"></a>Szybki start: zmiana modelu przy użyciu języka JavaScript

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Program Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>Tworzenie kodu przewodnika Szybki start

Utwórz plik `add-utterances.html` i dodaj następujący kod:

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>Uruchamianie kodu

1. Otwórz plik w przeglądarce.

2. Dodaj identyfikator tworzenia usługi LUIS, identyfikator Twojej aplikacji LUIS.

3. Zmodyfikuj **tablicę wypowiedzi** do dodania do Twojej aplikacji. Są one przechowywane w zmiennej utteranceJSON. Zmień te wartości na potrzeby używanej domeny i wypowiedzi. 

    ```json
    // example batch utterances
    var utteranceJSON = [
        {
            "text": "go to Seattle",
            "intentName": "BookFlight",
            "entityLabels": [
                {
                    "entityName": "Location::LocationTo",
                    "startCharIndex": 6,
                    "endCharIndex": 12
                }
            ]
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. Wybierz przycisk `Upload utterance`. Wyniki działania usługi LUIS zostaną wyświetlone poniżej przycisków.

5. Wybierz przycisk `Train model`, aby przeprowadzić uczenie aplikacji przy użyciu tych nowych wypowiedzi.

6. Wybierz przycisk `Train Status`, aby wyświetlić stan uczenia. 

    ![Add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po ukończeniu przewodnika Szybki start usuń wszystkie pliki utworzone w tym przewodniku Szybki start. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Integrowanie usługi LUIS z botem](luis-csharp-tutorial-build-bot-framework-sample.md)
