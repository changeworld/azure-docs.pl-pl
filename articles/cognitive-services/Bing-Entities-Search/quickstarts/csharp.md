---
title: C# szybkiego startu dla usług Azure kognitywnych, wyszukiwania usługi Bing jednostki interfejsu API | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API wyszukiwania usługi Bing jednostki w kognitywnych usług Microsoft Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-jaswel
ms.openlocfilehash: 928cddf5017890bddd25b9da3584d230cc44483a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349197"
---
# <a name="quickstart-for-microsoft-bing-entity-search-api-with-c"></a>Szybki Start dla usługi Bing firmy Microsoft encji wyszukiwania interfejsu API w języku C# 
<a name="HOLTop"></a>

W tym artykule przedstawiono sposób użycia [wyszukiwania usługi Bing jednostki](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) interfejsu API w języku C#.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) tego kodu dla systemu Windows. (Bezpłatna wersja Community będzie działać.)

Musi mieć [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsu API Bing encji wyszukiwania**. [Bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) jest wystarczająca dla tego przewodnika Szybki Start. Należy klucz dostępu podany przy wywołaniu metody aktywacji bezpłatną wersję próbną lub może używać klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="search-entities"></a>Jednostek wyszukiwania

Aby uruchomić tę aplikację, wykonaj następujące kroki.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Net.Http;
using System.Text;

namespace EntitySearchSample
{
    class Program
    {
        static string host = "https://api.cognitive.microsoft.com";
        static string path = "/bing/v7.0/entities";

        static string market = "en-US";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string query = "italian restaurant near me";

        async static void Search()
        {
            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

            HttpResponseMessage response = await client.GetAsync(uri);

            string contentString = await response.Content.ReadAsStringAsync();
            Console.WriteLine(JsonPrettyPrint(contentString));
        }

        static void Main(string[] args)
        {
            Search();
            Console.ReadLine();
        }


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

**Odpowiedź**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

[Powrót do początku](#HOLTop)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek wyszukiwania usługi Bing jednostki](../tutorial-bing-entities-search-single-page-app.md)
> [Omówienie wyszukiwania usługi Bing jednostki](../search-the-web.md )
> [dokumentacja interfejsu API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
