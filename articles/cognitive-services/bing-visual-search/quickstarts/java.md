---
title: 'Szybki start: tworzenie zapytania wyszukiwania wizualnego, Java — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Sposób przekazywania obrazu do interfejsu API wyszukiwania wizualnego Bing i uzyskiwanie szczegółowych informacji zwrotnych o obrazie.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 56e1b943f03128fa6703a7b15bd0d6ade09089d6
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222628"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-java"></a>Szybki start: pierwsze zapytanie wyszukiwania wizualnego Bing w języku Java

Interfejs API wyszukiwania wizualnego Bing zwraca informacje o podanym obrazie. Obraz można podać przy użyciu adresu URL obrazu, tokenu szczegółowych informacji lub przez przekazanie obrazu. Aby uzyskać informacje o tych opcjach, zobacz artykuł [Czym jest interfejs API wyszukiwania wizualnego Bing?](../overview.md) W tym artykule opisano przekazywanie obrazu. Przekazywanie obrazu może być przydatne w scenariuszach mobilnych, w których po zrobieniu zdjęcia znanego, charakterystycznego elementu krajobrazu zwracane są informacje na jego temat. Szczegółowe informacje mogą na przykład zawierać ciekawostki na temat charakterystycznego elementu krajobrazu. 

W przypadku przekazywania lokalnego obrazu w treści żądania POST konieczne jest podanie pokazanych poniżej danych formularza. Dane formularza muszą zawierać nagłówek Content-Disposition. Jego parametr `name` musi mieć wartość „image”, a parametr `filename` może być ustawiony na dowolny ciąg. Zawartością formularza jest plik binarny obrazu. Maksymalny rozmiar obrazu, który można przekazać, wynosi 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

W tym artykule zawarto prostą aplikację konsolową, która wysyła żądanie interfejsu API wyszukiwania wizualnego Bing i wyświetla wyniki wyszukiwania w formacie JSON. Ta aplikacja została napisana w języku Java, a interfejs API jest usługą internetową zgodną z wzorcem REST i każdym językiem programowania, który może wysyłać żądania HTTP i analizować dane JSON. 


## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć zestaw [JDK 7 lub 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), aby skompilować i uruchomić ten kod. Możesz użyć środowiska projektowego Java, jeśli masz ulubione, ale edytor tekstu również wystarczy.

Na potrzeby tego przewodnika Szybki start możesz użyć klucza subskrypcji [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) lub klucza subskrypcji płatnej.

## <a name="running-the-application"></a>Uruchamianie aplikacji

Poniżej przedstawiono sposób przekazywania obrazów przy użyciu klasy MultipartEntityBuilder w języku Java.

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Pobierz i zainstaluj [bibliotekę gson](https://github.com/google/gson). Możesz ją również uzyskać za pomocą narzędzia Maven.
2. Utwórz nowy projekt języka Java w ulubionym środowisku IDE lub edytorze.
3. Dodaj kod podany w pliku o nazwie `VisualSearch.java`.
4. Zastąp wartość elementu `subscriptionKey` kluczem subskrypcji.
4. Zastąp wartość elementu `imagePath` ścieżką obrazu do przekazania.
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

## <a name="next-steps"></a>Następne kroki

[Uzyskiwanie szczegółowych informacji o obrazie przy użyciu tokenu szczegółowych informacji](../use-insights-token.md)  
[Samouczek dotyczący przekazywania obrazów na potrzeby wyszukiwania wizualnego Bing](../tutorial-visual-search-image-upload.md)
[Samouczek dotyczący aplikacji jednostronicowej wyszukiwania wizualnego Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Omówienie wyszukiwania wizualnego Bing](../overview.md)  
[Wypróbuj](https://aka.ms/bingvisualsearchtryforfree)  
[Pobierz klucz dostępu do bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Dokumentacja interfejsu API wyszukiwania wizualnego Bing](https://aka.ms/bingvisualsearchreferencedoc)

