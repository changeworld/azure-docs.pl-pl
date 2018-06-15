---
title: Punkt końcowy wywołanie za pomocą usług kognitywnych Java — wyszukiwanie niestandardowe Bing - Microsoft
description: Ta opcja szybkiego startu pokazano, jak żądania wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego za pomocą języka Java do wywołania punktu końcowego niestandardowe wyszukiwania usługi Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 03d622e3c7a3315238f2bceedae529bbe06af299
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349777"
---
# <a name="call-bing-custom-search-endpoint-java"></a>Wywołanie wyszukiwania usługi Bing niestandardowe punktu końcowego (Java)

Ta opcja szybkiego startu pokazano, jak żądania wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego za pomocą języka Java do wywołania punktu końcowego niestandardowe wyszukiwania usługi Bing. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:
- Wystąpienie wyszukiwania niestandardowego. Zobacz [Tworzenie pierwszego wystąpienia wyszukiwania usługi Bing niestandardowe](quick-start.md).

- [Java](https://www.java.com) zainstalowane.

- A [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsy API wyszukiwania usługi Bing**. [Bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) jest wystarczająca dla tego przewodnika Szybki Start. Należy klucz dostępu podany przy wywołaniu metody aktywacji bezpłatną wersję próbną lub może używać klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="run-the-code"></a>Uruchamianie kodu

Aby wywołać punktu końcowego wyszukiwania usługi Bing niestandardowe, wykonaj następujące kroki:

1. Za pomocą programu IDE języka Java wyboru Utwórz pakiet.
2. Utwórz plik CustomSrchJava.java i skopiuj następujący kod do niego.
3. Zastąp **YOUR SUBSKRYPCJI klucza** i **YOUR-niestandardowe-CONFIG-ID** z identyfikatorem klucza i konfiguracji.

    ``` Java
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
    
    public class CustomSrchJava {       
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";  
    
        static String searchTerm = "Microsoft";  // Replace with search term specific to your defined sources.
    
        public static SearchResults SearchImages (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    
            // receive JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
    
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
    
            // extract Bing-related HTTP headers
            Map<String, List<String>> headers = connection.getHeaderFields();
            for (String header : headers.keySet()) {
                if (header == null) continue;      // may have null key
                if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                    results.relevantHeaders.put(header, headers.get(header).get(0));
                }
            }
    
            stream.close();
            return results;
        }
    
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    
        public static void main (String[] args) {
            if (subscriptionKey.length() != 32) {
                System.out.println("Invalid Bing Search API subscription key!");
                System.out.println("Please paste yours into the source code.");
                System.exit(1);
            }
    
            try {
                System.out.println("Searching the Web for: " + searchTerm);
    
                SearchResults result = SearchImages(searchTerm);
    
                System.out.println("\nRelevant HTTP Headers:\n");
                for (String header : result.relevantHeaders.keySet())
                    System.out.println(header + ": " + result.relevantHeaders.get(header));
    
                System.out.println("\nJSON Response:\n");
                System.out.println(prettify(result.jsonResponse));
            }
            catch (Exception e) {
                e.printStackTrace(System.out);
                System.exit(1);
            }
        }
    }
    
    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    
    }
    
    ```
4. Uruchom program.
    
## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie środowiska hostowanej interfejsu użytkownika](./hosted-ui.md)
- [Umożliwia znaczników decoration wyróżnianie tekstu](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)