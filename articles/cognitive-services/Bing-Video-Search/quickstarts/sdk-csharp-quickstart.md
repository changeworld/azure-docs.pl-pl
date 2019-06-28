---
title: 'Szybki start: Wyszukiwanie wideo za pomocą zestawu SDK wyszukiwania wideo Bing dla języka C#'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wysyłać żądania wyszukiwania wideo za pomocą zestawu SDK wyszukiwania wideo Bing dla języka C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 3673f18ff38b2ae98180f470b9f76f1fc57ee8b6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442534"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Szybki start: Wyszukiwanie wideo za pomocą zestawu SDK wyszukiwania wideo Bing dla języka C#

Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości za pomocą zestawu SDK wyszukiwania wiadomości Bing dla języka C#. Chociaż wyszukiwanie wideo Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe integrowanie usługi z Twoimi aplikacjami. Kod źródłowy dla tego przykładu znajduje się na [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) dodatkowych adnotacji i funkcje.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolnej wersji programu [programu Visual Studio 2017 r. lub nowszej](https://visualstudio.microsoft.com/downloads/).
* Struktura [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) dostępna jako pakiet NuGet.

Aby dodać zestaw SDK wyszukiwania wideo Bing do projektu, wybierz **Zarządzaj pakietami NuGet** z **Eksploratora rozwiązań** w programie Visual Studio. Dodaj pakiet `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

Zainstalowanie [[zestawu NuGet Video Search SDK]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0)spowoduje również zainstalowanie następujących zależności:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowe rozwiązanie konsolowe dla języka C# w programie Visual Studio. Dodaj następujący kod do głównego pliku kodu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Utwórz wystąpienie klienta, tworząc nowy obiekt `ApiKeyServiceClientCredentials` za pomocą klucza subskrypcji, a następnie wywołaj konstruktor.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Wyślij żądanie wyszukiwania i przetwórz wyniki

1. Użyj klienta do wysłania żądania wyszukiwania. Użyj elementu „SwiftKey” na potrzeby zapytania wyszukiwania.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Jeśli zostały zwrócone jakiekolwiek wyniki, pobierz pierwszy z nich przy użyciu funkcji `videoResults.Value[0]`. Następnie wyświetl identyfikator, tytuł i adres URL wideo.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie internetowej aplikacji jednostronicowej](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także 

* [Co to jest interfejs API wyszukiwania wideo Bing?](../overview.md)
* [Cognitive services .NET SDK samples (Przykłady zestawów SKD .NET usług Cognitive Services)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
