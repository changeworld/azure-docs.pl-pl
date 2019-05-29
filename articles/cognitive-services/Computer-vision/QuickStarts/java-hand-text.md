---
title: 'Szybki start: wyodrębnianie tekstu odręcznego — REST, Java'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wyodrębnisz z obrazu tekst odręczny przy użyciu interfejsu API przetwarzania obrazów i języka Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9d057628f79eb5edbbd3424d29aa7683b5d99eb7
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357219"
---
# <a name="quickstart-extract-handwritten-text-using-the-rest-api-and-java-in-computer-vision"></a>Szybki start: wyodrębnianie tekstu odręcznego przy użyciu interfejsu API REST i języka Java w przetwarzaniu obrazów

W tym przewodniku Szybki start wyodrębnisz tekst odręczny z obrazu przy użyciu interfejsu API REST przetwarzania obrazów. Za pomocą [odczytu wsadowego](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) interfejsu API i [wynik operacji odczytu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) interfejsu API, można wykryć tekstu odręcznego na obrazie, a następnie Wyodrębnij rozpoznane znaki do strumienia znaków, można używać maszyny.

> [!IMPORTANT]
> W odróżnieniu od [optyczne rozpoznawanie znaków](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) metody [odczytu wsadowego](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) metody jest uruchamiane asynchronicznie. Ta metoda nie zwraca żadnych informacji w treści pomyślnej odpowiedzi. Zamiast tego metody odczytu wsadowego zwraca identyfikator URI w wartości `Operation-Content` pole nagłówka odpowiedzi. Następnie możesz wywołać ten identyfikator URI, który reprezentuje [wynik operacji odczytu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) metody, aby sprawdzić stan i zwracają wyniki wywołania metody odczytu wsadowego.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć zainstalowaną platformę [Java&trade; i zestaw Standard Edition Development Kit 7 lub 8](https://aka.ms/azure-jdks) (JDK 7 lub 8).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatnej wersji próbnej klucza z [spróbuj usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Lub, postępuj zgodnie z instrukcjami w [Tworzenie konta usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) subskrybować przetwarzania obrazów, i Uzyskaj klucz.

## <a name="create-and-run-the-sample-application"></a>Tworzenie i uruchamianie przykładowej aplikacji

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Utwórz nowy projekt języka Java w ulubionym środowisku IDE lub edytorze. Jeśli ta opcja jest dostępna, utwórz projekt języka Java na podstawie szablonu aplikacji wiersza polecenia.
1. Zaimportuj poniższe biblioteki do projektu języka Java. Jeśli używasz programu Maven, współrzędne programu Maven są udostępniane w każdej bibliotece.
   - [Klient HTTP serwera Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Podstawowa wersja HTTP serwera Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [Biblioteka JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Dodaj poniższe instrukcje `import` do pliku, który zawiera publiczną klasę `Main` dla projektu.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpGet;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.apache.http.Header;
   import org.json.JSONObject;
   ```

1. Zastąp klasę publiczną `Main` poniższym kodem, a następnie w odpowiednich miejscach wprowadź następujące zmiany kodu:
   1. Zastąp wartość `subscriptionKey` kluczem subskrypcji.
   1. Zastąp wartość `uriBase` przy użyciu adresu URL punktu końcowego dla [odczytu wsadowego](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) metody z regionu platformy Azure, gdzie uzyskać klucze subskrypcji, jeśli to konieczne.
   1. Opcjonalnie zastąp wartość `imageToAnalyze` adresem URL innego obrazu, z którego chcesz wyodrębnić tekst odręczny.
1. Zapisz, a następnie skompiluj projekt języka Java.
1. Jeśli używasz środowiska IDE, uruchom klasę `Main`. W przeciwnym razie otwórz okno wiersza polecenia, a następnie użyj polecenia `java`, aby uruchomić skompilowaną klasę. Na przykład `java Main`.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
        "https://westus.api.cognitive.microsoft.com/vision/v2.0/read/core/asyncBatchAnalyze";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
        "Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpTextClient = HttpClientBuilder.create().build();
        CloseableHttpClient httpResultClient = HttpClientBuilder.create().build();;

        try {
            // This operation requires two REST API calls. One to submit the image
            // for processing, the other to retrieve the text found in the image.

            URIBuilder builder = new URIBuilder(uriBase);

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Two REST API methods are required to extract handwritten text.
            // One method to submit the image for processing, the other method
            // to retrieve the text found in the image.

            // Call the first REST API method to detect the text.
            HttpResponse response = httpTextClient.execute(request);

            // Check for success.
            if (response.getStatusLine().getStatusCode() != 202) {
                // Format and display the JSON error message.
                HttpEntity entity = response.getEntity();
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
                return;
            }

            // Store the URI of the second REST API method.
            // This URI is where you can get the results of the first REST API method.
            String operationLocation = null;

            // The 'Operation-Location' response header value contains the URI for
            // the second REST API method.
            Header[] responseHeaders = response.getAllHeaders();
            for (Header header : responseHeaders) {
                if (header.getName().equals("Operation-Location")) {
                    operationLocation = header.getValue();
                    break;
                }
            }

            if (operationLocation == null) {
                System.out.println("\nError retrieving Operation-Location.\nExiting.");
                System.exit(1);
            }

            // If the first REST API method completes successfully, the second
            // REST API method retrieves the text written in the image.
            //
            // Note: The response may not be immediately available. Handwriting
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the handwritten text.
            // You may need to wait or retry this operation.

            System.out.println("\nHandwritten text submitted.\n" +
                    "Waiting 10 seconds to retrieve the recognized text.\n");
            Thread.sleep(10000);

            // Call the second REST API method and get the response.
            HttpGet resultRequest = new HttpGet(operationLocation);
            resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            HttpResponse resultResponse = httpResultClient.execute(resultRequest);
            HttpEntity responseEntity = resultResponse.getEntity();

            if (responseEntity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(responseEntity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Text recognition result response: \n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa aplikacja analizuje i wyświetla pomyślną odpowiedź w oknie konsoli, podobnie jak w poniższym przykładzie:

```json
Handwritten text submitted. Waiting 10 seconds to retrieve the recognized text.

Text recognition result response:

{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 349.59,
      "width": 3200,
      "height": 3200,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [202,618,2047,643,2046,840,200,813],
          "text": "Our greatest glory is not",
          "words": [
            {
              "boundingBox": [204,627,481,628,481,830,204,829],
              "text": "Our"
            },
            {
              "boundingBox": [519,628,1057,630,1057,832,518,830],
              "text": "greatest"
            },
            {
              "boundingBox": [1114,630,1549,631,1548,833,1114,832],
              "text": "glory"
            },
            {
              "boundingBox": [1586,631,1785,632,1784,834,1586,833],
              "text": "is"
            },
            {
              "boundingBox": [1822,632,2115,633,2115,835,1822,834],
              "text": "not"
            }
          ]
        },
        {
          "boundingBox": [420,1273,2954,1250,2958,1488,422,1511],
          "text": "but in rising every time we fall",
          "words": [
            {
              "boundingBox": [423,1269,634,1268,635,1507,424,1508],
              "text": "but"
            },
            {
              "boundingBox": [667,1268,808,1268,809,1506,668,1507],
              "text": "in"
            },
            {
              "boundingBox": [874,1267,1289,1265,1290,1504,875,1506],
              "text": "rising"
            },
            {
              "boundingBox": [1331,1265,1771,1263,1772,1502,1332,1504],
              "text": "every"
            },
            {
              "boundingBox": [1812, 1263, 2178, 1261, 2179, 1500, 1813, 1502],
              "text": "time"
            },
            {
              "boundingBox": [2219, 1261, 2510, 1260, 2511, 1498, 2220, 1500],
              "text": "we"
            },
            {
              "boundingBox": [2551, 1260, 3016, 1258, 3017, 1496, 2552, 1498],
              "text": "fall"
            }
          ]
        },
        {
          "boundingBox": [1612, 903, 2744, 935, 2738, 1139, 1607, 1107],
          "text": "in never failing ,",
          "words": [
            {
              "boundingBox": [1611, 934, 1707, 933, 1708, 1147, 1613, 1147],
              "text": "in"
            },
            {
              "boundingBox": [1753, 933, 2132, 930, 2133, 1144, 1754, 1146],
              "text": "never"
            },
            {
              "boundingBox": [2162, 930, 2673, 927, 2674, 1140, 2164, 1144],
              "text": "failing"
            },
            {
              "boundingBox": [2703, 926, 2788, 926, 2790, 1139, 2705, 1140],
              "text": ",",
              "confidence": "Low"
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy projekt języka Java nie będzie już potrzebny, usuń go wraz ze skompilowaną klasą i zaimportowanymi bibliotekami.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z aplikacją w języku Java Swing, w której zastosowano interfejs API przetwarzania obrazów do optycznego rozpoznawania znaków (OCR), inteligentnego przycinania miniatur oraz wykrywania, kategoryzowania, tagowania i opisywania elementów wizualnych, w tym twarzy, na obrazie. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API Java Tutorial (Samouczek dla języka JavaScript dotyczący interfejsu API przetwarzania obrazów)](../Tutorials/java-tutorial.md)
