---
title: Wyszukiwanie obrazów GIF przy użyciu interfejsu API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API wyszukiwania obrazów Bing, aby wyszukać obrazów GIF, w sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 0a2040424aa70f30831e214ce0b05d21414ff45c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389404"
---
# <a name="search-for-gif-images"></a>Wyszukiwanie obrazów GIF 

Interfejs API wyszukiwania obrazów Bing umożliwia również wyszukiwanie w całym internetowym dla najbardziej istotnych obrazów GIF.  Programiści mogą zintegrować atrakcyjne GIF w różnych scenariuszach w konwersacji. 

Następujący adres URL jest zapytanie dotyczące obrazów animowany obraz GIF.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
[q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) parametr określa terminy wyszukiwania.  Poprzednie zapytanie określa również `animatedGif` przy użyciu [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) parametr filtru.

Aby zobaczyć przykłady wyników, użyj następującego adresu URL, aby wyszukać bing.com.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Parametry zapytania

Aby uzyskać więcej informacji na temat parametrów zapytania i opcji, zobacz [dokumentacja interfejsu API wyszukiwania obrazów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters). Przykładem jest zgodna pod nagłówkiem [przykład wyszukaj animowany plik gif, za pomocą języka Java](#gifExample).

## <a name="tips-and-suggestions"></a>Porady i sugestie

- Można określić [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) i [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) parametrów. Zalecamy ustawienie maxFileSize = 2000000, ponieważ większość plików GIF w indeksie jest w obszarze 2MB.  Pomaga to również do kontrolowania rozmiaru danych, jeśli przepustowość jest istotny, takie jak w scenariuszach mobilnych, sieci komórkowej.
- Aby poprawić wydajność, należy załadować miniaturę przed ładowania źródłowy adres url.  
- Dla pierwszego uruchomienia lub docelowej strony środowisko, której nie ma zapytania użytkownika jeszcze, spróbuj użyć naszego popularne wyszukiwania gif, aby pomocy [popularne obrazy interfejsu API](trending-images.md).
- Dostępne są trzy ustawienia dla [bezpieczne wyszukiwanie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) parametru.  `strict` Opcja blokuje treści dla dorosłych.
- Zobacz [mkt](supported-countries-markets.md) pełną listę języków obsługiwanych lokalizacji.
- *AnimatedGifHttps* tylko zwraca animowane obrazów gif, które są z adresu https. Dla bezpieczeństwa wiele aplikacji wymaga połączenia z linków zewnętrznych sieci web przy użyciu protokołu https. Na przykład Apple Store aplikacji wymaga połączenia z usługami sieci web przy użyciu protokołu HTTPS, który szyfruje bezpiecznego przesyłanych danych użytkownika.

<a name="gifExample" />

## <a name="example-search-for-animated-gif-using-java"></a>Przykład wyszukaj animowany plik gif, za pomocą języka Java

Następujący adres URL wyszukuje animowany obraz GIF obrazów: `q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Jak pokazano w poniższym przykładzie, zapytanie adresu URL wymaga [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers) nagłówka.

W poniższym przykładzie w języku Java, tworzy i wysyła żądanie.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
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

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>Wyniki
Ten kod pobiera następujące wyniki jako obiekty JSON:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Kolejne kroki
- [Przewodnik Szybki start dla języka C#](quickstarts/csharp.md)
- [Samouczek aplikacji jednostronicowej wyszukiwania obrazów](tutorial-bing-image-search-single-page-app.md)
