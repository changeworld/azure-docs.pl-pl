---
title: 'Szybki Start: pobieranie zamierzeń przy użyciu przeglądarki LUIS'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start Użyj dostępnej publicznej aplikacji LUIS, aby określić zamiar użytkownika w przeglądarce.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703131"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Szybki Start: pobieranie zamierzeń za pomocą przeglądarki

Aby zrozumieć, co zwraca punkt końcowy przewidywania usługi LUIS, wyświetl wynik przewidywania w przeglądarce internetowej. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wykonywać zapytania dotyczące aplikacji publicznej, potrzebne są:

* Własny klucz Language Understanding (LUIS). Jeśli nie masz jeszcze subskrypcji, aby utworzyć klucz, możesz zarejestrować się w celu uzyskania [bezpłatnego konta](https://azure.microsoft.com/free/).
* Identyfikator aplikacji publicznej: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>Używanie przeglądarki do wyświetlania prognoz

1. Otwórz przeglądarkę internetową. 
1. Użyj pełnych adresów URL poniżej, zastępując `{your-key}` własnym kluczem LUIS. Żądania ODBIERAją żądania i obejmują autoryzację z kluczem LUIS jako parametr ciągu zapytania.

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[Żądanie punktu końcowego przewidywania wersji 2](#tab/V2)
    
    Format adresu URL w wersji 2 dla żądania **Get** Endpoint:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[Żądanie punktu końcowego przewidywania v3](#tab/V3)
    
    
    Format adresu URL v3 dla żądania **Get** Endpoint (według gniazd) to:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. Wklej adres URL w oknie przeglądarki, a następnie naciśnij klawisz Enter. W przeglądarce zostanie wyświetlony wynik w formacie JSON, który wskazuje, że usługa LUIS wykryła intencję `HomeAutomation.TurnOn` jako główną intencję oraz jednostkę `HomeAutomation.Operation` o wartości `on`.

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * *

1. Aby wyświetlić wszystkie intencje, Dodaj odpowiedni parametr ciągu zapytania. 

    #### <a name="v2-prediction-endpointtabv2"></a>[Punkt końcowy przewidywania wersji 2](#tab/V2)

    Dodaj `verbose=true` na końcu ciągu QueryString, aby **pokazać wszystkie intencje**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpointtabv3"></a>[Punkt końcowy przewidywania v3](#tab/V3)

    Dodaj `show-all-intents=true` na końcu ciągu QueryString, aby **pokazać wszystkie intencje**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * * 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Tworzenie aplikacji w portalu LUIS](get-started-portal-build-app.md)