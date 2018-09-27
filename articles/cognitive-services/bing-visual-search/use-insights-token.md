---
title: Przy użyciu tokenu insights - wyszukiwania wizualnego Bing
titleSuffix: Azure Cognitive Services
description: Przedstawia użycie szczegółowe informacje o obrazie tokenu za pomocą API wyszukiwania wizualnego Bing Aby uzyskać szczegółowe informacje o obrazie.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: conceptual
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: e853545e07709d82fa1e3b1143392e6d6012f6f6
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227592"
---
# <a name="using-an-insights-token-to-get-insights-about-an-image"></a>Przy użyciu tokenu szczegółowe informacje, aby uzyskać szczegółowe informacje o obrazie

Interfejs API wyszukiwania wizualnego Bing zwraca informacje o obrazie, który należy podać. Aby przekazać obraz, przy użyciu adresu URL obrazu usługi insights tokenu, lub przekazanie obrazu. Aby uzyskać informacje o tych opcjach, zobacz [co to jest interfejs API wyszukiwania wizualnego Bing?](overview.md). W tym artykule przedstawiono, przy użyciu tokenu szczegółowych informacji. Przykłady demonstrujące przekazanie obrazu, aby uzyskać szczegółowe informacje, zobacz Szybki Start ([C#](quickstarts\csharp.md) | [Java](quickstarts\java.md) | [Node.js](quickstarts\nodejs.md)  |  [Python](quickstarts\python.md)).


Jeśli wyślesz wyszukiwania wizualnego token obrazu lub adres URL, poniżej przedstawiono dane formularza należy uwzględnić w treści wpisu. Dane mogą zawierać nagłówek Content-Disposition i jego `name` parametru musi być równa "knowledgeRequest". Aby uzyskać szczegółowe informacje o `imageInfo` obiektu, zobacz [żądania](#the-request).

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

W przykładach w w tym artykule przedstawiono sposób użycia tokenu szczegółowych informacji. Należy pobrać token szczegółowych informacji z obiektu obrazu /images/search odpowiedź interfejsu API. Aby dowiedzieć się, jak uzyskiwanie tokenu szczegółowych informacji, zobacz [interfejsu API wyszukiwania obrazów Bing](../Bing-Image-Search/overview.md).

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```


Przykłady, które używają tokenu szczegółowych informacji, zobacz [C#](#using-csharp) | [Java](#using-java) | [Node.js](#using-nodejs) | [Python](#using-python).

<a name="using-csharp" />

## <a name="using-c"></a>Przy użyciu języka C#

### <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) aby otrzymać ten kod w systemie Windows. (Można korzystać z bezpłatnej wersji Community Edition).

W tym przewodniku Szybki Start, możesz użyć [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatna subskrypcja lub subskrypcja.

## <a name="running-the-application"></a>Uruchamianie aplikacji

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz nowe rozwiązanie konsoli w programie Visual Studio.
1. Zastąp zawartość `Program.cs` kodem przedstawionym w tym przewodniku Szybki Start.
2. Zastąp `accessKey` wartość z kluczem subskrypcji.
2. Zastąp `insightsToken` wartość tokenem szczegółowych informacji z odpowiedzi wyszukiwania/obrazy /.
3. Uruchom program.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```

<a name="using-java" />

## <a name="using-java"></a>Korzystanie z języka Java

### <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć zestaw [JDK 7 lub 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), aby skompilować i uruchomić ten kod. Jeśli masz Ulubione, ale wystarczy edytora tekstów, można użyć Java IDE.

W tym przewodniku Szybki Start, możesz użyć [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatna subskrypcja lub subskrypcja.

## <a name="running-the-application"></a>Uruchamianie aplikacji

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Pobierz i zainstaluj [biblioteki gson](https://github.com/google/gson). Może również uzyskać za pomocą narzędzia Maven.
2. Utwórz nowy projekt języka Java w Twoim ulubionym środowiskiem IDE lub edytora.
3. Dodaj kod podany w pliku o nazwie `VisualSearch.java`.
4. Zastąp `subscriptionKey` wartość z kluczem subskrypcji.
5. Uruchom program.

```java
package insightstoken;

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


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
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


<a name="using-nodejs" />

## <a name="using-nodejs"></a>Używanie środowiska Node.js

### <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz [Node.js 6](https://nodejs.org/en/download/) do uruchamiania tego kodu.

W tym przewodniku Szybki Start, możesz użyć [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatna subskrypcja lub subskrypcja.

## <a name="running-the-application"></a>Uruchamianie aplikacji

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz folder dla projektu (lub użyj Twoim ulubionym środowiskiem IDE lub edytora).
2. W wierszu polecenia lub terminalu przejdź do folderu, który został utworzony.
3. Zainstaluj moduły żądania:  
  ```  
  npm install request  
  ```  
3. Zainstaluj moduły danych formularza:  
  ```  
  npm install form-data  
  ```  
4. Utwórz plik o nazwie GetVisualInsights.js i Dodaj następujący kod do niego.
5. Zastąp `subscriptionKey` wartość z kluczem subskrypcji.
7. Uruchom program.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


<a name="using-python" />

## <a name="using-python"></a>Korzystanie z języka Python


### <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz [Python 3](https://www.python.org/) do uruchamiania tego kodu.

W tym przewodniku Szybki Start, możesz użyć [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatna subskrypcja lub subskrypcja.

## <a name="running-the-walkthrough"></a>Uruchamianie przewodnik

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz nowy projekt języka Python w Twoim ulubionym środowiskiem IDE lub edytora.
2. Utwórz plik o nazwie visualsearch.py i Dodaj kod przedstawiony w tym przewodniku Szybki Start.
3. Zastąp `SUBSCRIPTION_KEY` wartość z kluczem subskrypcji.
4. Uruchom program.


```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest' : (None, formData)}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Kolejne kroki

[Samouczek dotyczący aplikacji jednostronicowej wyszukiwania wizualnego Bing](tutorial-bing-visual-search-single-page-app.md)  
[Przegląd wyszukiwania wizualnego Bing](overview.md)  
[Wypróbuj](https://aka.ms/bingvisualsearchtryforfree)  
[Pobierz klucz bezpłatny dostęp próbny](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Dokumentacja interfejsu API wyszukiwania wizualnego Bing](https://aka.ms/bingvisualsearchreferencedoc)
