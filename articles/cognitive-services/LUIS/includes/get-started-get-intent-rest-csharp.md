---
title: Pobierz prognozowanie za pomocą wywołania REST wC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 2d3a000040ff1b4f6e0ae548b578e8be014dc06a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414470"
---
## <a name="prerequisites"></a>Wymagania wstępne

* [.NET Core V 2.2 +](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* Identyfikator aplikacji publicznej: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>Pobieranie intencji w sposób programistyczny

Użyj C# (.NET Core), aby wykonać zapytanie dotyczące [punktu końcowego przewidywania](https://aka.ms/luis-apim-v3-prediction) i uzyskać wynik przewidywania.

1. Utwórz nową aplikację konsolową przeznaczoną C# dla języka z nazwą projektu i folderu `predict-with-rest`. 

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Przejdź do katalogu `predict-with-rest`, który został właśnie utworzony, i zainstaluj wymagane zależności przy użyciu następujących poleceń:  

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. Otwórz `Program.cs` w ulubionym środowisku IDE lub edytorze. Następnie zastąp `Program.cs` następującym kodem:
    
   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    
    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: for example, the starter key
                var key = "YOUR-KEY";
                
                // YOUR-ENDPOINT: example is westus2.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"; 
    
                var utterance = "turn on all lights";
    
                MakeRequest(key, endpoint, appId, utterance);
    
                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);
    
                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
    
                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;
    
                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";
    
                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);
    
                var response = await client.GetAsync(endpointUri);
    
                var strResponseContent = await response.Content.ReadAsStringAsync();
                
                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. Zastąp następujące wartości:

    * `YOUR-KEY` z kluczem początkowym.
    * `YOUR-ENDPOINT` z punktem końcowym. Na przykład `westus2.api.cognitive.microsoft.com`.

1. Skompiluj aplikację konsolową za pomocą tego polecenia: 

    ```console
    dotnet build
    ```

1. Uruchom aplikację konsolową. Dane wyjściowe konsoli wyświetlają ten sam kod JSON, który został wyświetlony wcześniej w oknie przeglądarki.

    ```console
    dotnet run
    ```

1. Przejrzyj odpowiedź przewidywania zwracaną jako kod JSON:

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Odpowiedź JSON sformatowana pod kątem czytelności: 

    ```JSON
    {
        "query": "turn on all lights",
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
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="luis-keys"></a>Klucze usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Usuń plik z systemu plików. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wyrażenia długości i uczenie](../get-started-get-model-rest-apis.md)