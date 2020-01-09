---
title: Szybki Start — wysyłanie zapytania do interfejsu API w języku Python — wyszukiwanie lokalne firmy Bing
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z interfejsu API wyszukiwania lokalnego firmy Bing w języku Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: c7e7ef7f052fccfea18b246f41109d5fa7528b4b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379747"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Szybki Start: wysyłanie zapytania do lokalnego interfejsu API wyszukiwania biznesowego Bing w języku Python

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wysyłanie żądań do lokalnego interfejsu API wyszukiwania biznesowego Bing, który jest usługą poznawczej platformy Azure. Chociaż ta prosta aplikacja jest zapisywana w języku Python, interfejs API jest usługą sieci Web RESTful zgodną z dowolnym językiem programowania, który umożliwia wykonywanie żądań HTTP i analizowanie kodu JSON.

Ta przykładowa aplikacja pobiera dane lokalnego odpowiedzi z interfejsu API dla zapytania wyszukiwania `hotel in Bellevue`.

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko [Python](https://www.python.org/) 2.x lub 3.x
 
Musisz mieć [konto interfejsu API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z interfejsami API Bing. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca na potrzeby tego przewodnika Szybki start. Użyj klucza dostępu dostarczonego w ramach bezpłatnej wersji próbnej.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Uruchom kompletną aplikację

Poniższy kod pobiera zlokalizowane wyniki. W celu zaimplementowania kodu wykonaj następujące kroki:
1. Zadeklaruj zmienne, aby określić punkt końcowy na podstawie hosta i ścieżki.
2. Określ parametr zapytania. 
3. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje nagłówek OCP-APIM-Subscription-Key.
4. Ustaw dla nagłówka OCP-APIM-Subscription-Key. 
5. Utwórz połączenie i Wyślij żądanie.
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
- [Lokalne wyszukiwanie biznesowe w języku Java — Szybki Start](local-search-java-quickstart.md)
- [Lokalne wyszukiwanie C# biznesowe — Szybki Start](local-quickstart.md)
- [Lokalny węzeł wyszukiwania biznesowego — Szybki Start](local-search-node-quickstart.md)
