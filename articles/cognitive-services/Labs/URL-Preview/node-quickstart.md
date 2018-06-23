---
title: Szybki Start node.js projektu adres URL podglądu - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Rozpoczynanie pracy przy użyciu adresu URL w wersji zapoznawczej w kognitywnych usług Microsoft Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 195033d2740b11873baae095cec028dc8d19ce49
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348625"
---
# <a name="url-preview-node-quickstart"></a>Adres URL podglądu węzła Szybki Start

W poniższym przykładzie węzła tworzy podgląd adres Url witryny sieci SwiftKey Web: https://swiftkey.com/en.

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz klucz dostępu dla bezpłatnej wersji próbnej [kognitywnych Labs usług](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scenariusz kodu 

Poniższy kod pobiera adres URL podglądu danych.
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
let subscriptionKey = 'YOUR-ACCESS-KEY'; 
let host = 'api.labs.cognitive.microsoft.com';
let path = '/urlpreview/v7.0/search';

let mkt = 'en-US';
let q = 'https://swiftkey.com/';

let params = '?q=' + encodeURI(q);

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
- [Przykład kodu C#](csharp.md)
- [Szybki Start Java](java-quickstart.md)
- [JavaScript — Szybki Start](javascript.md)
- [Krótkie wprowadzenie do języka Python](python-quickstart.md)