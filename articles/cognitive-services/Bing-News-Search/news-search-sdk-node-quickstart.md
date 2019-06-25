---
title: 'Szybki start: Wyszukiwanie wiadomości dla środowiska Node.js przy użyciu zestawu SDK wyszukiwania wiadomości Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wyszukać wiadomości przy użyciu zestawu SDK wyszukiwania wiadomości Bing dla platformy Node.js i przetworzyć odpowiedź.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c71d76e93eb1a121163d40fe61dcd5a8546d63f8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203356"
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

1. Utwórz wystąpienie elementu `CognitiveServicesCredentials`. Utwórz zmienne dla swój klucz subskrypcji i termin wyszukiwania.

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

1. Użyj klienta, aby przeprowadzić wyszukiwanie przy użyciu terminu zapytania — w tym przykładzie jest to „Winter Olympics” (zimowe igrzyska olimpijskie):
    
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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-news-search-single-page-app.md)
