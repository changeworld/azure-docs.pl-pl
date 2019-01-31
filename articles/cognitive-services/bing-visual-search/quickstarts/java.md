---
title: 'Szybki start: uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i języka Java'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje na jego temat.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 67cd215479efb40e3eb7e81c6c519a59b6526f47
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172038"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Szybki start: uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i języka Java

Ten przewodnik Szybki start umożliwia utworzenie pierwszego wywołania do interfejsu API wyszukiwania wizualnego Bing i wyświetlenie wyników wyszukiwania. Ta prosta aplikacja C# przekazuje obraz do interfejsu API i wyświetla zwrócone informacje na jego temat. Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Podczas przekazywania obrazu lokalnego dane formularza muszą zawierać nagłówek Content-Disposition. Jego parametr `name` musi mieć wartość "image", a parametr `filename` może być ustawiony na dowolny ciąg. Zawartością formularza jest plik binarny obrazu. Maksymalny rozmiar obrazu, który można przekazać, wynosi 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Java Development Kit (JDK) 7 lub 8](https://aka.ms/azure-jdks)
* [Biblioteka Gson](https://github.com/google/gson)
* [Apache HttpComponents](http://hc.apache.org/downloads.cgi)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki.

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

2. Utwórz zmienne dla punktu końcowego interfejsu API, klucz subskrypcji oraz ścieżkę obrazu. 

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>Tworzenie analizatora składni JSON

Utwórz metodę, aby zwiększyć czytelność odpowiedzi JSON z interfejsu API, przy użyciu analizatora `JsonParser`.

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>Konstruowanie zapytania i żądania wyszukiwania

1. W metodzie głównej aplikacji utwórz klienta Http przy użyciu funkcji `HttpClientBuilder.create().build();`.

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Utwórz element `HttpEntity` w celu przekazania obrazu do interfejsu API.

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Utwórz obiekt `httpPost` z Twoim punktem końcowym, a następnie ustaw w nagłówku używanie Twojego klucza subskrypcji.

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Odbieranie i przetwarzanie odpowiedzi w formacie JSON

1. Za pomocą funkcji `HttpClient.execute()` wyślij żądanie do interfejsu API i zapisz odpowiedź w obiekcie `InputStream`.
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Zapisz ciąg JSON, a następnie wydrukuj odpowiedź.

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](../tutorial-bing-visual-search-single-page-app.md)
