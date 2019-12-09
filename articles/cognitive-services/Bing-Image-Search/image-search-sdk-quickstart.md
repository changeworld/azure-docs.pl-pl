---
title: 'Szybki Start: Wyszukiwanie obrazów — zestaw SDK wyszukiwanie obrazów Bing dlaC#'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki start umożliwia wyszukanie pierwszego obrazu przy użyciu zestawu SDK wyszukiwania obrazów Bing, który jest otoką dla interfejsu API i zawiera te same funkcje. Ta prosta aplikacja w języku C# wysyła zapytanie dotyczące wyszukania obrazu, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszego zwróconego obrazu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 006f24c5b3bd3f30bc2e6fe48df08dd351d4a69c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930569"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-c"></a>Szybki start: wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing dla języka C#

Ten przewodnik Szybki start umożliwia wyszukanie pierwszego obrazu przy użyciu zestawu SDK wyszukiwania obrazów Bing, który jest otoką dla interfejsu API i zawiera te same funkcje. Ta prosta aplikacja w języku C# wysyła zapytanie dotyczące wyszukania obrazu, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszego zwróconego obrazu.

Kod źródłowy dla tego przykładu jest dostępny [w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) wraz z dodatkową obsługą błędów i adnotacjami.

## <a name="prerequisites"></a>Wymagania wstępne
* Dowolna wersja programu [Visual Studio 2017 lub nowszego](https://visualstudio.microsoft.com/vs/whatsnew/).
* [Pakiet NuGet wyszukiwania obrazów w usłudze Cognitive Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0).

Aby zainstalować zestaw SDK wyszukiwanie obrazów Bing w programie Visual Studio, użyj opcji **Zarządzaj pakietami NuGet** z **Eksplorator rozwiązań**.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

Najpierw utwórz nową aplikację konsoli języka C# w programie Visual Studio. Następnie dodaj do projektu poniższe pakiety.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

W metodzie głównej projektu utwórz zmienne dla swojego ważnego klucza subskrypcji, wyników obrazów do zwrócenia przez usługę Bing i terminu wyszukiwania. Następnie utwórz wystąpienie klienta wyszukiwania obrazów za pomocą klucza.

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";

//initialize the client
//NOTE: If you're using version 1.2.0 or below for the Bing Image Search SDK, 
// use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.

var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>Wysyłanie zapytania wyszukiwania przy użyciu klienta

Użyj klienta, aby wyszukać za pomocą tekstu zapytania:

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analizowanie i wyświetlanie wyniku pierwszego obrazu

Analizuj wyniki obrazów zwróconych w odpowiedzi.
Jeśli odpowiedź zawiera wyniki wyszukiwania, zapisz pierwszy wynik i wydrukuj jego szczegóły, takie jak adres URL miniatury i oryginalny adres URL, wraz z całkowitą liczbą zwróconych obrazów.  

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Zobacz także

* [Czym jest funkcja wyszukiwania obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj interaktywny pokaz online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Pobieranie bezpłatnego klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Przykłady dla zestawu Azure Cognitive Services SDK na platformie .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Dokumentacja usług Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
