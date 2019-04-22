---
title: 'Szybki start: uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i języka Java'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje na jego temat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 2fe4e9dad0b198fe54e06ce07100d231f1f7d157
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046448"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Szybki start: uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i języka Java

Użyj tego przewodnika Szybki Start, aby wprowadzić pierwsze wywołanie interfejsu API wyszukiwania wizualnego Bing i przejrzysz wyniki. Ta aplikacja Java przekazuje obraz do interfejsu API i wyświetla informacje, które zwraca. Chociaż tej aplikacji został napisany w języku Java, interfejs API jest zgodny z większość języków programowania usługi sieci Web typu RESTful.

Podczas przekazywania lokalny obraz musi zawierać dane formularza `Content-Disposition` nagłówka. Należy ustawić jego `name` można ustawić parametru "image", a `filename` parametr dowolny ciąg. Zawartość formularza obejmują dane binarne obrazu. Rozmiar maksymalny obrazu, które można przekazać to 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestawu Java Development Kit (JDK) 7 lub 8](https://aka.ms/azure-jdks)
* [Biblioteka Gson języka Java](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowy projekt języka Java w Twoim ulubionym środowiskiem IDE lub edytora, a następnie zaimportuj następujące biblioteki:

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. Utwórz zmienne dla punktu końcowego interfejsu API, klucz subskrypcji oraz ścieżkę do obrazu:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>Tworzenie analizatora składni JSON

Utwórz metodę, aby lepiej odpowiedź JSON z interfejsu API można odczytać przy użyciu `JsonParser`:

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>Konstruowanie zapytania i żądania wyszukiwania

1. W metodzie głównej aplikacji, należy utworzyć klienta HTTP przy użyciu `HttpClientBuilder.create().build();`:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Utwórz `HttpEntity` obiektu przekażesz obraz do interfejsu API:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Utwórz `httpPost` obiektu z punktem końcowym usługi, a następnie ustaw nagłówek do użycia tego klucza subskrypcji:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Odbieranie i przetwarzanie odpowiedzi w formacie JSON

1. Użyj `HttpClient.execute()` metodę, aby wysłać żądanie do interfejsu API i Przechowaj odpowiedź w `InputStream` obiektu:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Store ciągu JSON, a następnie wydrukować odpowiedzi:

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z jednej strony wyszukiwania wizualnego](../tutorial-bing-visual-search-single-page-app.md)
