---
title: Szybki start — wysyłanie kwerendy do interfejsu API w języku C# przy użyciu usługi Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki start służy do rozpoczynania wysyłania żądań do interfejsu API wyszukiwania lokalnego firmy Bing, który jest usługą Azure Cognitive Service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 62e975121c05bd4fde2438480f7042b0957a9c82
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475602"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-c"></a>Szybki start: wysyłanie kwerendy do interfejsu API wyszukiwania lokalnego firmy Bing w języku C #

Ten przewodnik Szybki start służy do rozpoczynania wysyłania żądań do interfejsu API wyszukiwania lokalnego firmy Bing, który jest usługą Azure Cognitive Service. Podczas gdy ta prosta aplikacja jest napisana w języku C#, interfejs API jest usługą sieci Web RESTful, która jest zgodna z dowolnym językiem programowania umożliwiającym wykonywanie żądań HTTP i analizowanie języka JSON.

Ta przykładowa aplikacja pobiera dane odpowiedzi lokalnej `hotel in Bellevue`z interfejsu API dla kwerendy wyszukiwania .

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna edycja [programu Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).

Musisz mieć [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z interfejsami API wyszukiwania Bing. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca na potrzeby tego przewodnika Szybki start.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-the-request"></a>Tworzenie żądania 

Poniższy kod `WebRequest`tworzy , ustawia nagłówek klucza dostępu i dodaje ciąg zapytania dla "restauracja w Bellevue".  Następnie wysyła żądanie i przypisuje odpowiedź do ciągu zawierającego tekst w formacie JSON.

```csharp
    // Replace the accessKey string value with your valid access key.
    const string accessKey = "enter key here";

    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search";   

    const string searchTerm = "restaurant in Bellevue";
    // Construct the URI of the search request
    var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + mkt=en-us;

    // Run the Web request and get response.
    WebRequest request = HttpWebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
```

## <a name="run-the-complete-application"></a>Uruchamianie kompletnej aplikacji

Interfejs API wyszukiwania lokalnego firmy Bing zwraca zlokalizowane wyniki wyszukiwania z wyszukiwarki Bing.
1. Utwórz nowe rozwiązanie konsolowe w programie Visual Studio (wersja Community Edition jest wystarczająca).
2. Zastąp plik Program.cs kodem przedstawionym poniżej.
3. Zastąp accessKey wartość klucza dostępu ważny dla subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace localSearch
{
    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the accessKey string value with your valid access key.
        const string accessKey = "enter key here";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search";   

        const string searchTerm = "restaurant in Bellevue";

        // Returns search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm, accessKey);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Performs a Bing Local business search and return the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery, string key)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "&mkt=en-us";

            // Perform the Web request and get the response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = key; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
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
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
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
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}

```

## <a name="next-steps"></a>Następne kroki
- [Przewodnik Szybki start java wyszukiwania lokalnego firmy](local-search-java-quickstart.md)
- [Szybki start lokalnego węzła wyszukiwania firm](local-search-node-quickstart.md)
- [Szybki start wyszukiwania dla firm lokalnych](local-search-python-quickstart.md)
