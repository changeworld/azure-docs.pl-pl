---
title: Szybki start — wysyłanie kwerendy do interfejsu API przy użyciu pliku Node.js — wyszukiwanie lokalne firmy Bing
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki start służy do rozpoczynania wysyłania żądań do interfejsu API wyszukiwania lokalnego firmy Bing, który jest usługą Azure Cognitive Service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: beab594126ce292ea1fc47e399a12274dbb31aa3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74665699"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Szybki start: wysyłanie kwerendy do interfejsu API wyszukiwania lokalnego firmy Bing przy użyciu pliku Node.js

Ten przewodnik Szybki start służy do rozpoczynania wysyłania żądań do interfejsu API wyszukiwania lokalnego firmy Bing, który jest usługą Azure Cognitive Service. Podczas gdy ta prosta aplikacja jest napisana w node.js, interfejs API jest usługą sieci Web RESTful kompatybilną z dowolnym językiem programowania zdolnym do tworzenia żądań HTTP i analizowania JSON.

Ta przykładowa aplikacja pobiera dane odpowiedzi lokalnej `hotel in Bellevue`z interfejsu API dla kwerendy wyszukiwania .

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja środowiska [Node.js](https://nodejs.org/en/download/).

* [Biblioteka żądań języka JavaScript](https://github.com/request/request)

Musisz mieć [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z interfejsami API usługi Bing. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca na potrzeby tego przewodnika Szybki start. Użyj klucza dostępu dostarczonego przez bezpłatną wersję próbną.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Scenariusz kodu

Poniższy kod pobiera definiuje i wysyła żądanie. W celu zaimplementowania kodu wykonaj następujące kroki:

1. Zadeklaruj zmienne, aby określić punkt końcowy na podstawie hosta i ścieżki.
2. Określ kwerendę i dodaj parametr kwerendy.
3. Utwórz funkcję procedury obsługi odpowiedzi.
4. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje nagłówek Ocp-Apim-Subscription-Key.
5. Uruchom funkcję wyszukiwania.

Pełny kod tej wersji demonstracyjnej wygląda następująco:

```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Następne kroki

* [Szybki start wyszukiwania lokalnych firm](local-quickstart.md)
* [Przewodnik Szybki start java wyszukiwania lokalnego firmy](local-search-java-quickstart.md)
* [Szybki start wyszukiwania dla firm lokalnych](local-search-python-quickstart.md)
