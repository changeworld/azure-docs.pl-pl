---
title: Pobierz intencje, przejdź do LUIS
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dotyczącym języka Go użyjesz dostępnej publicznie aplikacji LUIS, aby określić intencję użytkownika w tekście konwersacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: c4c2d94544739b0611d19705a669d124295947d8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563837"
---
# <a name="quickstart-get-intent-using-go"></a>Szybki start: pobieranie intencji za pomocą języka Go

W tym przewodniku Szybki start przekażesz wypowiedzi do punktu końcowego aplikacji LUIS i uzyskasz intencje oraz jednostki.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Język programowania [Go](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Pobieranie intencji za pomocą przeglądarki

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Pobieranie intencji w sposób programistyczny

Aby uzyskać dostęp do tych samych wyników, które zostały wyświetlone w oknie przeglądarki w poprzednim kroku, możesz użyć języka Go. 

1. Utwórz nowy plik o nazwie `endpoint.go`. Dodaj następujący kod:
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. W wierszu polecenia w tym samym katalogu, w którym utworzono plik, wprowadź polecenie `go build endpoint.go`, aby skompilować plik Go. Wiersz polecenia nie zwraca żadnej informacji w przypadku pomyślnej kompilacji.

3. Uruchom aplikację języka Go z wiersza polecenia, wprowadzając następujący tekst w wierszu polecenia: 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    Zastąp zmienną `<add-your-key>` wartością klucza.  
    
    Odpowiedź w wierszu polecenia jest następująca: 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
    {
        "query": "turn on the bedroom light",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.809439957
        },
        "entities": [
            {
            "entity": "bedroom",
            "type": "HomeAutomation.Room",
            "startIndex": 12,
            "endIndex": 18,
            "score": 0.8065475
            }
        ]
    }
    ```
    
## <a name="luis-keys"></a>Klucze usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Zamknij plik Go i usuń go z systemu plików. 

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi](luis-get-started-go-add-utterance.md)
