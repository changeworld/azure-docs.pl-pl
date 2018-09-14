---
title: 'Szybki Start: Wyszukiwanie wysyłania zapytań przy użyciu interfejsu API wyszukiwania obrazów Bing oraz'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start umożliwia wyszukiwanie i znajdowanie obrazów w sieci web za pomocą API wyszukiwania w Internecie Bing.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/07/2018
ms.author: aahi
ms.openlocfilehash: 22eb54f6adec0335dd89a5ae906117542bb11717
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580416"
---
# <a name="quickstart-send-search-queries-using-the-bing-image-search-api-and-c"></a>Szybki Start: Wysyłanie zapytania przy użyciu interfejsu API wyszukiwania obrazów Bing i języka C#

Ten przewodnik Szybki Start umożliwia utworzenie pierwszego wywołania do interfejsu API wyszukiwania obrazów Bing i wyświetlić wynik wyszukiwania z odpowiedź w formacie JSON. Ta prosta aplikacja C# wysyła zapytanie wyszukiwania obrazów HTTP do interfejsu API i wyświetla adres URL pierwszy obraz zwracane.

Podczas tej aplikacji został napisany w języku C#, interfejs API jest zgodny z większość języków programowania usługi sieci Web typu RESTful.

Kod źródłowy dla tego przykładu jest dostępny [w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingImageSearchv7Quickstart.cs) obsługi dodatkowych błędów i adnotacje kodu.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolnej wersji programu [programu Visual Studio 2017](https://www.visualstudio.com/downloads/).
* [Json.NET](https://www.newtonsoft.com/json) framework dostępne jako pakiet NuGet. 
* Jeśli używasz systemu Linux/MacOS można uruchomić tę aplikację przy użyciu [Mono](http://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowe rozwiązanie konsoli o nazwie `BingSearchApisQuickStart` w programie Visual Studio. Następnie należy dodać następujące przestrzenie nazw do pliku głównego kodu.

    ```csharp
    using System;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    using Newtonsoft.Json.Linq;
    ```

2. Utwórz zmienne dla punktu końcowego interfejsu API, klucz subskrypcji i szukanego terminu.

    ```csharp
    //...
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        // Replace the this string with your valid access key.
        const string subscriptionKey = "enter your key here";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";
        const string searchTerm = "tropical ocean";
    //...
    ```

## <a name="create-a-struct-to-format-the-bing-image-search-response"></a>Tworzenie struktury formatu odpowiedzi wyszukiwania obrazów Bing

Zdefiniuj `SearchResult` struktury zawierają wyników wyszukiwania obrazów i informacje o nagłówku JSON.
    
```csharp
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        //...
            struct SearchResult
            {
                public String jsonResult;
                public Dictionary<String, String> relevantHeaders;
            }
//...
```

## <a name="create-a-method-to-send-search-requests"></a>Utwórz metodę, aby wysyłać żądania wyszukiwania

Utwórz metodę o nazwie `BingImageSearch` wykonywania wywołań interfejsu API i ustaw zwracany typ `SearchResult` struktury utworzone wcześniej.

```csharp
//...
namespace BingSearchApisQuickstart
{
    //...
    class Program
    {
        //...
        static SearchResult BingImageSearch(string searchTerm)
        {
        }
//...
```

## <a name="create-and-handle-an-image-search-request"></a>Utworzyć i obsługiwać żądania wyszukiwania obrazów
 
W `BingImageSearch` metody, wykonaj następujące kroki.

1. Należy utworzyć identyfikator URI żądania wyszukiwania. Należy pamiętać, że wyszukiwany termin `toSearch` musi być sformatowany przed jest dołączany do ciągu. 

    ```csharp
    static SearchResult BingImageSearch(string toSearch){

        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. Wykonaj żądania sieci web i uzyskuj odpowiedzi jako ciąg JSON.

    ```csharp
    WebRequest request = WebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = subscriptionKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    ```

3. Utwórz obiekt wyniku wyszukiwania, a następnie Wyodrębnij nagłówków Bing HTTP. Następnie wróć `searchResult`.

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

## <a name="process-and-view-the-response"></a>Proces i wyświetlanie odpowiedzi

1. W metodzie głównej, należy wywołać `BingImageSearch()` i Przechowaj odpowiedź zwrócona. Następnie deserializuje dane JSON na obiekt.

    ```csharp
    SearchResult result = BingImageSearch(searchTerm);
    //deserialize the JSON response from the Bing Image Search API
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result.jsonResult);
    ```

2. Pobierz pierwsze obraz zwrócony z `jsonObj`oraz wydrukować tytuł i adres URL obrazu. 
    ```csharp
    var firstJsonObj = jsonObj["value"][0];
    Console.WriteLine("Title for the first image result: " + firstJsonObj["name"]+"\n");
    //After running the application, copy the output URL into a browser to see the image. 
    Console.WriteLine("URL for the first image result: " + firstJsonObj["webSearchUrl"]+"\n");
    ```  
       
3. Upewnij się usunąć swój klucz subskrypcji z kodu aplikacji.

## <a name="json-response"></a>Odpowiedź w formacie JSON

Odpowiedzi z interfejsu API wyszukiwania obrazów Bing są zwracane w formacie JSON. Ta przykładowa odpowiedź została obcięta do Pokaż jeden wynik.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący aplikacji jednostronicowej wyszukiwania obrazów Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także 

* [Co to jest wyszukiwanie obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj prezentację online dla interaktywne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Uzyskaj bezpłatne klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentacja usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)