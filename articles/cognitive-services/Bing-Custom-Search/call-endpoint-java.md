---
title: 'Szybki start: Wywoływanie punktu końcowego wyszukiwania niestandardowego Bing przy użyciu języka Java | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Użyj tego przewodnika Szybki start, aby rozpocząć żądanie wyników z wystąpienia wyszukiwania niestandardowego Bing w języku Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 9af82ae8cff2e93c0456ed8b5a84414f98ba5b06
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405271"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Szybki start: Wywoływanie punktu końcowego wyszukiwania niestandardowego Bing przy użyciu języka Java

Użyj tego przewodnika Szybki start, aby rozpocząć żądanie wyników z wystąpienia wyszukiwania niestandardowego Bing. Chociaż ta aplikacja jest napisana w języku Java, interfejs API wyszukiwania niestandardowego Bing jest usługą internetową RESTful zgodną z większością języków programowania. Kod źródłowy tego przykładu jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Zobacz [Szybki start: tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing](quick-start.md), aby uzyskać więcej informacji.

- Najnowszy zestaw [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  

- [Biblioteka Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki.

    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Utwórz klasę o nazwie `CustomSrchJava` i utwórz zmienne dla klucza subskrypcji, punktu końcowego wyszukiwania niestandardowego oraz identyfikatora konfiguracji niestandardowej wystąpienia. 
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Utwórz inną klasę o nazwie `SearchResults`, która będzie zawierać odpowiedź z wystąpienia wyszukiwania niestandardowego Bing.

    ```csharp
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. Utwórz funkcję o nazwie `prettify()` w celu formatowania odpowiedzi JSON z interfejsu API wyszukiwania niestandardowego Bing.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Wysyłanie i odbieranie żądania wyszukiwania 

1. Utwórz funkcję o nazwie `SearchWeb()`, która będzie wysyłać żądanie i zwracać obiekt `SearchResults`. Utwórz adres URL żądania, łącząc informacje dotyczące identyfikatora konfiguracji niestandardowej, zapytania i punktu końcowego. Dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. Utwórz strumień i umieść odpowiedź JSON w obiekcie `SearchResults`.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. W metodzie głównej aplikacji wywołaj element `SearchWeb()` przy użyciu terminu wyszukiwania, 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Uruchom program.
    
## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](./tutorials/custom-search-web-page.md)
