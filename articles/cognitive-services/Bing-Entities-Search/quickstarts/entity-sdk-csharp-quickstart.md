---
title: 'Szybki start: Wyszukiwanie jednostek za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka C#'
titleSuffix: Azure Cognitive Services
description: Użyj tego poradnika Szybki start, aby wyszukiwać jednostki za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka C#.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 0390ef5b9364aaa54c4d46abf3164728b3a4126a
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595818"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>Wysyłanie żądania wyszukiwania za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka C#

Użyj tego poradnika Szybki start, aby zacząć wyszukiwać jednostki za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka C#. Mimo że wyszukiwanie jednostek Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe zintegrowanie tej usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Wymagania wstępne

* Dowolnej wersji programu [programu Visual Studio 2017 r. lub nowszej](https://www.visualstudio.com/downloads/).
* Struktura [Json.NET](https://www.newtonsoft.com/json) dostępna jako pakiet NuGet.
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).
* [Pakiet zestawu SDK wyszukiwania wiadomości Bing dla narzędzia NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Zainstalowanie tego pakietu powoduje także zainstalowanie następujących elementów:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Aby dodać zestaw SDK wyszukiwania jednostek Bing do projektu programu Visual Studio, należy użyć **Zarządzaj pakietami NuGet** opcję **Eksploratora rozwiązań**i Dodaj `Microsoft.Azure.CognitiveServices.Search.EntitySearch` pakietu.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

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
    var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../overview.md )