---
title: Wyszukiwanie obrazów GIF za pomocą interfejsu API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania obrazów Bing umożliwia również wyszukiwanie w całej sieci Web najbardziej odpowiednich obrazów gif.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: ad33b5a28728a8e7f9c0a70ff81ea5a9e65624bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072653"
---
# <a name="search-for-gif-images"></a>Wyszukiwanie obrazów GIF 

Interfejs API wyszukiwania obrazów Bing umożliwia również wyszukiwanie w całej sieci Web najbardziej odpowiednich obrazów gif.Deweloperzy mogą integrować angażujące gify w różnych scenariuszach konwersacji. 

Poniższy adres URL to zapytanie dotyczące animowanych obrazów gif.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
Parametr [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) określa wyszukiwane terminy.  Poprzednia kwerenda `animatedGif` określa również przy użyciu parametru filtru [imageType.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)

Aby wyświetlić przykłady wyników, użyj następującego adresu URL, aby wyszukać bing.com.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Parametry zapytania

Aby uzyskać więcej informacji na temat parametrów i opcji kwerendy, zobacz [odwołanie do interfejsu API wyszukiwania obrazów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters). Przykład znajduje się pod nagłówkiem [Przykład wyszukiwania animowanego gif przy użyciu języka Java](#gifExample).

## <a name="tips-and-suggestions"></a>Porady i sugestie

- Można określić parametry [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) i [minFileSize.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) Zalecamy ustawienie maxFileSize =2000000, ponieważ większość gifów w naszym indeksie jest poniżej 2MB.  Pomaga to również kontrolować rozmiar danych, jeśli przepustowość jest problemem, na przykład w scenariuszach komórkowych komórkowych.
- Aby poprawić postrzeganą wydajność, należy najpierw załadować miniaturę przed załadowaniem źródłowego adresu URL.  
- Aby uzyskać doświadczenie pierwszego uruchomienia lub strony docelowej, w której nie masz jeszcze zapytania użytkownika, spróbuj użyć naszych popularnych wyszukiwań gif, aby pomóc w [interfejsie API popularnych obrazów.](trending-images.md)
- Istnieją trzy ustawienia parametru [safeSearch.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch)  Opcja `strict` blokuje treści dla dorosłych.
- Pełna lista obsługiwanych języków i lokalizacji można znaleźć [w mkt.](supported-countries-markets.md)
- *Program AnimatedGifHttps* zwraca tylko animowane obrazy gifów pochodzące z adresu https. Ze względów bezpieczeństwa wiele aplikacji wymaga połączenia z zewnętrznymi łączami sieci Web za pośrednictwem protokołu https. Na przykład apple App Store wymaga połączenia z usługami sieci web za pośrednictwem protokołu HTTPS, który szyfruje dane użytkownika bezpieczne podczas przesyłania.

<a name="gifExample" />

## <a name="example-search-for-animated-gif-using-java"></a>Przykładowe wyszukiwanie animowanego gifa przy użyciu języka Java

Następujący adres URL wyszukuje animowane obrazy gif:`q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Jak pokazano w poniższym przykładzie, kwerenda url wymaga nagłówka [Ocp-Apim-Subscription-Key.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers)

Poniższy przykład Języka Java tworzy i wysyła żądanie.

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
Kod pobiera następujące wyniki jako JSON obiektów:

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

## <a name="next-steps"></a>Następne kroki
- [Przewodnik Szybki start dla języka C#](quickstarts/csharp.md)
- [Samouczek Wyszukiwanie obrazów aplikacji jednostronicowej](tutorial-bing-image-search-single-page-app.md)
