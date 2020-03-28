---
title: Szybki start — wysyłanie kwerendy do interfejsu API w języku Python — wyszukiwanie lokalne firmy Bing
titleSuffix: Azure Cognitive Services
description: Użyj tego przewodnika Szybki start, aby rozpocząć korzystanie z interfejsu API wyszukiwania lokalnego firmy Bing w języku Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: c7e7ef7f052fccfea18b246f41109d5fa7528b4b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379747"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Szybki start: wysyłanie kwerendy do interfejsu API wyszukiwania lokalnego firmy Bing w języku Python

Ten przewodnik Szybki start służy do rozpoczynania wysyłania żądań do interfejsu API wyszukiwania lokalnego firmy Bing, który jest usługą Azure Cognitive Service. Podczas gdy ta prosta aplikacja jest napisana w języku Python, INTERFEJS API jest usługą sieci Web RESTful kompatybilną z dowolnym językiem programowania zdolnym do wysyłania żądań HTTP i analizowania JSON.

Ta przykładowa aplikacja pobiera dane odpowiedzi lokalnej `hotel in Bellevue`z interfejsu API dla kwerendy wyszukiwania .

## <a name="prerequisites"></a>Wymagania wstępne

* [Python](https://www.python.org/) 2.x lub 3.x
 
Musisz mieć [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z interfejsami API usługi Bing. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca na potrzeby tego przewodnika Szybki start. Użyj klucza dostępu dostarczonego przez bezpłatną wersję próbną.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Uruchamianie kompletnej aplikacji

Poniższy kod pobiera zlokalizowane wyniki. W celu zaimplementowania kodu wykonaj następujące kroki:
1. Zadeklaruj zmienne, aby określić punkt końcowy na podstawie hosta i ścieżki.
2. Określ parametr kwerendy. 
3. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje nagłówek Ocp-Apim-Subscription-Key.
4. Ustaw nagłówek Ocp-Apim-Subscription-Key. 
5. Nawiązaj połączenie i wyślij żądanie.
6. Wydrukuj wyniki w formacie JSON.

Pełny kod tej wersji demonstracyjnej wygląda następująco:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Następne kroki
- [Szybki start wyszukiwania w firmie lokalnej](local-search-java-quickstart.md)
- [Szybki start wyszukiwania dla firm lokalnych](local-quickstart.md)
- [Szybki start lokalnego węzła wyszukiwania firm](local-search-node-quickstart.md)
