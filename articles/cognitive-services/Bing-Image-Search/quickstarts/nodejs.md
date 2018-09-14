---
title: 'Szybki Start: Wysyłania zapytania przy użyciu interfejsu API wyszukiwania obrazów Bing przy użyciu środowiska Node.js'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start umożliwia wyszukiwanie i znajdowanie obrazów w sieci web za pomocą API wyszukiwania w Internecie Bing.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: 43f0cfec6aa2d4263b6a627736c2a432b2943145
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576650"
---
# <a name="quickstart-send-search-queries-using-the-bing-image-search-rest-api-and-nodejs"></a>Szybki Start: Wysyłanie zapytania przy użyciu interfejsu API REST wyszukiwania obrazów Bing i Node.js

Ten przewodnik Szybki Start umożliwia utworzenie pierwszego wywołania do interfejsu API wyszukiwania obrazów Bing i odbierać odpowiedź w formacie JSON. Ta prosta aplikacja JavaScript wysyła zapytanie wyszukiwania do interfejsu API i wyświetli nieprzetworzone wyniki.

Podczas tej aplikacji jest napisany w języku JavaScript i działa w środowisku Node.js, interfejs API jest RESTful sieci Web usługi zgodne większość języków programowania.

Kod źródłowy dla tego przykładu jest dostępny [w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) obsługi dodatkowych błędów i adnotacje kodu.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowszą wersję [Node.js](https://nodejs.org/en/download/).

* [Biblioteki JavaScript żądania](https://github.com/request/request)
[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i Inicjowanie aplikacji

1. Utwórz nowy plik JavaScript w Twoim ulubionym środowiskiem IDE lub edytora, a następnie ustaw wymagania dotyczące poziom ścisłości i https.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Utwórz zmienne dla punktu końcowego interfejsu API, ścieżki wyszukiwania interfejs API obrazów, klucz subskrypcji i szukanego terminu.
    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Konstruowania żądania wyszukiwania i zapytań.

1. Używać zmiennych w ostatnim kroku, aby sformatować adres URL żądania interfejsu API wyszukiwania. Należy pamiętać, że termin wyszukiwania musi być zakodowane w adresie URL przed wysłaniem do interfejsu API.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. Biblioteka żądania służy do wysyłania zapytania do interfejsu API. `response_handler` zostanie zdefiniowany w następnej sekcji.
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Obsługa i przeanalizować odpowiedzi

1. Zdefiniuj funkcję o nazwie `response_handler` przyjmującej wywołanie HTTP `response`, jako parametr. w ramach tej funkcji wykonaj następujące czynności:
    
    1. Definiowanie zmiennej, by zawierała treści odpowiedzi JSON.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Store Zamorska odpowiedzi podczas **danych** flaga jest wywoływana. 
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Gdy **zakończenia** zgłaszane jest flaga, za pomocą pliku JSON, które mogą być przetwarzane i adres URL obrazu może zostać zrealizowane, wraz z całkowitą liczbę obrazów zwrócony.
    
        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="json-response"></a>Odpowiedź w formacie JSON

Odpowiedzi z interfejsu API wyszukiwania obrazów Bing są zwracane w formacie JSON. Ta przykładowa odpowiedź została obcięta do Pokaż jeden wynik.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący aplikacji jednostronicowej wyszukiwania obrazów Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także 

* [Co to jest wyszukiwanie obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj prezentację online dla interaktywne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Uzyskaj bezpłatne klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentacja usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)