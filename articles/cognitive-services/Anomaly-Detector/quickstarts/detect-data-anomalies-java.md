---
title: 'Szybki start: Wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST wykrywanie anomalii i języka Java | Dokumentacja firmy Microsoft'
description: Wykrywanie nieprawidłowości w serii danych, jako partii albo na strumieniu danych za pomocą interfejsu API wykrywanie anomalii.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 06cb4d32359014f3cbc67ed1f75988c794e6599e
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619519"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Szybki start: Wykrywanie anomalii w danych szeregów czasowych za pomocą interfejsu API REST wykrywanie anomalii i Java

Użyj tego przewodnika Szybki Start, aby rozpocząć korzystanie z dwóch trybów wykrywania API wykrywanie anomalii do wykrycia anomalii w danych szeregów czasowych. Ta aplikacja Java wysyła dwa żądania interfejsu API, zawierająca dane szeregów czasowych w formacie JSON, a następnie pobiera odpowiedzi.

| Żądanie interfejsu API                                        | Dane wyjściowe aplikacji                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Wykrywaj anomalie jako zadania wsadowego                        | Odpowiedź JSON zawierający stan anomalii (i inne dane) dla każdego punktu danych w danych szeregów czasowych i pozycji wszelkie wykryte anomalie. |
| Wykrywanie anomalii stan najnowszego punktu danych | Odpowiedź JSON zawierający stan anomalii (i inne dane) najnowszy punkt danych w danych szeregów czasowych.                                                                                                                                         |

 Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

- [Java&trade; 7 Kit(JDK) rozwoju](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) lub nowszej.

- Importowanie tych bibliotek z repozytorium Maven
    - [JSON w języku Java](https://mvnrepository.com/artifact/org.json/json) pakietu
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) pakietu

- Wskazuje JSON pliku zawierającego dane szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki Start można znaleźć na [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

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

2. Utwórz zmienne dla swój klucz subskrypcji i punktu końcowego usługi. Poniżej przedstawiono identyfikatory URI, można użyć do wykrywania anomalii. Te będą dołączane do punktu końcowego usługi później, aby utworzyć interfejs API adresów URL żądania.

    |Metoda wykrywania  |URI  |
    |---------|---------|
    |Wykrywanie usługi Batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Wykrywanie na najnowszy punkt danych     | `/anomalydetector/v1.0/timeseries/last/detect`        |

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

3. Przeczytaj w pliku danych JSON

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Tworzenie funkcji na potrzeby wysyłania żądań

1. Utwórz nową funkcję o nazwie `sendRequest()` przyjmującej zmienne utworzone powyżej. Następnie wykonaj poniższe czynności.

2. Utwórz `CloseableHttpClient` obiektu, który może wysyłać żądań do interfejsu API. Wyślij żądanie `HttpPost` obiekt żądania przez połączenie punktu końcowego i adres URL wykrywanie anomalii.

3. Użyj żądania `setHeader()` funkcję, aby ustawić `Content-Type` nagłówka do `application/json`i Dodaj swój klucz subskrypcji, aby `Ocp-Apim-Subscription-Key` nagłówka.

4. Użyj żądania `setEntity()` funkcję, aby dane, które mają być wysyłane.   

5. Korzystanie z klienta `execute()` funkcji do wysłania żądania i zapisać go w celu `CloseableHttpResponse` obiektu. 

6. Utwórz `HttpEntity` obiektu do zapisania zawartości odpowiedzi. Pobierz zawartość z `getEntity()`. Jeśli odpowiedź nie jest pusty, zwraca go.

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

## <a name="detect-anomalies-as-a-batch"></a>Wykrywaj anomalie jako zadania wsadowego

1. Utwórz metodę o nazwie `detectAnomaliesBatch()` wykrycia anomalii w danych jako zadania wsadowego. Wywołaj `sendRequest()` metoda utworzoną za pomocą punktu końcowego, adres url, klucz subskrypcji i danych json. Pobierz wynik i drukować do konsoli.

2. Znajdź pozycje anomalii w zestawie danych. Odpowiedź `isAnomaly` pole zawiera wartość typu boolean odnoszących się do tego, czy punkt danych to anomalii. Pobierz tablicę JSON i iteracji, drukowanie indeksu dowolnego `true` wartości. Te wartości odpowiadają indeks punktów danych nietypowe, jeśli którekolwiek.

    
    ```java
    static void detectAnomaliesBatch(String requestData) {
        System.out.println("Detecting anomalies as a batch");
        String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
        if (result != null) {
            System.out.println(result);
            JSONObject jsonObj = new JSONObject(result);
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
    ```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie anomalii stan najnowszego punktu danych

* Utwórz metodę o nazwie `detectAnomaliesLatest()` do wykrywania anomalii stan ostatniego punktu danych w zestawie danych. Wywołaj `sendRequest()` metoda utworzoną za pomocą punktu końcowego, adres url, klucz subskrypcji i danych json. Pobierz wynik i drukować do konsoli.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Ładowanie danych szeregów czasowych i wysłać żądanie

1. W metodzie głównej aplikacji należy przeczytać plik JSON zawierający dane, które zostaną dodane do żądania.

2. Wywołanie funkcji wykrywania anomalii dwóch utworzonego powyżej.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        detectAnomaliesBatch(requestData);
        detectAnomaliesLatest(requestData);
    }
    ```

### <a name="example-response"></a>Przykładowa odpowiedź

Odpowiedź oznaczająca Powodzenie są zwracane w formacie JSON. Kliknij poniższe łącza, aby wyświetlić odpowiedź w formacie JSON w usłudze GitHub:
* [Przykładowa odpowiedź wykrywania usługi batch](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Przykład najnowszy punkt wykrywania odpowiedzi](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)