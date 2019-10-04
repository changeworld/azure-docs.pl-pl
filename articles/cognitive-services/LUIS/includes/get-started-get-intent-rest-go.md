---
title: Pobierz zamierzenia z wywołaniem REST w usłudze go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: b36e967e960ec6a2b39409ea7be5f3f98f69b9c8
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838488"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Język programowania języka [go](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46AF-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Pobierz klucz LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Programowe pobieranie zamiarów

Możesz użyć języka go, aby uzyskać dostęp do tych samych wyników, które zostały wystawione w oknie przeglądarki w poprzednim kroku. 

1. Utwórz nowy plik o nazwie `endpoint.go`. Dodaj następujący kod:
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. Za pomocą wiersza polecenia w tym samym katalogu, w którym został utworzony plik, wprowadź `go build endpoint.go`, aby skompilować plik go. Wiersz polecenia nie zwraca żadnych informacji o pomyślnej kompilacji.

3. Uruchom aplikację przejdź z wiersza polecenia, wprowadzając następujący tekst w wierszu polecenia: 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    Zastąp `<add-your-key>` wartością klucza.  
    
    Odpowiedź z wiersza polecenia to: 
    
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


## <a name="luis-keys"></a>Klucze LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Zamknij projekt programu Visual Studio i Usuń katalog projektu z systemu plików. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wyrażenia długości i uczenie się za pomocą języka go](../luis-get-started-go-add-utterance.md)