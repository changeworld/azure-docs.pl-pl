---
title: 'Szybki start: interfejs API wyszukiwania jednostek Bing, Python'
titlesuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API wyszukiwania jednostek Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: aahi
ms.openlocfilehash: 8650d241be5d3a34ae24b25c4e694792925ee300
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161824"
---
# <a name="quickstart-for-bing-entity-search-api-with-python"></a>Przewodnik Szybki start dotyczący korzystania z interfejsu API wyszukiwania jednostek Bing przy użyciu języka Python

W tym artykule przedstawiono sposób korzystania z interfejsu API [wyszukiwania jednostek Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) przy użyciu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten kod, potrzebne jest środowisko języka [Python 3.x](https://www.python.org/downloads/).

Trzeba mieć [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do **interfejsu API wyszukiwania jednostek Bing**. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) jest wystarczająca na potrzeby tego przewodnika Szybki start. Potrzebny jest klucz dostępu podany przy aktywacji bezpłatnej wersji próbnej lub klucz płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="search-entities"></a>Wyszukiwanie jednostek

Aby uruchomić tę aplikację, wykonaj następujące kroki.

1. Utwórz nowy projekt języka Python w Twoim ulubionym środowisku IDE.
2. Dodaj kod przedstawiony poniżej.
3. Zastąp wartość `key` kluczem dostępu właściwym dla Twojej subskrypcji.
4. Uruchom program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse
import json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/entities'

mkt = 'en-US'
query = 'italian restaurants near me'

params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)

def get_suggestions ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_suggestions ()
print (json.dumps(json.loads(result), indent=4))
```

**Odpowiedź**

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Bing Entity Search tutorial (Samouczek dotyczący wyszukiwania jednostek Bing)](../tutorial-bing-entities-search-single-page-app.md)
> [Omówienie wyszukiwania jednostek Bing](../search-the-web.md )
> [API Reference (Dokumentacja interfejsu API)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
