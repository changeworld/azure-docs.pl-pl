---
title: 'Szybki start: Cyfrowy atrament za pomocą interfejsu API REST rozpoznawania pisma odręcznego rozpoznaje iC#'
description: Użyj interfejsu API rozpoznawania pisma odręcznego, aby rozpocząć, rozpoznawaniu pociągnięć odręcznych cyfrowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: d661d6eca6e4916946944c48cc2e5411aeaf8f14
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060994"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Szybki start: Cyfrowy atrament za pomocą interfejsu API REST rozpoznawania pisma odręcznego rozpoznaje iC#

Użyj tego przewodnika Szybki Start, aby rozpocząć wysyłanie pociągnięć odręcznych cyfrowych do interfejsu API rozpoznawania pisma odręcznego. To C# aplikacja wysyła żądanie interfejsu API, zawierające dane pociągnięcia odręczne w formacie JSON, a następnie pobiera odpowiedź.

Chociaż ta aplikacja jest napisana w języku C#, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Zwykle będzie wywołać interfejs API z aplikacją pisma odręcznego cyfrowych. Ten przewodnik Szybki Start wysyła danych pociągnięcia odręczne na potrzeby poniższego przykładu pisma odręcznego z pliku JSON.

![Obraz przedstawiający tekstu odręcznego](../media/handwriting-sample.jpg)

Kod źródłowy dla tego przewodnika Szybki Start można znaleźć na [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Aby zainstalować pakiet Newtonsoft.Json jako pakiet NuGet w programie Visual studio:
        1. Kliknij prawym przyciskiem myszy **Menedżerze rozwiązania**
        2. Kliknij przycisk **Zarządzaj pakietami NuGet...**
        3. Wyszukaj `Newtonsoft.Json` i zainstaluj pakiet
- Jeśli używasz systemu Linux/MacOS ta aplikacja może być została uruchomiona przy użyciu [Mono](https://www.mono-project.com/).

- Pociągnięcia odręczne przykładowych danych w tym przewodniku Szybki znajduje się na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W programie Visual Studio Utwórz nowe rozwiązanie konsoli i dodaj następujące pakiety. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Utwórz zmienne dla swój klucz subskrypcji i punktu końcowego usługi. Poniżej znajduje się identyfikator URI, można użyć do rozpoznawania pisma odręcznego. Zostanie dołączony do punktu końcowego usługi później, aby utworzyć interfejs API adres URl żądania.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Tworzenie funkcji na potrzeby wysyłania żądań

1. Tworzenie nowej funkcji asynchronicznej o nazwie `Request` przyjmującej zmienne utworzone powyżej.

2. Protokół zabezpieczeń klienta i informacje nagłówka przy użyciu `HttpClient` obiektu. Pamiętaj dodać klucz subskrypcji, aby `Ocp-Apim-Subscription-Key` nagłówka. Następnie utwórz `StringContent` obiekt dla żądania.
 
3. Wyślij żądanie za pomocą `PutAsync()`. Jeśli żądanie zakończy się pomyślnie, zwraca odpowiedź.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Wyślij żądanie rozpoznawania pisma odręcznego

1. Utwórz nową funkcję o nazwie `recognizeInk()`. Konstruowania żądania i wysyłać je przez wywołanie metody `Request()` funkcji z punktem końcowym usługi, klucz subskrypcji, adres URL dla interfejsu API i dane obrysu cyfrowy atrament.

2. Deserializacji obiektu JSON i zapisz je w konsoli. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>Załaduj dane cyfrowy atrament

Tworzenie funkcji o nazwie `LoadJson()` można załadować pliku JSON dane pisma odręcznego. Użyj `StreamReader` i `JsonTextReader` utworzyć `JObject` i przywrócić go.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Wysyłanie żądań do interfejsu API

1. W metodzie głównej aplikacji Załaduj dane JSON za pomocą funkcji utworzonego powyżej. 

2. Wywołaj `recognizeInk()` funkcji utworzonego powyżej. Użyj `System.Console.ReadKey()` do nie zamykaj okna konsoli po uruchomieniu aplikacji.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Uruchom aplikację, a następnie zobacz odpowiedź

Uruchom aplikację. Odpowiedź oznaczająca Powodzenie są zwracane w formacie JSON. Możesz również znaleźć odpowiedź w formacie JSON na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odręcznego w cyfrowych aplikacji pisma odręcznego, spójrz na następujące przykładowe aplikacje w usłudze GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
