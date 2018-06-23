---
title: Szybki Start C# Podgląd adres URL projektu - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Rozpoczynanie pracy przy użyciu adresu URL projektu w wersji zapoznawczej w kognitywnych usług Microsoft Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 17d44bd0c23d0a1e67da5a0e91248700d3166c1a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348636"
---
# <a name="url-preview-query-in-c"></a>Adres URL podglądu kwerendy w języku C#

W poniższym przykładzie C# tworzy podgląd adres Url witryny sieci SwiftKey Web: https://swiftkey.com/en.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) tego kodu dla systemu Windows. (Bezpłatna wersja Community będzie działać.)

Pobierz klucz dostępu dla bezpłatnej wersji próbnej [kognitywnych Labs usług](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scenariusz kodu

Poniższy kod C# tworzy podgląd adres URL witryny sieci SwiftKey Web: https://swiftkey.com/en. 

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
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
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
## <a name="running-the-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację:

1. Utwórz nowe rozwiązanie konsoli w programie Visual Studio.
2. Zastąp `Program.cs` podany kod.
3. Zastąp `YOUR-ACCESS-KEY` wartość kluczem dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

## <a name="next-steps"></a>Kolejne kroki
- [Szybki Start Java](java-quickstart.md)
- [JavaScript — Szybki Start](javascript.md)
- [Węzeł Szybki Start](node-quickstart.md)
- [Krótkie wprowadzenie do języka Python](python-quickstart.md)