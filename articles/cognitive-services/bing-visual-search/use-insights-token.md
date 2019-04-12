---
title: Przy użyciu tokenu insights - wyszukiwania wizualnego Bing
titleSuffix: Azure Cognitive Services
description: Przedstawia użycie szczegółowe informacje o obrazie tokenu za pomocą API wyszukiwania wizualnego Bing Aby uzyskać szczegółowe informacje o obrazie.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 4/05/2019
ms.author: scottwhi
ms.openlocfilehash: e42e56e6361b1fde7ab13655d3c57a90d7235938
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493879"
---
# <a name="use-an-insights-token-to-get-insights-for-an-image"></a>Użyj tokenu szczegółowe informacje, aby uzyskać szczegółowe informacje o obrazie

Interfejs API wyszukiwania wizualnego Bing zwraca informacje o udostępnionym obrazie. Obraz można udostępnić przy użyciu adresu URL obrazu, tokenu szczegółowych informacji lub przez przekazanie obrazu. Aby uzyskać informacje o tych opcjach, zobacz [co to jest interfejs API wyszukiwania wizualnego Bing?](overview.md). W tym artykule przedstawiono, przy użyciu tokenu szczegółowych informacji. Przykłady demonstrujące sposób przekazywania obrazu, aby uzyskać szczegółowe informacje, zobacz Szybki Start ([C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [Node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md)).

Jeśli wyślesz wyszukiwania wizualnego Bing token obrazu lub adres URL, poniżej przedstawiono dane formularza należy uwzględnić w treści wpisu. Musi zawierać dane formularza `Content-Disposition` nagłówka, na które należy ustawić jego `name` parametr "knowledgeRequest". Aby uzyskać szczegółowe informacje o `imageInfo` obiektu, zobacz żądania:

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

W przykładach w w tym artykule przedstawiono sposób użycia tokenu szczegółowych informacji. Pobierz token szczegółowych informacji z `Image` obiektu /images/wyszukiwania odpowiedzi interfejsu API. Aby dowiedzieć się, jak uzyskiwanie tokenu szczegółowych informacji, zobacz [co to jest interfejs API wyszukiwania obrazów Bing?](../Bing-Image-Search/overview.md).

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

Przykłady, które używają tokenu szczegółowych informacji, zobacz [C#](#use-with-c) | [Java](#use-with-java) | [Node.js](#use-with-nodejs) | [Python](#use-with-python).

## <a name="use-with-c"></a>Za pomocąC#

### <a name="c-prerequisites"></a>C#wymagania wstępne

- Dowolna wersja [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) aby otrzymać ten kod w systemie Windows.
- Subskrypcja platformy Azure. W tym przewodniku Szybki Start, możesz użyć [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatna subskrypcja lub subskrypcja.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz rozwiązanie konsoli w programie Visual Studio.
2. Zastąp zawartość pliku Program.cs kodem przedstawionym w tym przewodniku Szybki Start.
3. Zastąp wartość elementu `accessKey` kluczem subskrypcji.
4. Zastąp `insightsToken` wartość tokenem szczegółowych informacji z odpowiedzi wyszukiwania/obrazy /.
5. Uruchom program.

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

## <a name="use-with-java"></a>Korzystanie z języka Java

### <a name="java-prerequisites"></a>Wymagania wstępne dotyczące języka Java

- Należy użyć [JDK 7 lub 8](https://aka.ms/azure-jdks) Aby skompilować i uruchomić ten kod. Jeśli masz Ulubione, ale wystarczy edytora tekstów, możesz użyć Java IDE.
- W tym przewodniku Szybki Start, możesz użyć [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatna subskrypcja lub subskrypcja.

## <a name="run-the-java-application"></a>Uruchom aplikację języka Java

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Pobierz i zainstaluj [biblioteka języka Gson Java](https://github.com/google/gson). Można również uzyskać Gson za pomocą narzędzia Maven.
2. Utwórz nowy projekt języka Java w ulubionym środowisku IDE lub edytorze.
3. Dodaj kod podany w pliku o nazwie `VisualSearch.java`.
4. Zastąp wartość elementu `subscriptionKey` kluczem subskrypcji.
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

// https://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

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

## <a name="use-with-nodejs"></a>Użyj przy użyciu środowiska Node.js

### <a name="nodejs-prerequisites"></a>Wymagania wstępne dotyczące środowiska node.js

- Konieczne jest posiadanie [Node.js 6](https://nodejs.org/en/download/) do uruchamiania tego kodu.
- W tym przewodniku Szybki Start, możesz użyć [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatna subskrypcja lub subskrypcja.

## <a name="run-the-javascript-application"></a>Uruchom aplikację języka JavaScript

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz folder dla projektu (lub użyj swojego ulubionego środowiska IDE lub edytora).
2. W wierszu polecenia lub terminalu przejdź do właśnie utworzonego folderu.
3. Zainstaluj moduły żądania:
  
   ```
   npm install request  
   ```
1. Zainstaluj moduły form-data:  
   ```
   npm install form-data  
   ```
1. Utwórz plik o nazwie GetVisualInsights.js i dodaj do niego następujący kod.
1. Zastąp wartość elementu `subscriptionKey` kluczem subskrypcji.
1. Uruchom program.  
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

## <a name="use-with-python"></a>Korzystanie z języka Python

### <a name="python-prerequisites"></a>Wymagania wstępne dotyczące języka Python

- Konieczne jest posiadanie [Python 3](https://www.python.org/) do uruchamiania tego kodu.
- Na potrzeby tego przewodnika Szybki start możesz użyć klucza subskrypcji [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) lub klucza subskrypcji płatnej.

## <a name="run-the-python-application"></a>Uruchom aplikację w języku Python

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora.
2. Utwórz plik o nazwie visualsearch.py i dodaj kod przedstawiony w tym przewodniku Szybki start.
3. Zastąp wartość elementu `SUBSCRIPTION_KEY` kluczem subskrypcji.
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

[Tworzenie aplikacji internetowej z jednej strony wyszukiwania wizualnego](tutorial-bing-visual-search-single-page-app.md)  
[Co to jest interfejs API wyszukiwania wizualnego Bing?](overview.md)  
[Wypróbuj usługi Cognitive Services](https://aka.ms/bingvisualsearchtryforfree)  
[Pobierz klucz bezpłatny dostęp próbny](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Obrazy — wyszukiwanie wizualne](https://aka.ms/bingvisualsearchreferencedoc)
