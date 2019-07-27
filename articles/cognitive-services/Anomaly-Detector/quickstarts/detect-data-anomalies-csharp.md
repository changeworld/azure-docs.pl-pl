---
title: 'Szybki start: Wykrywaj anomalie w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii iC#'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API wykrywania anomalii w celu wykrycia nieprawidłowości w seriach danych jako partii lub przesyłania strumieniowego danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 97efa5cd91646809178d685ca51e29ef2fda7c0d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564741"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Szybki start: Wykrywaj anomalie w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii iC# 

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z dwóch trybów wykrywania interfejsu API wykrywania anomalii w celu wykrycia anomalii w danych szeregów czasowych. Ta C# aplikacja wysyła dwa żądania interfejsu API zawierające dane szeregów czasowych w formacie JSON i pobiera odpowiedzi.

| Żądanie interfejsu API                                        | Dane wyjściowe aplikacji                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Wykryj anomalie jako partię                        | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla każdego punktu danych w danych szeregów czasowych oraz pozycje wykryte anomalie. |
| Wykrywanie stanu anomalii najnowszego punktu danych | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla najnowszego punktu danych w danych szeregów czasowych.                                                                                                                                         |

 Chociaż ta aplikacja jest napisana w języku C#, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2017 lub nowszego](https://visualstudio.microsoft.com/downloads/),

- Struktura [Json.NET](https://www.newtonsoft.com/json) dostępna jako pakiet NuGet. Aby zainstalować plik Newtonsoft. JSON jako pakiet NuGet w programie Visual Studio:
    
    1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**.
    2. Wybierz pozycję **Zarządzaj pakietami NuGet**.
    3. Wyszukaj plik *Newtonsoft. JSON* i zainstaluj pakiet.

- Jeśli używasz systemu Linux/MacOS, ta aplikacja może być uruchamiana przy użyciu narzędzia [mono](https://www.mono-project.com/).

- Plik JSON zawierający punkty danych szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W programie Visual Studio Utwórz nowe rozwiązanie konsoli i Dodaj następujące pakiety. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Utwórz zmienne dla klucza subskrypcji i punktu końcowego. Poniżej znajdują się identyfikatory URI, których można użyć do wykrywania anomalii. Zostaną one dołączone do punktu końcowego usługi później w celu utworzenia adresów URL żądań interfejsu API.

    |Metoda wykrywania  |Identyfikator URI  |
    |---------|---------|
    |Wykrywanie partii    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Wykrywanie najnowszego punktu danych     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>Utwórz funkcję do wysyłania żądań

1. Utwórz nową funkcję asynchroniczną o `Request` nazwie, która przyjmuje utworzone powyżej zmienne.

2. Ustaw informacje o protokole i nagłówku zabezpieczeń klienta przy `HttpClient` użyciu obiektu. Pamiętaj, aby dodać klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka. Następnie Utwórz `StringContent` obiekt dla żądania.

3. Wyślij żądanie `PostAsync()`, a następnie Zwróć odpowiedź.

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>Wykryj anomalie jako partię

1. Utwórz nową funkcję o nazwie `detectAnomaliesBatch()`. Utwórz żądanie i wyślij je, wywołując `Request()` funkcję z punktem końcowym, kluczem subskrypcji, adresem URL na potrzeby wykrywania anomalii partii oraz danymi szeregów czasowych.

2. Deserializacji obiektu JSON i Zapisz go w konsoli.

3. Jeśli odpowiedź zawiera `code` pole, Wydrukuj kod błędu i komunikat o błędzie. 

4. W przeciwnym razie Znajdź pozycje anomalii w zestawie danych. `isAnomaly` Pole odpowiedzi zawiera tablicę wartości logicznych, z których każdy wskazuje, czy punkt danych jest anomalią. Przekonwertuj ten element na tablicę ciągów z `ToObject<bool[]>()` funkcją obiektu Response. Wykonaj iterację tablicy i wydrukuj indeks wszelkich `true` wartości. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

1. Utwórz nową funkcję o nazwie `detectAnomaliesLatest()`. Utwórz żądanie i wyślij je, wywołując `Request()` funkcję z punktem końcowym, kluczem subskrypcji, adresem URL dla ostatniego wykrywania anomalii w punkcie i danymi szeregów czasowych.

2. Deserializacji obiektu JSON i Zapisz go w konsoli.

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Załaduj dane szeregów czasowych i Wyślij żądanie

1. W głównej metodzie aplikacji Załaduj dane z szeregów czasowych JSON przy `File.ReadAllText()`użyciu programu. 

2. Wywołaj utworzone powyżej funkcje wykrywania anomalii. Użyj `System.Console.ReadKey()` , aby zachować otwarte okno konsoli po uruchomieniu aplikacji.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Przykładowa odpowiedź

Pomyślna odpowiedź jest zwracana w formacie JSON. Kliknij poniższe linki, aby wyświetlić odpowiedź JSON w serwisie GitHub:
* [Przykładowa reakcja wykrywania partii](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Przykład ostatniej odpowiedzi wykrywania punktu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
