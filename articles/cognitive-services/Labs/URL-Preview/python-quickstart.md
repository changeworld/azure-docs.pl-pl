---
title: Szybki Start Python projektu adres URL podglądu - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Przykład skryptu, aby szybko rozpocząć korzystanie z podglądu adres URL projektu w kognitywnych usług Microsoft Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 78b2d83b02aa9ea32509029c7456e04e420b8572
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348628"
---
# <a name="url-preview-python-quickstart"></a>Adres URL podglądu Python — Szybki Start

W poniższym przykładzie Python tworzy podgląd adres Url witryny sieci SwiftKey Web: https://swiftkey.com/en.

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz klucz dostępu dla bezpłatnej wersji próbnej [kognitywnych Labs usług](https://aka.ms/answersearchsubscription)

W tym przykładzie użyto 3,6 Python.

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
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
````
## <a name="next-steps"></a>Kolejne kroki
- [C# — Szybki Start](csharp.md)
- [Szybki Start Java](java-quickstart.md)
- [JavaScript — Szybki Start](javascript.md)
- [Adres URL węzła — Szybki Start](node-quickstart.md)