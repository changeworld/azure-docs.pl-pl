---
title: 'Szybki Start: wyszukiwanie filmów wideo przy użyciu zestawu C# SDK dla programu-wyszukiwanie wideo Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wysyłać żądania wyszukiwania wideo za pomocą zestawu SDK wyszukiwania wideo Bing dla języka C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 1cbb5880c38aca649dffb91f31b9340648b85f68
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75382513"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Szybki Start: wykonywanie wyszukiwania wideo przy użyciu zestawu SDK wyszukiwanie wideo Bing dlaC#

Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości za pomocą zestawu SDK wyszukiwania wiadomości Bing dla języka C#. Chociaż wyszukiwanie wideo Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe integrowanie usługi z Twoimi aplikacjami. Kod źródłowy dla tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) z dodatkowymi adnotacjami i funkcjami.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2017 lub nowszego](https://visualstudio.microsoft.com/downloads/).
* Struktura [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) dostępna jako pakiet NuGet.

Aby dodać zestaw wyszukiwanie wideo Bing SDK do projektu, wybierz pozycję **Zarządzaj pakietami NuGet** z **Eksplorator rozwiązań** w programie Visual Studio. Dodaj pakiet `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie internetowej aplikacji jednostronicowej](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także 

* [Co to jest interfejs API wyszukiwania wideo Bing?](../overview.md)
* [Cognitive services .NET SDK samples (Przykłady zestawów SKD .NET usług Cognitive Services)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
