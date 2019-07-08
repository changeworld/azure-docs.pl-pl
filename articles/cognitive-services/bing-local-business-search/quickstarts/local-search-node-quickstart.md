---
title: Przewodnik Szybki Start — Wyślij zapytanie do lokalnych firm interfejs API wyszukiwania Bing przy użyciu środowiska Node.js | Dokumentacja firmy Microsoft
titleSuffix: Azure Cognitive Services
description: Rozpocznij korzystanie z lokalnych firm interfejsu API wyszukiwania Bing w węźle.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 07b238f900a7f0ba15ad7b37ba7e194567fa0f6a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592710"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Szybki start: Wysłanie zapytania do lokalnych firm interfejs API wyszukiwania Bing przy użyciu środowiska Node.js

Użyj tego przewodnika Szybki Start, aby rozpocząć wysyłanie żądań lokalnych firm interfejs API wyszukiwania Bing, czyli usługi Azure Cognitive Service. Gdy ta prosta aplikacja, jest napisany w języku Node.js, interfejs API jest zgodny z dowolnego języka programowania możliwością wysyłania żądań HTTP i analizowania danych JSON Usługa sieci Web typu RESTful.

Ta przykładowa aplikacja pobiera dane lokalne odpowiedzi z interfejsu API dla zapytania wyszukiwania `hotel in Bellevue`.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja środowiska [Node.js](https://nodejs.org/en/download/).

* [Biblioteka żądań języka JavaScript](https://github.com/request/request)

Konieczne jest posiadanie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) za pomocą interfejsów API usługi Bing. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca na potrzeby tego przewodnika Szybki start. Użyj klucza dostępu, dostarczone przez bezpłatnej wersji próbnej.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Scenariusz kodu

Poniższy kod pobiera definiuje i wysyła żądanie. W celu zaimplementowania kodu wykonaj następujące kroki:

1. Zadeklaruj zmienne, aby określić punkt końcowy na podstawie hosta i ścieżki.
2. Określ zapytanie, a następnie dodaj parametr zapytania.
3. Utwórz funkcję procedury obsługi odpowiedzi.
4. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje nagłówek Ocp-Apim-Subscription-Key.
5. Uruchom funkcję wyszukiwania.

Pełny kod tej wersji demonstracyjnej wygląda następująco:

```
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

## <a name="next-steps"></a>Kolejne kroki

* [Lokalne wyszukiwanie firm Szybki Start](local-quickstart.md)
* [Lokalnych firm wyszukiwania Java Szybki Start](local-search-java-quickstart.md)
* [Lokalnych firm wyszukiwania Python Szybki Start](local-search-python-quickstart.md)
