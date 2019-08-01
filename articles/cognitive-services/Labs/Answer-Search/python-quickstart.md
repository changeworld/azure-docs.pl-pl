---
title: 'Szybki start: Laboratorium Project Answer Search, Python'
titlesuffix: Azure Cognitive Services
description: Wprowadzenie do korzystania z laboratorium Project Answer Search na przykładzie języka Python.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: c0da596e84ac827b55affd5545c516e7623980f5
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698062"
---
# <a name="quickstart-project-answer-search-with-python"></a>Szybki start: laboratorium Project Answer Search w języku Python

Poniższy przykład w języku Python tworzy i wysyła żądanie dotyczące informacji na temat Skały Gibraltarskiej.

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz klucz dostępu dla bezpłatnej wersji próbnej zestawu [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-answer-search).

W tym przykładzie użyto języka Python 3.6.4

## <a name="code-scenario"></a>Scenariusz kodu 

Poniższy kod tworzy podgląd adresu URL.
W celu zaimplementowania kodu wykonaj następujące kroki:
1. Zadeklaruj zmienne, aby określić punkt końcowy na podstawie hosta i ścieżki.
2. Określ adres URL zapytania, dla którego ma zostać wyświetlony podgląd, a następnie dodaj parametr zapytania.  
3. Ustaw parametr zapytania.
4. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje nagłówek *Ocp-Apim-Subscription-Key*.
5. Ustaw nagłówek *Ocp-Apim-Subscription-Key*. 
6. Nawiąż połączenie i wyślij żądanie.
7. Wydrukuj wyniki w formacie JSON.

Pełny kod tej wersji demonstracyjnej wygląda następująco:

```
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

```
## <a name="next-steps"></a>Następne kroki
- [Przewodnik Szybki start dla języka C#](c-sharp-quickstart.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)
