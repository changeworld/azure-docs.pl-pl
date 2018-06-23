---
title: Węzeł typu Szybki Start dla usług kognitywnych firmy Microsoft, wyszukiwanie odpowiedzi projektu | Dokumentacja firmy Microsoft
description: Rozpoczynanie pracy przy użyciu projektu odpowiedzi wyszukiwania, kognitywnych usług Microsoft Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 36b2709d39230aae7929164ba4c9306f57043b43
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348600"
---
# <a name="project-answer-search-node-quickstart"></a>Szybki Start odpowiedzi wyszukiwania węzła projektu

W poniższym przykładzie węzła tworzy zapytanie informacji o Yosemite National wstrzymywanie.

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz klucz dostępu dla bezpłatnej wersji próbnej [kognitywnych Labs usług](https://aka.ms/answersearchsubscription)

W tym przykładzie użyto v8.9.4 węzła

## <a name="code-scenario"></a>Scenariusz kodu 

Poniższy kod pobiera odpowiedzi.
Jest stosowana w poniższych krokach:
1. Zadeklaruj zmienne, aby określić punkt końcowy przez hosta i ścieżkę.
2. Podaj adres URL zapytania, aby wyświetlić podgląd i dodać parametr zapytania.  
3. Utwórz funkcję obsługi dla odpowiedzi.
4. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje *Ocp-Apim-subskrypcji — klucz* nagłówka.
5. Uruchom funkcję wyszukiwania. 

Kompletny kod dla tego pokazu następująco:

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt=en-us';

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

````

## <a name="next-steps"></a>Kolejne kroki
- [Przykład kodu C#](c-sharp-quickstart.md)
- [Szybki Start Java](java-quickstart.md)
- [Krótkie wprowadzenie do języka Python](python-quickstart.md)