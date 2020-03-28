---
title: Szybki start biblioteki klienta wyszukiwania jednostek Bing w języku C
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 39a6c21ad056980e8c7b146e36a6e185cb3ed95e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136781"
---
Ten przewodnik Szybki start służy do wyszukiwania jednostek z biblioteką klienta wyszukiwania jednostek Bing dla języka C#. Usługa Bing Entity Search ma interfejs API REST zgodny z większością języków programowania, ale biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja [programu Visual Studio 2017 lub nowszej](https://www.visualstudio.com/downloads/).
* Struktura [Json.NET](https://www.newtonsoft.com/json) dostępna jako pakiet NuGet.
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).
* [Pakiet zestawu SDK wyszukiwania wiadomości Bing dla narzędzia NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Zainstalowanie tego pakietu powoduje także zainstalowanie następujących elementów:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Aby dodać bibliotekę klienta wyszukiwania jednostek Bing do projektu programu Visual Studio, użyj `Microsoft.Azure.CognitiveServices.Search.EntitySearch` opcji **Zarządzaj pakietami NuGet** z **Eksploratora rozwiązań**i dodaj pakiet.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowe rozwiązanie konsolowe dla języka C# w programie Visual Studio. Dodaj następujący kod do głównego pliku kodu.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Tworzenie klienta i wysyłanie żądania wyszukiwania

1. Utwórz nowego klienta wyszukiwania. Dodaj klucz subskrypcji, tworząc nowy obiekt `ApiKeyServiceClientCredentials`.

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. Użyj funkcji `Entities.Search()` klienta w celu wyszukania zapytania:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Pobieranie i wyświetlanie opisu jednostki

1. Jeśli interfejs API zwrócił wyniki wyszukiwania, pobierz jednostkę główną z obiektu `entityData`.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Wyświetlanie opisu jednostki głównej 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji sieci Web](../../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../../overview.md )