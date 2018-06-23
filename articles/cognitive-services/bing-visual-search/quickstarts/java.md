---
title: Szybki Start Java Visual wyszukiwania usługi Bing interfejsu API | Dokumentacja firmy Microsoft
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Pokazuje, jak przekazać obraz Visual API wyszukiwania usługi Bing i wrócić szczegółowych informacji o obrazie.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 8160302faa373d69b65afe6b68a8efb44442850d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349373"
---
# <a name="your-first-bing-visual-search-query-in-java"></a>Pierwszego zapytania Visual wyszukiwania usługi Bing w języku Java

Visual API wyszukiwania usługi Bing zwraca informacje dotyczące obrazu, który podasz. Musisz podać obrazu przy użyciu adresu URL obrazu, insights token, lub przekazując obrazu. Aby uzyskać informacje o tych opcjach, zobacz [co to jest Visual API wyszukiwania usługi Bing?](../overview.md) W tym artykule przedstawiono przekazywanie obrazu. Przekazywanie obrazu może być przydatne w scenariuszach mobilnych, gdzie obrazu dzielnicę dobrze znanych i odzyskać informacje o nim. Na przykład szczegółowych danych może zawierać elementy towarzyszące składni o punkty. 

Jeśli Przekaż obraz lokalnych, poniżej przedstawiono dane formularza należy uwzględnić w treści POST. Dane formularza musi zawierać nagłówek Content-Disposition. Jego `name` parametr musi być ustawiony na "obrazu" i `filename` parametr może być ustawiony na dowolny ciąg. Zawartość formularza jest plikiem binarnym obrazu. Rozmiar maksymalny obrazu, które mogą przekazać to 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ten artykuł zawiera prostej aplikacji konsolowej wysyła żądanie Visual API wyszukiwania usługi Bing i wyświetla wyniki wyszukiwania JSON. Ta aplikacja jest napisany w języku Java, interfejsu API jest zgodny z żadnego języka programowania, które mogą wysyłać żądania HTTP i przeanalizować składni JSON usługi sieci RESTful Web. 


## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [JDK 7 lub 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) Aby skompilować i uruchomić ten kod. Jeśli masz element ulubiony, ale wystarczy edytora tekstu, może używać IDE języka Java.

Dla tego przewodnika Szybki Start, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatną subskrypcję lub subskrypcji.

## <a name="running-the-application"></a>Uruchamianie aplikacji

Poniżej przedstawiono sposób przekazywania obrazu przy użyciu MultipartEntityBuilder w języku Java.

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Pobierz i zainstaluj [gson biblioteki](https://github.com/google/gson). Mogą również uzyskać za pośrednictwem Maven.
2. Utwórz nowy projekt języka Java w ulubionych IDE lub edytora.
3. Dodaj kod podany w pliku o nazwie `VisualSearch.java`.
4. Zastąp `subscriptionKey` wartości z klucza subskrypcji.
4. Zastąp `imagePath` wartości o ścieżce obrazu do przekazania.
5. Uruchom program.


```java
package uploadimage;

import java.util.*;
import java.io.*;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// http://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class UploadImage2 {

    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere";
    static String imagePath = "<pathtoyourimagetouploadgoeshere>";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addBinaryBody("image", new File(imagePath))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    
}
```

## <a name="next-steps"></a>Kolejne kroki

[Uzyskiwanie szczegółowych informacji o obrazie przy użyciu tokenu insights](../use-insights-token.md)  
[Samouczek aplikacji jednej strony Visual wyszukiwania usługi Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Omówienie Visual wyszukiwania usługi Bing](../overview.md)  
[Wypróbuj](https://aka.ms/bingvisualsearchtryforfree)  
[Pobierz klucz bezpłatnej wersji próbnej dostępu](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Odwołanie do Visual API wyszukiwania usługi Bing](https://aka.ms/bingvisualsearchreferencedoc)

