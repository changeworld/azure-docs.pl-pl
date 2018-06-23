---
title: Szybki Start języka Python dla usług kognitywnych firmy Microsoft, wyszukiwanie odpowiedzi projektu | Dokumentacja firmy Microsoft
description: Przykład Python rozpocząć korzystanie z projektu odpowiedzi wyszukiwania, kognitywnych usług Microsoft Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9cb5406c616ed8e96d73c00c788a0d20f66dcabd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348572"
---
# <a name="project-answer-search-python-quickstart"></a>Szybki Start odpowiedzi wyszukiwania Python projektu

W poniższym przykładzie Python tworzy i wysyła żądanie informacji o "Skale Gibraltarze".

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz klucz dostępu dla bezpłatnej wersji próbnej [kognitywnych Labs usług](https://aka.ms/answersearchsubscription)

W tym przykładzie użyto Python 3.6.4

## <a name="code-scenario"></a>Scenariusz kodu 

Poniższy kod tworzy Podgląd adresu URL.
Jest stosowana w poniższych krokach:
1. Zadeklaruj zmienne, aby określić punkt końcowy przez hosta i ścieżkę.
2. Podaj adres URL zapytania, aby wyświetlić podgląd i dodać parametr zapytania.  
3. Ustaw dla parametru zapytania.
4. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje *Ocp-Apim-subskrypcji — klucz* nagłówka.
5. Ustaw *Ocp-Apim-subskrypcji — klucz* nagłówka. 
6. Nawiązywania połączeń i wysłać żądanie.
7. Drukowanie wyników JSON.

Kompletny kod dla tego pokazu następująco:

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

````
## <a name="next-steps"></a>Kolejne kroki
- [C# — Szybki Start](c-sharp-quickstart.md)
- [Szybki Start Java](java-quickstart.md)
- [Węzeł Szybki Start](node-quickstart.md)