---
title: 'Szybki start: Wyszukiwanie wiadomości — zestaw SDK wyszukiwania wiadomości Bing dla platformy Node.js'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wyszukać wiadomości przy użyciu zestawu SDK wyszukiwania wiadomości Bing dla platformy Node.js i przetworzyć odpowiedź.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 8f45e0921748092af4702e03d557e52153a78ec0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258666"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Szybki start: Wyszukiwanie wiadomości przy użyciu zestawu SDK wyszukiwania wiadomości Bing dla platformy Node.js

Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości za pomocą zestawu SDK wyszukiwania wiadomości Bing dla platformy Node.js. Mimo że wyszukiwanie wiadomości Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe zintegrowanie tej usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/)

Aby skonfigurować aplikację konsolową przy użyciu zestawu SDK wyszukiwania wiadomości Bing:
1. Uruchom polecenie `npm install ms-rest-azure` w środowisku programistycznym.
2. Uruchom polecenie `npm install azure-cognitiveservices-newssearch` w środowisku programistycznym.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz wystąpienie elementu `CognitiveServicesCredentials`. Utwórz zmienne dla klucza subskrypcji i terminu wyszukiwania.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. Utwórz wystąpienie klienta:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Wysyłanie zapytania wyszukiwania

3. Użyj klienta, aby przeprowadzić wyszukiwanie przy użyciu terminu zapytania — w tym przykładzie jest to „Winter Olympics” (zimowe igrzyska olimpijskie):
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

Ten kod wyświetla elementy `result.value` w konsoli bez analizowania tekstu. Wyniki, jeśli zostały zwrócone w danej kategorii, obejmują następujące elementy:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
[Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-news-search-single-page-app.md)
