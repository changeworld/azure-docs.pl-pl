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
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: e06bb4c09b3ebab25c0c0ef8ac5c51f6842f34cd
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987958"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Szybki Start: pobieranie zamierzeń za pomocą przeglądarki

Aby zrozumieć, co zwraca punkt końcowy przewidywania usługi LUIS, wyświetl wynik przewidywania w przeglądarce internetowej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wykonywać zapytania dotyczące aplikacji publicznej, potrzebne są:

* Własny Language Understanding (LUIS) do tworzenia i prognozowania, który można uzyskać z [portalu Luis (wersja zapoznawcza)](https://preview.luis.ai/). Jeśli nie masz jeszcze subskrypcji, aby utworzyć klucz, możesz zarejestrować się w celu uzyskania [bezpłatnego konta](https://azure.microsoft.com/free/).
* Identyfikator aplikacji publicznej: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.

## <a name="use-the-browser-to-see-predictions"></a>Używanie przeglądarki do wyświetlania prognoz

1. Otwórz przeglądarkę internetową.
1. Użyj pełnych adresów URL poniżej, zastępując `YOUR-KEY` własnymi procesami tworzenia lub przewidywania. Żądania ODBIERAją żądania i obejmują autoryzację, za pomocą LUIS tworzenia lub klucza przewidywania, jako parametru ciągu zapytania.

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[Żądanie prognozowania v3](#tab/V3-1-1)


    Format adresu URL v3 dla żądania **Get** Endpoint (według gniazd) to:

    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[Żądanie przewidywania w wersji 2](#tab/V2-1-2)

    Format adresu URL w wersji 2 dla żądania **Get** Endpoint:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Wklej adres URL w oknie przeglądarki, a następnie naciśnij klawisz Enter. W przeglądarce zostanie wyświetlony wynik w formacie JSON, który wskazuje, że usługa LUIS wykryła intencję `HomeAutomation.TurnOn` jako główną intencję oraz jednostkę `HomeAutomation.Operation` o wartości `on`.

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[Odpowiedź przewidywania v3](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
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

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[Odpowiedź przewidywania w wersji 2](#tab/V2-2-2)

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

    * * *

1. Aby wyświetlić wszystkie intencje, Dodaj odpowiedni parametr ciągu zapytania.

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[Punkt końcowy przewidywania v3](#tab/V3-3-1)

    Dodaj `show-all-intents=true` na końcu ciągu QueryString, aby **pokazać wszystkie intencje**:

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
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


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Tworzenie aplikacji w portalu LUIS](get-started-portal-build-app.md)
