---
title: 'Szybki start: uzyskaj szczegółowe informacje o obrazach za pomocą interfejsu API REST i oprogramowania Java — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje na jego temat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: fe323fc27062ad1bee9abdfaf3408430e28523a9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446629"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Szybki start: uchodź szczegółowe informacje o obrazach za pomocą interfejsu API REST wyszukiwania wizualnego Bing i oprogramowania Java

Ten szybki start umożliwia nawiąkczenie pierwszego połączenia z interfejsem API wyszukiwania wizualnego Bing i wyświetl wyniki. Ta aplikacja Java przesyła obraz do interfejsu API i wyświetla informacje, które zwraca. Chociaż ta aplikacja jest napisana w języku Java, API jest restful usługi sieci web zgodne z większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw java development kit (JDK) 7 lub 8](https://aka.ms/azure-jdks)
* [Biblioteka Java Gsona](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowy projekt Java w ulubionym ide lub edytorze i zaimportuj następujące biblioteki:

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

2. Utwórz zmienne dla punktu końcowego interfejsu API, klucz subskrypcji oraz ścieżkę obrazu. `endpoint`może to być globalny punkt końcowy poniżej lub niestandardowy punkt końcowy [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlany w witrynie Azure portal dla zasobu:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

    
    Podczas przekazywania obrazu lokalnego dane formularza `Content-Disposition` muszą zawierać nagłówek. Należy ustawić `name` jego parametr na "image" `filename` i można ustawić parametr na dowolny ciąg. Zawartość formularza zawiera dane binarne obrazu. Maksymalny rozmiar obrazu, który można przesłać, to 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

## <a name="create-the-json-parser"></a>Tworzenie analizatora składni JSON

Utwórz metodę, aby odpowiedź JSON z interfejsu `JsonParser`API była bardziej czytelna za pomocą:

```java
public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
```

## <a name="construct-the-search-request-and-query"></a>Konstruowanie zapytania i żądania wyszukiwania

1. W głównej metodzie aplikacji utwórz klienta `HttpClientBuilder.create().build();`HTTP przy użyciu:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Utwórz `HttpEntity` obiekt, aby przekazać obraz do interfejsu API:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Utwórz `httpPost` obiekt z punktem końcowym i ustaw nagłówek tak, aby używał klucza subskrypcji:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Odbieranie i przetwarzanie odpowiedzi w formacie JSON

1. Użyj `HttpClient.execute()` metody, aby wysłać żądanie do interfejsu API `InputStream` i przechowywać odpowiedź w obiekcie:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Zapisz ciąg JSON i wydrukuj odpowiedź:

    ```java
    String json = new Scanner(stream).useDelimiter("\\A").next();
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(json));
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji sieci Web wyszukiwania wizualnego](../tutorial-bing-visual-search-single-page-app.md)
