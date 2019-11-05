---
title: Pobierz model z wywołaniem REST wC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: 503482243f5aa2e7f833257a3a6eb91a3b5c5ec1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503745"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Klucz początkowy.
* Zaimportuj aplikację [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z repozytorium GitHub-Services-Language-zrozumieć.
* Identyfikator aplikacji LUIS dla zaimportowanej aplikacji TravelAgent. Identyfikator aplikacji jest wyświetlany na pulpicie nawigacyjnym aplikacji.
* Identyfikator wersji w aplikacji, która odbiera wyrażenia długości. Domyślny identyfikator to „0.1”.
* [.NET Core V 2.2 +](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Programowo Zmień model

Służy C# do dodawania [interfejsu API](https://aka.ms/luis-apim-v3-authoring) jednostki, który jest obiektem do aplikacji. 

1. Utwórz nową aplikację konsolową przeznaczoną C# dla języka z nazwą projektu i folderu `model-with-rest`. 

    ```console
    dotnet new console -lang C# -n model-with-rest
    ```

1. Zainstaluj wymagane zależności z następującymi poleceniami interfejsu wiersza polecenia dotnet.

    ```console
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```
1. Zastąp plik Program.cs przy użyciu następującego kodu:

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;
    
    // 3rd party NuGet packages
    using JsonFormatterPlus;
    
    namespace AddUtterances
    {
        class Program
        {
            // NOTE: use your starter key value
            static string authoringKey = "YOUR-KEY";
    
            // NOTE: Replace this endpoint with your starter key endpoint
            // for example, westus.api.cognitive.microsoft.com
            static string endpoint = "YOUR-ENDPOINT";
    
            // NOTE: Replace this with the ID of your LUIS application
            static string appID = "YOUR-APP-ID";
    
            // NOTE: Replace this your version number
            static string appVersion = "0.1";
    
            static string host = String.Format("https://{0}/luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);
    
            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);
    
                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }
    
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }        
            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";
    
                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";
    
                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }    
            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }    
            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
                [
                    {
                    'text': 'go to Seattle today',
                    'intentName': 'BookFlight',
                    'entityLabels': [
                        {
                        'entityName': 'Location::LocationTo',
                        'startCharIndex': 6,
                        'endCharIndex': 12
                        }
                    ]
                    },
                    {
                        'text': 'a barking dog is annoying',
                        'intentName': 'None',
                        'entityLabels': []
                    }
                ]
                ";            
                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
        }
    }
    ```

1. Zastąp następujące wartości:

    * `YOUR-KEY` z kluczem początkowym
    * `YOUR-ENDPOINT` z punktem końcowym, na przykład `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` z IDENTYFIKATORem aplikacji

1. Skompiluj aplikację konsolową. 

    ```console
    dotnet build
    ```

1. Uruchom aplikację konsolową. Dane wyjściowe konsoli wyświetlają ten sam kod JSON, który został wyświetlony wcześniej w oknie przeglądarki.

    ```console
    dotnet run
    ```

## <a name="luis-keys"></a>Klucze usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Usuń plik z systemu plików. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dla aplikacji](../luis-concept-best-practices.md)