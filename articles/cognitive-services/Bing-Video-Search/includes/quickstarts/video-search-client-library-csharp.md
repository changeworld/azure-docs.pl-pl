---
title: Szybki start biblioteki klienta wyszukiwania wideo w języku Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: d50e1acd104916d68f7fbb84ff568cf4efc0b46b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289756"
---
Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości za pomocą biblioteki klienta wyszukiwania wideo Bing dla języka C#. Usługa Bing Video Search ma interfejs API REST zgodny z większością języków programowania, ale biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy dla tego przykładu można znaleźć w [usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) z dodatkowymi adnotacjami i funkcjami.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja [programu Visual Studio 2017 lub nowszej](https://visualstudio.microsoft.com/downloads/).
* Json.NET framework, dostępny [jako pakiet NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Aby dodać bibliotekę klienta wyszukiwania wideo Bing do projektu, wybierz pozycję **Zarządzaj pakietami NuGet** z **Eksploratora rozwiązań** w programie Visual Studio. Dodaj pakiet `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

Zainstalowanie [[zestawu NuGet Video Search SDK]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0)spowoduje również zainstalowanie następujących zależności:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


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
> [Tworzenie internetowej aplikacji jednostronicowej](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też 

* [Co to jest interfejs API wyszukiwania wideo Bing?](../../overview.md)
* [Przykłady zestawów SDK usług cognitive.NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
