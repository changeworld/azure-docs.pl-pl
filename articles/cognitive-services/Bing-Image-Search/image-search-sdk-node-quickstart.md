---
title: 'Szybki Start: Wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing i środowiska Node.js'
description: W tym przewodniku Szybki Start, żądania i Filtruj obrazy zwrócone przez wyszukiwanie obrazów Bing, przy użyciu środowiska Node.js.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cagronlund
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: ebe6629344c076119c0bfdaee76a69df6274ca28
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45572587"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-nodejs"></a>Szybki Start: Wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing i środowiska Node.js

Ten przewodnik Szybki Start umożliwia wyszukiwanie pierwsze obraz przy użyciu zestawu SDK wyszukiwania obrazów Bing, w jest otoką dla interfejsu API, która zawiera te same funkcje. Ta prosta aplikacja JavaScript wysyła kwerendę wyszukiwania obrazów, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszy obraz zwracane.

Kod źródłowy, w tym przykładzie jest dostępny na [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) za pomocą obsługi dodatkowych błędów i adnotacji.

## <a name="prerequisites"></a>Wymagania wstępne

* [Usług Cognitive Services obraz wyszukiwania zestawu SDK dla środowiska Node.js](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * Instalowanie przy użyciu `npm install azure-cognitiveservices-imagesearch`
* [Node.js Azure Rest](https://www.npmjs.com/package/ms-rest-azure) modułu
    * Instalowanie przy użyciu `npm install ms-rest-azure`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i Inicjowanie aplikacji

1. Utwórz nowy plik JavaScript w Twoim ulubionym środowiskiem IDE lub edytora i ustawić poziom ścisłości, https i inne wymagania.

    ```javascript
    'use strict';
    https = require('https');
    const Search = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. W metodzie głównej projektu tworzenie zmiennych dla tego klucza ważnej subskrypcji, wyniki obraz ma zostać zwrócona, Bing i termin wyszukiwania. Następnie utwórz wystąpienie klienta wyszukiwania obrazów za pomocą klucza.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request 
    let searchTerm = "canadian rockies";

    //instantiate the the image search client 
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new Search.ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Tworzenie funkcji pomocnika asynchroniczne

1. Tworzenie funkcji do wywołania klienta asynchronicznie, a następnie Zwróć odpowiedź z usługi wyszukiwania obrazów Bing.  
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
## <a name="send-a-query-and-handle-the-response"></a>Wysłanie zapytania i obsługiwać odpowiedzi 

1. Wywołaj funkcję pomocnika i obsługują jego `promise` analizować wyniki obraz zwrócona w odpowiedzi.

    Jeśli odpowiedź zawiera wyniki wyszukiwania, przechowywania pierwszego wyniku i wydrukować jej szczegóły, takie jak miniatura URL, oryginalny adres URL wraz z całkowitą liczbą zwrócił obrazów.  
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image. 
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`); 
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący aplikacji jednostronicowej wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Zobacz także 

* [Co to jest wyszukiwanie obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj prezentację online dla interaktywne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Uzyskaj bezpłatne klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api) 
* [Przykłady dla platformy node.js dla zestawu SDK usługi Azure Cognitive Services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
* [Dokumentacja usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)