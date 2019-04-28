---
title: 'Szybki start: Projekt Podgląd adresu URL, C#'
titlesuffix: Azure Cognitive Services
description: Rozpocznij korzystanie z laboratorium Project URL Preview z użyciem języka C#.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 0e2d4b783068cfd14385eb1712ea246c94dc9500
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335486"
---
# <a name="quickstart-url-preview-query-in-c"></a>Szybki start: Podgląd adresu URL — zapytanie w języku C#

Poniższy przykład w języku C# tworzy podgląd adresu URL witryny internetowej SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Wymagania wstępne

Do uruchamiania tego kodu w systemie Windows jest potrzebny [program Visual Studio 2017](https://www.visualstudio.com/downloads/). (Można korzystać z bezpłatnej wersji Community Edition).

Pobierz klucz dostępu dla bezpłatnej wersji próbnej zestawu [Cognitive Services Labs](https://aka.ms/answersearchsubscription).

## <a name="code-scenario"></a>Scenariusz kodu

Poniższy kod w języku C# tworzy podgląd adresu URL witryny internetowej SwiftKey: https://swiftkey.com/en. 

W celu zaimplementowania kodu wykonaj następujące kroki:
1. Zadeklaruj zmienne, aby określić punkt końcowy i adres URL zapytania, dla którego ma zostać wyświetlony podgląd.  
2. Utwórz żądanie.
3. Dodaj nagłówek *Ocp-Apim-Subscription-Key*. 
4. Uruchom asynchronicznie żądanie internetowe. 
5. Odczytaj odpowiedź.
6. Wydrukuj w konsoli nagłówki i wyniki w formacie JSON.

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

1. Utwórz nowe rozwiązanie konsolowe w programie Visual Studio.
2. Zastąp zawartość pliku `Program.cs` udostępnionym kodem.
3. Zastąp wartość `YOUR-ACCESS-KEY` prawidłowym kluczem dostępu dla Twojej subskrypcji.
4. Uruchom program.

## <a name="next-steps"></a>Kolejne kroki
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla języka JavaScript](javascript.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)
- [Przewodnik Szybki start dla języka Python](python-quickstart.md)
