---
title: 'Szybki start: Wykrywaj anomalie w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii i środowiska Java'
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
ms.openlocfilehash: 001d53cbd7e2a57615ea3da71d128bd210a79921
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565854"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Szybki start: Wykrywaj anomalie w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii i środowiska Java

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z dwóch trybów wykrywania interfejsu API wykrywania anomalii w celu wykrycia anomalii w danych szeregów czasowych. Ta aplikacja Java wysyła dwa żądania interfejsu API zawierające dane szeregów czasowych w formacie JSON i pobiera odpowiedzi.

| Żądanie interfejsu API                                        | Dane wyjściowe aplikacji                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Wykryj anomalie jako partię                        | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla każdego punktu danych w danych szeregów czasowych oraz pozycje wykryte anomalie. |
| Wykrywanie stanu anomalii najnowszego punktu danych | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla najnowszego punktu danych w danych szeregów czasowych.                                                                                                                                         |

 Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

- [Zestaw Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) lub nowszy.

- Zaimportuj te biblioteki z repozytorium Maven
    - [Kod JSON w pakiecie java](https://mvnrepository.com/artifact/org.json/json)
    - Pakiet [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Plik JSON zawierający punkty danych szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Utwórz zmienne dla klucza subskrypcji i punktu końcowego. Poniżej znajdują się identyfikatory URI, których można użyć do wykrywania anomalii. Zostaną one dołączone do punktu końcowego usługi później w celu utworzenia adresów URL żądań interfejsu API.

    |Metoda wykrywania  |Identyfikator URI  |
    |---------|---------|
    |Wykrywanie partii    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Wykrywanie najnowszego punktu danych     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. Odczytaj w pliku danych JSON

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Utwórz funkcję do wysyłania żądań

1. Utwórz nową funkcję o nazwie `sendRequest()` , która przyjmuje zmienne utworzone powyżej. Następnie wykonaj poniższe czynności.

2. `CloseableHttpClient` Utwórz obiekt, który może wysyłać żądania do interfejsu API. Wyślij żądanie do `HttpPost` obiektu żądania przez połączenie punktu końcowego i adresu URL wykrywania anomalii.

3. Użyj `setHeader()` funkcji żądania `Content-Type` ,aby`application/json`ustawić nagłówek na, i Dodaj swój klucz subskrypcji do nagłówka.`Ocp-Apim-Subscription-Key`

4. Użyj `setEntity()` funkcji żądania do wysyłanych danych.

5. Użyj `execute()` funkcji klienta, aby wysłać żądanie i zapisać ją `CloseableHttpResponse` w obiekcie.

6. `HttpEntity` Utwórz obiekt do przechowywania zawartości odpowiedzi. Pobierz zawartość za pomocą `getEntity()`programu. Jeśli odpowiedź nie jest pusta, zwróć ją.

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        request.setEntity(new StringEntity(requestData));
        try (CloseableHttpResponse response = client.execute(request)) {
            HttpEntity respEntity = response.getEntity();
            if (respEntity != null) {
                return EntityUtils.toString(respEntity, "utf-8");
            }
        } catch (Exception respEx) {
            respEx.printStackTrace();
        }
    } catch (IOException ex) {
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>Wykryj anomalie jako partię

1. Utwórz metodę o nazwie `detectAnomaliesBatch()` , aby wykrywać anomalie w danych jako partię. Wywołaj `sendRequest()` metodę utworzoną powyżej przy użyciu swojego punktu końcowego, adresu URL, klucza subskrypcji i danych JSON. Pobierz wynik i wydrukuj go w konsoli programu.

2. Jeśli odpowiedź zawiera `code` pole, Wydrukuj kod błędu i komunikat o błędzie.

3. W przeciwnym razie Znajdź pozycje anomalii w zestawie danych. `isAnomaly` Pole odpowiedzi zawiera wartość logiczną odnoszącą się do tego, czy dany punkt danych jest anomalią. Pobierz tablicę JSON i wykonaj iterację, drukując indeks dowolnych `true` wartości. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

* Utwórz metodę o nazwie `detectAnomaliesLatest()` w celu wykrycia stanu anomalii ostatniego punktu danych w zestawie danych. Wywołaj `sendRequest()` metodę utworzoną powyżej przy użyciu swojego punktu końcowego, adresu URL, klucza subskrypcji i danych JSON. Pobierz wynik i wydrukuj go w konsoli programu.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Załaduj dane szeregów czasowych i Wyślij żądanie

1. W głównej metodzie aplikacji przeczytaj plik JSON zawierający dane, które zostaną dodane do żądań.

2. Wywołaj dwie utworzone powyżej funkcje wykrywania anomalii.

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>Przykładowa odpowiedź

Pomyślna odpowiedź jest zwracana w formacie JSON. Kliknij poniższe linki, aby wyświetlić odpowiedź JSON w serwisie GitHub:
* [Przykładowa reakcja wykrywania partii](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Przykład ostatniej odpowiedzi wykrywania punktu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
