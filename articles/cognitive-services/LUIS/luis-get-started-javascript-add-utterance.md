---
title: Samouczek dotyczący dodawania wypowiedzi do aplikacji usługi LUIS przy użyciu języka JavaScript | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wywołać aplikację usługi LUIS przy użyciu języka JavaScript.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265463"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>Samouczek: Dodawanie wypowiedzi do aplikacji przy użyciu języka JavaScript
W tym samouczku pokazano, jak napisać program służący do dodawania wypowiedzi do intencji za pomocą interfejsów API tworzenia w języku JavaScript.

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie projektu konsolowego programu Visual Studio 
> * Dodawanie metody służącej do wywoływania interfejsu API usługi LUIS w celu dodawania wypowiedzi i uczenia aplikacji
> * Dodawanie pliku JSON z przykładowymi wypowiedziami do intencji BookFlight
> * Uruchamianie konsoli i wyświetlanie stanu uczenia dla wypowiedzi

Aby uzyskać więcej informacji, zobacz dokumentację techniczną dotyczącą interfejsów API [dodawania wypowiedzi do intencji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [uczenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) i [stanu uczenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS][LUIS] w celu tworzenia aplikacji LUIS.

## <a name="prerequisites"></a>Wymagania wstępne
* Twój [**klucz tworzenia**](luis-concept-keys.md#authoring-key) usługi LUIS. 
* Twój istniejący **identyfikator aplikacji** i **identyfikator wersji** usługi LUIS. 
* Nowy plik o nazwie `add-utterances.html` w programie VSCode.

> [!NOTE] 
> Kompletny plik `add-utterances.html` jest dostępny w [**repozytorium Github** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html).


## <a name="write-the-code"></a>Tworzenie kodu
Utwórz plik `add-utterances.html` i dodaj następujący kod:

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>Wyświetlanie w przeglądarce
1. Otwórz plik w przeglądarce.

2. Dodaj identyfikator tworzenia i identyfikator aplikacji usługi LUIS oraz zmień wersję, jeśli jest ona inna niż `0.1`

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
Po zakończeniu tego samouczka usuń program Visual Studio i aplikację konsolową, jeśli nie będą Ci już potrzebne. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Integrowanie usługi LUIS z botem](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website