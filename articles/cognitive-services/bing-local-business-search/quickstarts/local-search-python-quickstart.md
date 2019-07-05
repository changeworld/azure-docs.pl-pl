---
title: Przewodnik Szybki Start — Wyślij zapytanie do lokalnych firm interfejs API wyszukiwania Bing w języku Python | Dokumentacja firmy Microsoft
titleSuffix: Azure Cognitive Services
description: Użyj w tym artykule, aby rozpocząć korzystanie z lokalnych firm interfejsu API wyszukiwania Bing w języku Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: cfc4bd95214c56dfbe940b7a2785d297f59105a9
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592725"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Szybki start: Wysłanie zapytania do lokalnych firm interfejs API wyszukiwania Bing w języku Python

Użyj tego przewodnika Szybki Start, aby rozpocząć wysyłanie żądań lokalnych firm interfejs API wyszukiwania Bing, czyli usługi Azure Cognitive Service. Gdy ta prosta aplikacja został napisany w języku Python, interfejs API jest zgodny z dowolnego języka programowania możliwością wysyłania żądań HTTP i analizowania danych JSON Usługa sieci Web typu RESTful.

Ta przykładowa aplikacja pobiera dane lokalne odpowiedzi z interfejsu API dla zapytania wyszukiwania `hotel in Bellevue`.

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko [Python](https://www.python.org/) 2.x lub 3.x
 
Konieczne jest posiadanie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) za pomocą interfejsów API usługi Bing. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca na potrzeby tego przewodnika Szybki start. Użyj klucza dostępu, dostarczone przez bezpłatnej wersji próbnej.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Uruchom kompletnej aplikacji

Poniższy kod pobiera zlokalizowanych wyników. W celu zaimplementowania kodu wykonaj następujące kroki:
1. Zadeklaruj zmienne, aby określić punkt końcowy na podstawie hosta i ścieżki.
2. Określ parametr zapytania. 
3. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje nagłówek Ocp-Apim-Subscription-Key.
4. Ustaw nagłówek Ocp-Apim-Subscription-Key. 
5. Nawiązywanie połączeń i wysyłania żądania.
6. Wydrukuj wyniki w formacie JSON.

Pełny kod tej wersji demonstracyjnej wygląda następująco:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

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

## <a name="next-steps"></a>Kolejne kroki
- [Szybki Start Java wyszukiwania lokalnych firmach](local-search-java-quickstart.md)
- [Wyszukiwanie lokalnych firmach C# Szybki Start](local-quickstart.md)
- [Szybki Start węzła wyszukiwania lokalnych firmach](local-search-node-quickstart.md)
