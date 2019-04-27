---
title: 'Szybki start: Wysyłanie żądania wyszukiwania za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka Node.js'
titleSuffix: Azure Cognitive Services
description: Użyj tego poradnika Szybki start, aby wyszukiwać jednostki za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka Node.js
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 015a2d344b066bd7b65c3228a2795c3395793f2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737044"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Szybki start: Wysyłanie żądania wyszukiwania za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka Node.js

Użyj tego poradnika Szybki start, aby zacząć wyszukiwać jednostki za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka Node.js. Mimo że wyszukiwanie jednostek Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe zintegrowanie tej usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja środowiska [Node.js](https://nodejs.org/en/download/).

* [Zestaw SDK wyszukiwania jednostek Bing dla platformy Node.js](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Aby zainstalować zestaw SDK wyszukiwania jednostek Bing:

1. Uruchom polecenie `npm install ms-rest-azure` w środowisku programistycznym.
2. Uruchom polecenie `npm install azure-cognitiveservices-entitysearch` w środowisku programistycznym.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku JavaScript w ulubionym środowisku IDE lub edytorze i dodaj następujące wymagania. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Utwórz wystąpienie obiektu `CognitiveServicesCredentials` przy użyciu klucza subskrypcji. Następnie za pomocą tego obiektu utwórz wystąpienie klienta wyszukiwania.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Wysyłanie żądania i odbieranie odpowiedzi

1. Wyślij żądanie wyszukiwania jednostek za pomocą funkcji `entitiesOperations.search()`. Po otrzymaniu odpowiedzi wyświetl obiekt `queryContext`, liczbę zwróconych wyników i opis pierwszego wyniku.
      
    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../overview.md )