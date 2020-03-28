---
title: 'Szybki start: wywoływanie niestandardowego punktu końcowego wyszukiwania Bing przy użyciu pliku Node.js | Dokumenty firmy Microsoft'
titleSuffix: Azure Cognitive Services
description: Użyj tego przewodnika Szybki start, aby rozpocząć żądanie wyników z wystąpienia wyszukiwania niestandardowego Bing w języku Node.js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 1c7bd97de4e46e1c8da467840006fe2520851caf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238868"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Szybki start: wywoływanie niestandardowego punktu końcowego wyszukiwania Bing przy użyciu pliku Node.js

Użyj tego przewodnika Szybki start, aby rozpocząć żądanie wyników z wystąpienia wyszukiwania niestandardowego Bing. Chociaż ta aplikacja jest napisana w języku JavaScript, interfejs API wyszukiwania niestandardowego Bing jest usługą internetową RESTful zgodną z większością języków programowania. Kod źródłowy dla tego przykładu jest dostępny w [usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Zobacz [Szybki start: tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing, aby](quick-start.md) uzyskać więcej informacji.

- [Node.js](https://www.nodejs.org/)

- [Biblioteka żądań języka JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik JavaScript w swoim ulubionym środowisku IDE lub edytorze i dodaj instrukcję `require()` dla biblioteki żądań. Utwórz zmienne dla swojego klucza subskrypcji, identyfikator konfiguracji niestandardowej i termin wyszukiwania. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Wysyłanie i odbieranie żądania wyszukiwania 

1. Utwórz zmienną do przechowywania informacji wysyłanych w żądaniu. Skonstruuj adres URL żądania, dodając termin wyszukiwania do parametru zapytania `q=` oraz identyfikator konfiguracji niestandardowej wystąpienia wyszukiwania do parametru `customconfig=`. Oddziel parametry za pomocą znaku `&`. Można użyć globalnego punktu końcowego poniżej lub niestandardowego punktu końcowego [poddomeny](../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlanego w witrynie Azure portal dla zasobu.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Biblioteka żądań JavaScript umożliwia wysłanie żądania wyszukiwania do Twojego wystąpienia wyszukiwania niestandardowego Bing i wyświetlenie informacji o wynikach, łącznie z nazwą wyszukiwania, jego adresem URL i datą ostatniego przeszukiwania strony internetowej.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](./tutorials/custom-search-web-page.md)
