---
title: Aby wyświetlić wyniki wyszukiwania przy użyciu ranga
titleSuffix: Azure Cognitive Services
description: Pokazuje, jak używać odpowiedzi Bing RankingResponse do wyświetlania wyników wyszukiwania według rangi.
services: cognitive-services
author: bradumbaugh
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/08/2017
ms.author: bking
ms.openlocfilehash: 7c074d5d25453e2b2a1ddfc32422790235815f1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61431161"
---
# <a name="build-a-console-app-search-client-in-c"></a>Tworzenie klienta wyszukiwania aplikacji konsoli w języku C#

W tym samouczku przedstawiono sposób tworzenia prostej aplikacji konsoli .NET Core, umożliwiający użytkownikom zbadać API wyszukiwania w Internecie Bing i wyświetlić wyniki rangi.

Ten samouczek pokazuje, jak:

- Wprowadzać proste zapytanie API wyszukiwania w Internecie Bing
- Wyświetl wyniki zapytania w kolejności w rankingu

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z samouczka, potrzebne są:

- Program Visual Studio. Jeśli nie masz, [Pobierz i zainstaluj bezpłatny program Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
- Klucz subskrypcji API wyszukiwania w Internecie Bing. Jeśli jej nie masz, utwórz konto [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Utwórz nowy projekt aplikacji konsoli

W programie Visual Studio utwórz projekt za pomocą kombinacji `Ctrl` + `Shift` + `N`.

W **nowy projekt** okno dialogowe, kliknij przycisk **Visual C# > Windows Classic Desktop > Aplikacja Konsolowa (.NET Framework)**.

Nazwij aplikację **MyConsoleSearchApp**, a następnie kliknij przycisk **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Dodaj pakiet Nuget struktury JSON.net do projektu

Zezwala na składnik JSON.net do pracy z odpowiedziami JSON zwracane przez interfejs API. Dodawanie pakietu NuGet do projektu:

- W **Eksploratora rozwiązań** prawym przyciskiem myszy projekt i wybierz **Zarządzaj pakietami NuGet...** .
- Na **Przeglądaj** kartę, wyszukaj `Newtonsoft.Json`. Wybierz najnowszą wersję, a następnie kliknij przycisk **zainstalować**.
- Kliknij przycisk **OK** znajdujący się na **przeglądanie zmian** okna.
- Zamknij kartę programu Visual Studio o nazwie **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Dodaj odwołanie do System.Web

Ten samouczek opiera się na `System.Web` zestawu. Dodaj odwołanie do tego zestawu do projektu:

- W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **odwołania** i wybierz **Dodaj odwołanie...**
- Wybierz **zestawy > Framework**, następnie przewiń w dół i sprawdzanie **System.Web**
- Kliknij przycisk **OK**

## <a name="add-some-necessary-using-statements"></a>Dodaj niektóre niezbędne przy użyciu instrukcji

Kod w tym samouczku wymaga trzy dodatkowe za pomocą instrukcji. Dodaj następujące instrukcje poniżej istniejącego `using` instrukcji na górze **Program.cs**:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Poproś użytkownika dla zapytania

W **Eksploratora rozwiązań**, otwórz **Program.cs**. Aktualizacja `Main()` metody:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Tej metody:

- Monituje użytkownika o kwerendzie
- Wywołania `RunQueryAndDisplayResults(userQuery)` do wykonywania zapytania i wyświetlić wyniki
- Czeka na dane wejściowe użytkownika, aby zapobiec natychmiast zamyka okno konsoli.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Wyszukaj w wynikach zapytania przy użyciu interfejsu API wyszukiwania w sieci Web Bing

Następnie dodaj metodę, która wykonuje kwerendę interfejsu API i wyświetla wyniki:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Tej metody:

- Tworzy `HttpClient` do wykonywania zapytań interfejsu API wyszukiwania w sieci Web
- Zestawy `Ocp-Apim-Subscription-Key` nagłówka HTTP, który usługa Bing używa uwierzytelniania żądania
- Wykonuje żądanie i używa struktury JSON.net w celu wykonania deserializacji wyniki
- Wywołania `DisplayAllRankedResults(responseObjects)` Aby wyświetlić wszystkie wyniki w kolejności w rankingu

Upewnij się, że wartość `Ocp-Apim-Subscription-Key` klucza subskrypcji.

## <a name="display-ranked-results"></a>Wyświetl wyniki w rankingu

Przed wyświetleniem jak wyświetlać wyniki według rangi, spójrz na przykładowej odpowiedzi wyszukiwania w sieci web:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

`rankingResponse` Obiekt JSON ([dokumentacji](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)) opisuje kolejność wyświetlania odpowiednie wyniki wyszukiwania. Zawiera co najmniej jednej z poniższych, priorytetyzacji grup:

- `pole`: Wyniki wyszukiwania, aby uzyskać najbardziej widoczne traktowania (na przykład wyświetlane powyżej linii głównej i paska bocznego).
- `mainline`: Wyniki wyszukiwania w celu wyświetlenia w ramach linii głównej.
- `sidebar`: Wyniki wyszukiwania, aby wyświetlić na pasku bocznym. Jeśli nie ma paska bocznego, wyświetlić wyniki poniżej linii głównej.

Klasyfikacja odpowiedź JSON może obejmować jeden lub więcej grup.

W **Program.cs**, dodaj następującą metodę, aby wyświetlić wyniki w kolejności prawidłowo rangi:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Tej metody:

- Pętli `rankingResponse` grupach, które zawierają odpowiedzi
- Wyświetla elementy w każdej grupie przez wywołanie metody `DisplaySpecificResults(...)`

W **Program.cs**, Dodaj następujących dwóch metod:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Te metody współpracują w celu wyświetlenia wyników wyszukiwania do konsoli.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację. Dane wyjściowe powinny wyglądać mniej więcej tak:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej na temat [przy użyciu klasyfikacji, aby wyświetlić wyniki](rank-results.md).
