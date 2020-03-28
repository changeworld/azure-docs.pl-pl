---
title: Wideo Bing Szukaj w javascript biblioteki klienta Szybki start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: b642d981b79d13857881ec8cc5796e6365003ace
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289763"
---
Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości za pomocą biblioteki klienta wyszukiwania wideo Bing dla języka JavaScript. Usługa Bing Video Search ma interfejs API REST zgodny z większością języków programowania, ale biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Zawiera on więcej adnotacji i funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

- [Node.js](https://www.nodejs.org/)

Aby skonfigurować aplikację konsoli przy użyciu biblioteki klienta wyszukiwania wideo Bing:
* Uruchom polecenie `npm install ms-rest-azure` w środowisku programistycznym.
* Uruchom polecenie `npm install azure-cognitiveservices-videosearch` w środowisku programistycznym.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik JavaScript w ulubionym identyfikatorze `require()` IDE lub edytorze i dodaj `CognitiveServicesCredentials` instrukcję dla biblioteki klienta wyszukiwania wideo Bing i modułu. Utwórz zmienną dla klucza subskrypcji. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Utwórz wystąpienie obiektu `CognitiveServicesCredentials`, korzystając z klucza. Następnie użyj go do utworzenia wystąpienia klienta wyszukiwania wideo.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Wysyłanie żądania wyszukiwania

1. Użyj funkcji `client.videosOperations.search()`, aby wysłać żądanie wyszukiwania do interfejsu API wyszukiwania wideo Bing. Jeśli zostaną zwrócone wyniki wyszukiwania, użyj funkcji `.then()`, aby je zarejestrować.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie internetowej aplikacji jednostronicowej](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też 

* [Co to jest interfejs API wyszukiwania wideo Bing?](../../overview.md)
* [Przykłady zestawów SDK usług cognitive.NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)