---
title: Pobierz sformatowane - kognitywnych usługi firmy Microsoft | Dokumentacja firmy Microsoft
description: Jak używać interfejsu API wyszukiwania usługi Bing obrazu, aby uzyskać więcej informacji o obrazach GIF.
services: cognitive-services
author: MikeDodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/24/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 10e922b0cd15868bfe8f09b3846c76a368052e69
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349325"
---
# <a name="search-for-gif-images"></a>Wyszukaj obrazy GIF
Interfejs API wyszukiwania usługi Bing obraz umożliwia również wyszukiwanie w całej sieci Web najistotniejsza obrazów GIF.  Deweloperzy mogą integrować atrakcyjnej plików GIF w różnych scenariuszach konwersacji. 

Następujący adres URL jest zapytaniem obrazów animowany GIF.
````
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
````
[q](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#query) parametr określa z warunkami wyszukiwania.  Poprzednie zapytanie określa również `animatedGif` przy użyciu [imageType](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) parametr filtru.

Aby wyświetlić przykłady wyników, użyj następującego adresu URL do wyszukiwania bing.com.
````
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif 

````
## <a name="query-parameters"></a>Parametry zapytania

Aby uzyskać więcej informacji na temat parametrów zapytania i opcji, zobacz [dokumentacja interfejsu API wyszukiwania obrazu](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). Przykład następuje pod nagłówkiem [przykład wyszukaj animowany plik gif przy użyciu języka Java](#gifExample).

## <a name="tips-and-suggestions"></a>Porady i sugestie

- Można określić [maxFileSize](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) i [minFileSize](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) parametrów. Firma Microsoft zaleca ustawienie maxFileSize = 2000000 większość plików GIF w indeksie jest poniżej 2MB.  Pomaga to również kontrolować rozmiar danych, jeśli przepustowość jest istotny, takich jak w scenariuszach mobilnych komórkową.
- Aby zwiększyć wydajność, obciążenia miniatur przed ładowania źródłowy adres url.  
- Dla pierwszego uruchomienia lub którym nie ma zapytania użytkownika jeszcze obsługi strony docelowej, spróbuj użyć naszych trendów wyszukiwania gif, aby pomóc z [umożliwia analizę trendów obrazy interfejsu API](trending-images.md).
- Dostępne są trzy ustawienia dla [bezpieczne wyszukiwanie](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) parametru.  `strict` Opcja blokuje zawartość dla dorosłych. 
- Zobacz [mkt](supported-countries-markets.md) pełną listę języków obsługiwanych lokalizacji.
- *AnimatedGifHttps* zwraca tylko animowany plik gif obrazów, które pochodzą z adresem https. Dla bezpieczeństwa wiele aplikacji wymagają połączenia z łącza zewnętrzne sieci web za pośrednictwem protokołu https. Na przykład sklepu z aplikacjami firmy Apple wymaga połączenia z usługami sieci web za pośrednictwem protokołu HTTPS, który szyfruje bezpiecznego przesyłanych danych użytkownika.

<a name="gifExample" />
## <a name="example-search-for-animated-gif-using-java"></a>Przykład wyszukaj animowany plik gif przy użyciu języka Java

Następujący adres URL wyszukuje animowany sformatowane: `q=interesting`
````
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

````
Jak pokazano w poniższym przykładzie, zapytanie adresu URL wymaga [Ocp-Apim-subskrypcji — klucz](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#headers) nagłówka.

W poniższym przykładzie Java kompilacje i wysyła żądanie.

````
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

````

## <a name="results"></a>Wyniki
Ten kod pobiera następujące wyniki jako obiektów JSON:

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
- [C# — Szybki Start](quickstarts/csharp.md)
- [Samouczek aplikacji jednej strony wyszukiwania obrazu](tutorial-bing-image-search-single-page-app.md)