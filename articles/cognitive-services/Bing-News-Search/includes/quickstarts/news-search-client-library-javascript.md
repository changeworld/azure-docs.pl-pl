---
title: Szybki start biblioteki klienta JavaScript wyszukiwania w u usługi Bing News
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 858e6b9e0e40ab988a4cdf04b31580c1ca28d40a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503895"
---
Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości w bibliotece klienta wyszukiwania wiadomości Bing dla języka JavaScript. Usługa Bing News Search ma interfejs API REST zgodny z większością języków programowania, ale biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/)

Aby skonfigurować aplikację konsoli przy użyciu biblioteki klienta wyszukiwania wiadomości Bing:
1. Uruchom polecenie `npm install ms-rest-azure` w środowisku programistycznym.
2. Uruchom polecenie `npm install azure-cognitiveservices-newssearch` w środowisku programistycznym.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz wystąpienie elementu `CognitiveServicesCredentials`. Utwórz zmienne dla klucza subskrypcji i wyszukiwanego hasła.

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji sieci Web](../../tutorial-bing-news-search-single-page-app.md)
