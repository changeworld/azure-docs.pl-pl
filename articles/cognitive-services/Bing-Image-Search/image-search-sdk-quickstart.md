---
title: 'Szybki Start: Wyszukiwanie obrazów przy użyciu zestawu SDK wyszukiwania obrazów Bing i języka C#'
description: Ten przewodnik Szybki Start umożliwia wyszukiwanie i znajdowanie obrazów w sieci web przy użyciu zestawu SDK wyszukiwania obrazów Bing i języka C#.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 9e0decb29224b5ad684e1242b8f93e091e08e6b6
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579254"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-c"></a>Szybki Start: Wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing i języka C#

Ten przewodnik Szybki Start umożliwia wyszukiwanie pierwsze obraz przy użyciu zestawu SDK wyszukiwania obrazów Bing, w jest otoką dla interfejsu API, która zawiera te same funkcje. Ta prosta aplikacja C# wysyła kwerendę wyszukiwania obrazów, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszy obraz zwracane.

Kod źródłowy dla tego przykładu jest dostępny [w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) za pomocą obsługi dodatkowych błędów i adnotacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolnej wersji programu [programu Visual Studio 2017](https://visualstudio.microsoft.com/vs/whatsnew/).
* [Pakietu NuGet wyszukiwania w usłudze Cognitive obraz](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0).

Aby zainstalować zestaw SDK wyszukiwania obrazów Bing w programie visual studio, użyj `Manage NuGet Packages` opcji z poziomu Eksploratora rozwiązań w programie Visual Studio.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i Inicjowanie aplikacji

Najpierw utwórz nową aplikację konsoli C# w programie Visual Studio. Następnie należy dodać następujące pakiety do projektu.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

W metodzie głównej projektu tworzenie zmiennych dla tego klucza ważnej subskrypcji, wyniki obraz ma zostać zwrócona, Bing i termin wyszukiwania. Następnie utwórz wystąpienie klienta wyszukiwania obrazów za pomocą klucza.

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";
//initialize the client
var client = new ImageSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>Wyślij zapytanie wyszukiwania przy użyciu klienta

Użyj klienta, aby wyszukać tekst zapytania:

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analizowanie i wyświetlenia wyniku pierwsze obraz

Analizowanie wyników obraz zwrócona w odpowiedzi.
Jeśli odpowiedź zawiera wyniki wyszukiwania, przechowywania pierwszego wyniku i wydrukować jej szczegóły, takie jak miniatura URL, oryginalny adres URL wraz z całkowitą liczbą zwrócił obrazów.  

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący aplikacji jednostronicowej wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Zobacz także 

* [Co to jest wyszukiwanie obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj prezentację online dla interaktywne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Uzyskaj bezpłatne klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Przykłady dla usługi Azure Cognitive Services SDK dla platformy .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Dokumentacja usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)