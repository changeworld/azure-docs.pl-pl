---
title: 'Szybki start: Laboratorium Project Answer Search, Node'
description: Rozpocznij pracę z laboratorium Project Answer Search w środowisku Node.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 21dda323cf465f56fb3f43160dd04c27a81d5590
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698072"
---
# <a name="quickstart-project-answer-search-with-node"></a>Szybki start: Laboratorium Project Answer Search w środowisku Node

W poniższym przykładzie w środowisku Node tworzone jest zapytanie o informacje dotyczące Parku Narodowego Yosemite.

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz klucz dostępu dla bezpłatnej wersji próbnej zestawu [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-answer-search).

W tym przykładzie użyto środowiska Node w wersji 8.9.4.

## <a name="code-scenario"></a>Scenariusz kodu 

Zadaniem poniższego kodu jest uzyskiwanie odpowiedzi.
W celu zaimplementowania kodu wykonaj następujące kroki:
1. Zadeklaruj zmienne, aby określić punkt końcowy na podstawie hosta i ścieżki.
2. Określ adres URL zapytania, dla którego ma zostać wyświetlony podgląd, a następnie dodaj parametr zapytania.  
3. Utwórz funkcję procedury obsługi odpowiedzi.
4. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje nagłówek *Ocp-Apim-Subscription-Key*.
5. Uruchom funkcję wyszukiwania. 

Pełny kod tej wersji demonstracyjnej wygląda następująco:

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt= + mkt;

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
- [Przykładowy kod w języku C#](c-sharp-quickstart.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla języka Python](python-quickstart.md)
