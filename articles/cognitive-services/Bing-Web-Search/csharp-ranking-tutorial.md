---
title: Aby wyświetlić wyniki wyszukiwania przy użyciu rangę | Dokumentacja firmy Microsoft
description: Przedstawia sposób użycia odpowiedzi Bing RankingResponse, aby wyświetlić wyniki wyszukiwania według rangi.
services: cognitive-services
author: bradumbaugh
manager: bking
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 05/08/2017
ms.author: brumbaug
ms.openlocfilehash: ec47b8448c0c39cc54e4c79434ce7a2d926df341
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347577"
---
# <a name="build-a-console-app-search-client-in-c"></a>Tworzenie klienta konsoli aplikacji wyszukiwania w języku C#

Ten samouczek przedstawia sposób tworzenia prostej aplikacji konsoli .NET Core, który umożliwia użytkownikom zapytania interfejsu API wyszukiwania usługi Bing sieci Web i wyświetlić wyniki uporządkowanej według rangi.

Ten samouczek pokazuje, jak:

- Wprowadź prostego zapytania do interfejsu API wyszukiwania usługi Bing sieci Web
- Wyświetl wyniki zapytania w kolejności środkowa

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego samouczka, potrzebne są:

- Program Visual Studio. Jeśli nie masz, [Pobierz i zainstaluj bezpłatne Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
- Klucz subskrypcji dla interfejsu API wyszukiwania usługi Bing sieci Web. Jeśli nie masz, [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Utwórz nowy projekt aplikacji konsoli

W programie Visual Studio utwórz projekt za pomocą kombinacji `Ctrl` + `Shift` + `N`.

W **nowy projekt** okna dialogowego, kliknij przycisk **Visual C# > klasycznego pulpitu systemu Windows > Aplikacja konsoli (.NET Framework)**.

Nazwa aplikacji **MyConsoleSearchApp**, a następnie kliknij przycisk **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Dodaj pakiet Nuget struktury JSON.net do projektu

Struktury JSON.net umożliwia pracę z odpowiedzi JSON zwracane przez interfejs API. Dodawanie pakietu NuGet do projektu:

- W **Eksploratora rozwiązań** kliknij prawym przyciskiem myszy na projekt i wybierz **Zarządzaj pakietami NuGet...** . 
- Na **Przeglądaj** kartę, wyszukiwania dla `Newtonsoft.Json`. Wybierz najnowszą wersję, a następnie kliknij przycisk **zainstalować**. 
- Kliknij przycisk **OK** znajdującego się na **Przejrzyj zmiany** okna.
- Zamknij program Visual Studio kartę zatytułowany **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Dodaj odwołanie do System.Web

W tym samouczku polega na `System.Web` zestawu. Dodaj odwołanie do tego zestawu do projektu:

- W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **odwołania** i wybierz **Dodawanie odwołania...**
- Wybierz **zestawy > Framework**, następnie przewiń w dół i sprawdź **System.Web**
- Kliknij przycisk **OK**

## <a name="add-some-necessary-using-statements"></a>Dodaj niektóre niezbędne instrukcje using

Kod w tym samouczku wymaga trzy dodatkowe instrukcje using. Dodaj następujące instrukcje poniżej istniejące `using` instrukcje w górnej części **Program.cs**: 

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Monitowanie użytkownika o dla zapytania

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
- Wywołania `RunQueryAndDisplayResults(userQuery)` można wykonać zapytania i wyświetlać wyniki
- Oczekuje na dane wejściowe użytkownika, aby uniknąć natychmiast zamknięcia okna konsoli.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Wyszukaj wyników zapytania za pomocą interfejsu API wyszukiwania usługi Bing sieci Web

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

Tej metody:

- Tworzy `HttpClient` wykonać zapytania interfejsu API sieci Web wyszukiwania
- Ustawia `Ocp-Apim-Subscription-Key` nagłówka HTTP, który korzysta z usługi Bing uwierzytelniania żądania
- Wykonuje żądanie i używa struktury JSON.net do deserializacji wyników
- Wywołania `DisplayAllRankedResults(responseObjects)` Aby wyświetlić wszystkie wyniki w kolejności środkowa

Upewnij się, że wartość `Ocp-Apim-Subscription-Key` do klucza subskrypcji.

## <a name="display-ranked-results"></a>Uporządkowany wyników

Przed pokazaniem jak wyświetlić wyniki w kolejności uporządkowanej według rangi, Przyjrzyjmy się przykładowa odpowiedź wyszukiwania w sieci web: 

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

`rankingResponse` Obiekt JSON ([dokumentacji](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)) opisano kolejność wyświetlania odpowiednie wyniki wyszukiwania. Zawiera co najmniej jeden z następujących, priorytetową grup: 

- `pole`Wyniki wyszukiwania, aby uzyskać najbardziej widocznej traktowanie (na przykład wyświetlany powyżej połączeniach i paska bocznego).
- `mainline`Aby wyświetlić wyniki wyszukiwania w połączeniach.
- `sidebar`Aby wyświetlić wyniki wyszukiwania na pasku bocznym. Jeśli nie ma paska bocznego, Wyświetl wyniki poniżej połączeniach.

Odpowiedzi klasyfikacji JSON mogą zawierać jedną lub więcej grup.

W **Program.cs**, dodaj następującą metodę, aby wyświetlić wyniki w kolejności prawidłowo uporządkowanej według rangi:

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

- Pętli `rankingResponse` grup zawartych w odpowiedzi
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

Te metody współdziałają ze sobą w celu wyświetlenia wyników wyszukiwania do konsoli.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację. Dane wyjściowe powinny wyglądać podobnie do poniższej:

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