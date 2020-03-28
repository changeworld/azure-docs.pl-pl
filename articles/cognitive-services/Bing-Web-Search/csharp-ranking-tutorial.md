---
title: Używanie rangi do wyświetlania wyników wyszukiwania
titleSuffix: Azure Cognitive Services
description: Pokazuje, jak używać odpowiedzi Bing RankingResponse do wyświetlania wyników wyszukiwania w kolejności rangi.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 1c8e0bb136fddeb84dc991e63a761378b38cc470
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382331"
---
# <a name="build-a-console-app-search-client-in-c"></a>Tworzenie klienta wyszukiwania aplikacji konsoli w języku C #

W tym samouczku pokazano, jak utworzyć prostą aplikację konsoli .NET Core, która umożliwia użytkownikom wykonywanie zapytań w interfejsie API wyszukiwania w sieci Web bing i wyświetlanie wyników rankingowych.

W tym samouczku pokazano, jak:

- Tworzenie prostego zapytania do interfejsu API wyszukiwania w sieci Web usługi Bing
- Wyświetlanie wyników kwerendy w kolejności rankingowej

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać wraz z samouczka, trzeba:

- Program Visual Studio. Jeśli go nie masz, [pobierz i zainstaluj bezpłatną wersję visual studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
- Klucz subskrypcji interfejsu API wyszukiwania sieci Web usługi Bing. Jeśli jej nie masz, utwórz konto [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Tworzenie nowego projektu aplikacji konsoli

W programie Visual Studio utwórz projekt za pomocą kombinacji `Ctrl`+`Shift`+`N`.

W oknie dialogowym **Nowy projekt** kliknij pozycję **Visual C# > Aplikacji > konsoli Windows Classic Desktop (.NET Framework).**

Nazwij aplikację **MyConsoleSearchApp**, a następnie kliknij przycisk **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Dodawanie pakietu JSON.net Nuget do projektu

JSON.net umożliwia pracę z odpowiedziami JSON zwracanych przez interfejs API. Dodaj swój pakiet NuGet do projektu:

- W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Zarządzaj pakietami NuGet...**.
- Na karcie **Przeglądaj** wyszukaj hasło `Newtonsoft.Json`. Wybierz najnowszą wersję, a następnie kliknij przycisk **Zainstaluj**.
- Kliknij przycisk **OK** w oknie **Przeglądaj zmiany.**
- Zamknij kartę programu Visual Studio pod tytułem **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Dodawanie odwołania do pliku System.Web

Ten samouczek `System.Web` opiera się na zestawie. Dodaj odwołanie do tego zestawu do projektu:

- W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **odwołania** i wybierz pozycję **Dodaj odwołanie...**
- Wybierz **zestawy > Framework**, a następnie przewiń w dół i zaznacz pozycję **System.Web**
- Wybierz **przycisk OK**

## <a name="add-some-necessary-using-statements"></a>Dodawanie niektórych niezbędnych instrukcji

Kod w tym samouczku wymaga trzech dodatkowych instrukcji przy użyciu. Dodaj te instrukcje `using` poniżej istniejących instrukcji w górnej części **Program.cs:**

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Poproś użytkownika o zapytanie

W **Eksploratorze rozwiązań**otwórz **Program.cs**. Zaktualizuj `Main()` metodę:

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

Ta metoda:

- Prosi użytkownika o kwerendę
- Wywołania, `RunQueryAndDisplayResults(userQuery)` aby wykonać kwerendę i wyświetlić wyniki
- Czeka na dane wejściowe użytkownika, aby zapobiec natychmiastowemu zamknięciu okna konsoli.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Wyszukiwanie wyników kwerend przy użyciu interfejsu API wyszukiwania w sieci Web usługi Bing

Następnie dodaj metodę, która wysyła zapytanie do interfejsu API i wyświetla wyniki:

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

Ta metoda:

- Tworzy `HttpClient` interfejs API wyszukiwania w sieci Web
- Ustawia `Ocp-Apim-Subscription-Key` nagłówek HTTP, którego bing używa do uwierzytelniania żądania
- Wykonuje żądanie i używa JSON.net do deserializacji wyników
- Wywołania, `DisplayAllRankedResults(responseObjects)` aby wyświetlić wszystkie wyniki w kolejności rankingowej

Upewnij się, że `Ocp-Apim-Subscription-Key` ustawisz wartość klucza subskrypcji.

## <a name="display-ranked-results"></a>Wyświetlanie wyników rankingowych

Zanim pokażesz, jak wyświetlić wyniki w kolejności rankingowej, zapoznaj się z przykładową odpowiedzią na wyszukiwanie w internecie:

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

Obiekt `rankingResponse` JSON ([dokumentacja](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) opisuje odpowiednią kolejność wyświetlania wyników wyszukiwania. Obejmuje ona co najmniej jedną z następujących grup priorytetowych:

- `pole`: Wyniki wyszukiwania, aby uzyskać najbardziej widoczne traktowanie (na przykład wyświetlane nad główną i boczną).
- `mainline`: Wyniki wyszukiwania wyświetlane w linii głównej.
- `sidebar`: Wyniki wyszukiwania wyświetlane na pasku bocznym. Jeśli nie ma paska bocznego, wyświetl wyniki poniżej linii głównej.

Odpowiedź rankingu JSON może zawierać jedną lub więcej grup.

W **Program.cs**dodaj następującą metodę wyświetlania wyników w prawidłowej kolejności rankingowej:

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

Ta metoda:

- Pętle nad `rankingResponse` grupami, które zawiera odpowiedź
- Wyświetla elementy w każdej grupie przez wywołanie`DisplaySpecificResults(...)`

W **Program.cs**dodać dwie następujące metody:

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

Te metody współpracują ze sobą, aby wyprowadzić wyniki wyszukiwania do konsoli.

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

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [używaniu rankingu do wyświetlania wyników](rank-results.md).
