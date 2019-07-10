---
title: 'Szybki start: Rozpoznaje cyfrowy atrament za pomocą interfejsu API REST rozpoznawania pisma odręcznego i Java'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API rozpoznawania pisma odręcznego, aby rozpocząć, rozpoznawaniu pociągnięć odręcznych cyfrowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: b1c739b6355d3b32063e5289720ed1d191611e65
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721231"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Szybki start: Rozpoznaje cyfrowy atrament za pomocą interfejsu API REST rozpoznawania pisma odręcznego i Java

Aby rozpocząć korzystanie z interfejsu API rozpoznawania pisma odręcznego na pociągnięć odręcznych cyfrowych, należy użyć tego przewodnika Szybki Start. Ta aplikacja Java wysyła żądanie interfejsu API, zawierające dane pociągnięcia odręczne w formacie JSON, a następnie pobiera odpowiedź.

Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Zwykle będzie wywołać interfejs API z aplikacją pisma odręcznego cyfrowych. Ten przewodnik Szybki Start wysyła danych pociągnięcia odręczne na potrzeby poniższego przykładu pisma odręcznego z pliku JSON.

![Obraz przedstawiający tekstu odręcznego](../media/handwriting-sample.jpg)

Kod źródłowy dla tego przewodnika Szybki Start można znaleźć na [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Wymagania wstępne

- [Java&trade; 7 Kit(JDK) rozwoju](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) lub nowszej.

- Importowanie tych bibliotek z repozytorium Maven
    - [JSON w języku Java](https://mvnrepository.com/artifact/org.json/json) pakietu
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) pakietu

- Pociągnięcia odręczne przykładowych danych w tym przewodniku Szybki znajduje się na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

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
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Utwórz zmienne dla swój klucz subskrypcji i punktu końcowego usługi. Poniżej znajduje się identyfikator URI, można użyć do rozpoznawania pisma odręcznego. Zostanie on dołączony do punktu końcowego usługi później, aby utworzyć adres URL żądania interfejsu API.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Tworzenie funkcji na potrzeby wysyłania żądań

1. Utwórz nową funkcję o nazwie `sendRequest()` przyjmującej zmienne utworzone powyżej. Następnie wykonaj poniższe czynności.

2. Utwórz `CloseableHttpClient` obiektu, który może wysyłać żądań do interfejsu API. Wyślij żądanie `HttpPut` obiekt żądania przez połączenie punktu końcowego i adres URL żądającego aparatu rozpoznawania pisma odręcznego.

3. Użyj żądania `setHeader()` funkcję, aby ustawić `Content-Type` nagłówka do `application/json`i Dodaj swój klucz subskrypcji, aby `Ocp-Apim-Subscription-Key` nagłówka.

4. Użyj żądania `setEntity()` funkcję, aby dane, które mają być wysyłane.   

5. Korzystanie z klienta `execute()` funkcji do wysłania żądania i zapisać go w celu `CloseableHttpResponse` obiektu. 

6. Utwórz `HttpEntity` obiektu do zapisania zawartości odpowiedzi. Pobierz zawartość z `getEntity()`. Jeśli odpowiedź nie jest pusty, zwraca go.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
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
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Wyślij żądanie rozpoznawania pisma odręcznego

Utwórz metodę o nazwie `recognizeInk()` rozpoznać danych pociągnięcia odręczne. Wywołaj `sendRequest()` metoda utworzoną za pomocą punktu końcowego, adres url, klucz subskrypcji i danych json. Pobierz wynik i drukować do konsoli.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Załaduj dane cyfrowy atrament i wysłać żądanie

1. W metodzie głównej aplikacji należy przeczytać plik JSON zawierający dane, które zostaną dodane do żądania.

2. Wywołaj funkcję rozpoznawania pisma odręcznego utworzonego powyżej.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Uruchom aplikację, a następnie zobacz odpowiedź

Uruchom aplikację. Odpowiedź oznaczająca Powodzenie są zwracane w formacie JSON. Możesz również znaleźć odpowiedź w formacie JSON na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odręcznego w cyfrowych aplikacji pisma odręcznego, spójrz na następujące przykładowe aplikacje w usłudze GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
