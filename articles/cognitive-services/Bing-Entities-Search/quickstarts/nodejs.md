---
title: Szybki Start Node.JS dla usług Azure kognitywnych, wyszukiwania usługi Bing jednostki interfejsu API | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API wyszukiwania usługi Bing jednostki w kognitywnych usług Microsoft Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-jaswel
ms.openlocfilehash: 7396976453c240d5ea9a767f8c26ac96d10d1e1f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349184"
---
# <a name="quickstart-for-microsoft-bing-entity-search-api-with-nodejs"></a>Szybki Start Bing firmy Microsoft encji wyszukiwania interfejsu API za pomocą języka Node.JS 
<a name="HOLTop"></a>

W tym artykule przedstawiono sposób użycia [wyszukiwania usługi Bing jednostki](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) interfejsu API za pomocą języka Node.JS.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [Node.js 6](https://nodejs.org/en/download/) do uruchomienia tego kodu.

Musi mieć [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsu API Bing encji wyszukiwania**. [Bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) jest wystarczająca dla tego przewodnika Szybki Start. Należy klucz dostępu podany przy wywołaniu metody aktywacji bezpłatną wersję próbną lub może używać klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="search-entities"></a>Jednostek wyszukiwania

Aby uruchomić tę aplikację, wykonaj następujące kroki.

1. Utwórz nowy projekt programu Node.JS w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```nodejs
'use strict';

let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/entities';

let mkt = 'en-US';
let q = 'italian restaurant near me';

let params = '?mkt=' + mkt + '&q=' + encodeURI(q);

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
        console.log (body__);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + params,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

    let req = https.request (request_params, response_handler);
    req.end ();
}

Search ();
```

**Odpowiedź**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

[Powrót do początku](#HOLTop)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek wyszukiwania usługi Bing jednostki](../tutorial-bing-entities-search-single-page-app.md)
> [Omówienie wyszukiwania usługi Bing jednostki](../search-the-web.md )
> [dokumentacja interfejsu API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
