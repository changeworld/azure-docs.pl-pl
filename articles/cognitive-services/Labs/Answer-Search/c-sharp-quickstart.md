---
title: C# szybkiego startu dla usług kognitywnych firmy Microsoft, wyszukiwanie odpowiedzi projektu | Dokumentacja firmy Microsoft
description: Przykładowy kod, aby rozpocząć, używając przycisku Wyszukaj odpowiedzi projektu kognitywnych usług Microsoft Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: b05b7745bdb5020a4a0912635e040256415b2626
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348580"
---
# <a name="project-answer-search-query-in-c"></a>Zapytania wyszukiwania odpowiedzi projektu w języku C#

W poniższym przykładzie C#, tworzy i wysyła zapytanie, aby uzyskać informacje dotyczące trzeci prawa calculus.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) tego kodu dla systemu Windows. (Bezpłatna wersja Community będzie działać.)

Pobierz klucz dostępu dla bezpłatnej wersji próbnej [kognitywnych Labs usług](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scenariusz kodu

Poniższy kod C# tworzy i wysyła zapytanie. 

Jest stosowana w poniższych krokach:
1. Zadeklaruj zmienne w celu określenia punktu końcowego i adresu URL zapytania, aby wyświetlić podgląd.  
2. Utwórz żądanie.
3. Dodaj *Ocp-Apim-subskrypcji — klucz* nagłówka. 
4. Wykonywane asynchronicznie żądanie sieci Web. 
5. Przeczytaj odpowiedzi.
6. Drukowanie nagłówków i wyniki JSON do konsoli.

**Kod źródłowy**

```
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace Answers_csharp
{
    class Program
    {
        // Replace the accessKey string value with your valid access key.
        const string accessKey = "YOUR-SUBSCRIPTION-KEY";

        const string uriBase = "https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search "; 

        const string searchTerm = "third law of calculus"; 

        // Used to return news search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Does Bing knowledge search and returns the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "&mkt=en-us";

            // Send the Web request and get the response.
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

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
## <a name="running-the-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację:

1. Utwórz nowe rozwiązanie konsoli w programie Visual Studio.
2. Zastąp `Program.cs` podany kod.
3. Zastąp `YOUR-ACCESS-KEY` wartość kluczem dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

## <a name="next-steps"></a>Kolejne kroki
[Szybki Start Java](java-quickstart.md)
