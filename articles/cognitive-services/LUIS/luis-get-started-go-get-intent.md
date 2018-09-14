---
title: Analizowanie tekstu w języku naturalnym w usłudze Language Understanding (LUIS) przy użyciu języka GO — Azure Cognitive Services | Microsoft Docs
description: W tym przewodniku Szybki start użyjesz dostępnej publicznie aplikacji LUIS, aby określić intencję użytkownika w tekście konwersacji. Przy użyciu języka GO wyślesz intencję użytkownika jako tekst do punktu końcowego przewidywania HTTP aplikacji publicznej. W punkcie końcowym usługa LUIS zastosuje model aplikacji publicznej, aby przeanalizować tekst w języku naturalnym pod kątem znaczenia, określając ogólną intencję i wyodrębniając dane dotyczące domeny podmiotu aplikacji.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: ca1cba4416f0855a5838e8f3024be87351978ac8
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771744"
---
# <a name="quickstart-call-a-luis-endpoint-using-go"></a>Szybki start: wywoływanie punktu końcowego aplikacji LUIS przy użyciu języka Go

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Język programowania [Go](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analiza tekstu przy użyciu przeglądarki

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-go"></a>Analiza tekstu przy użyciu języka Go

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

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Zamknij plik Go i usuń go z systemu plików. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi](luis-get-started-go-add-utterance.md)