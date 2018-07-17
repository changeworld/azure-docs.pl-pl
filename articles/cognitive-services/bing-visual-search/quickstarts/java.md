---
title: 'Szybki Start: Java API wyszukiwania wizualnego Bing | Dokumentacja firmy Microsoft'
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Pokazuje, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskanie szczegółowych informacji o obrazie.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 41e0855b126ca6e54d0a487a88fe59a0be6f72f6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071999"
---
# <a name="your-first-bing-visual-search-query-in-java"></a>Pierwsze zapytanie wyszukiwania wizualnego Bing w języku Java

Interfejs API wyszukiwania wizualnego Bing zwraca informacje o obrazie, który należy podać. Aby przekazać obraz, przy użyciu adresu URL obrazu usługi insights tokenu, lub przekazanie obrazu. Aby uzyskać informacje o tych opcjach, zobacz [co to jest interfejs API wyszukiwania wizualnego Bing?](../overview.md) W tym artykule przedstawiono przekazywania obrazu. Przekazywanie obrazu może być przydatne w scenariuszach mobilnych, gdzie Zrób zdjęcie dobrze znanych charakterystycznych elementów krajobrazu i uzyskanie informacji na jego temat. Na przykład szczegółowe informacje zawiera elementy towarzyszące składni o charakterystycznych elementów krajobrazu. 

Jeśli załadujesz lokalny obraz, poniżej przedstawiono dane formularza należy uwzględnić w treści wpisu. Dane mogą zawierać nagłówek Content-Disposition. Jego `name` parametru musi być równa "image" i `filename` parametru może być ustawiona na dowolny ciąg. Zawartość formularza jest plik binarny obrazu. Rozmiar maksymalny obrazu, którą możesz przekazać to 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ten artykuł zawiera prostą aplikację konsolową, która wysyła żądanie interfejsu API wyszukiwania wizualnego Bing i wyświetla wyniki wyszukiwania JSON. Podczas tej aplikacji został napisany w języku Java, interfejs API jest zgodny z dowolnego języka programowania, który może wysyłać żądania HTTP i Przeanalizuj dane JSON usługi sieci Web typu RESTful. 


## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [JDK 7 lub 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) Aby skompilować i uruchomić ten kod. Jeśli masz Ulubione, ale wystarczy edytora tekstów, można użyć Java IDE.

W tym przewodniku Szybki Start, możesz użyć [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatna subskrypcja lub subskrypcja.

## <a name="running-the-application"></a>Uruchamianie aplikacji

Poniżej przedstawiono sposób przekazywania obrazów przy użyciu MultipartEntityBuilder w języku Java.

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Pobierz i zainstaluj [biblioteki gson](https://github.com/google/gson). Może również uzyskać za pomocą narzędzia Maven.
2. Utwórz nowy projekt języka Java w Twoim ulubionym środowiskiem IDE lub edytora.
3. Dodaj kod podany w pliku o nazwie `VisualSearch.java`.
4. Zastąp `subscriptionKey` wartość z kluczem subskrypcji.
4. Zastąp `imagePath` na ścieżkę obrazu do przekazania.
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

[Uzyskiwanie szczegółowych informacji o pliku obrazu przy użyciu tokenu usługi insights](../use-insights-token.md)  
[Samouczek przekazywania obrazów wyszukiwania wizualnego Bing](../tutorial-visual-search-image-upload.md)
[samouczek dotyczący aplikacji jednostronicowej wyszukiwania wizualnego Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Przegląd wyszukiwania wizualnego Bing](../overview.md)  
[Wypróbuj](https://aka.ms/bingvisualsearchtryforfree)  
[Pobierz klucz bezpłatny dostęp próbny](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Dokumentacja interfejsu API wyszukiwania wizualnego Bing](https://aka.ms/bingvisualsearchreferencedoc)

