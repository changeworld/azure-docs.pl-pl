---
title: 'Szybki Start: Używanie wyszukiwania w Internecie Bing zestawu SDK dla platformy Node.js'
description: Instalator dla wyszukiwania w Internecie aplikację konsolową zestawu SDK.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 08/16/2018
ms.author: v-gedod, erhopf
ms.openlocfilehash: e25c295fc0fc144110325d3c494a513ea35aeb05
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888594"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>Szybki Start: Używanie wyszukiwania w Internecie Bing zestawu SDK dla platformy Node.js

Zestaw SDK wyszukiwania w sieci Web Bing zawiera funkcje interfejsu API REST dla sieci web kwerend i wyniki analizy.

[Kod dla przykładowych zestawach SDK wyszukiwania w sieci Web Bing węzeł źródłowy](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js) jest dostępna w witrynie GitHub.

## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania w sieci Web Bing, uruchom `npm install azure-cognitiveservices-websearch` w środowisku programistycznym.

## <a name="web-search-client"></a>Klient wyszukiwania w sieci Web
Pobierz [klucz subskrypcji usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze *wyszukiwania*. Utwórz wystąpienie obiektu `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Następnie utwórz wystąpienie klienta:
```
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```
Wyszukaj w wynikach:
```
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})

```
Ten kod wyświetla `result.value` elementy do konsoli bez analizowania tekstu.  Wyniki, jeśli według kategorii, obejmują:
- _typ: "ImageObject"
- _typ: "NewsArticle"
- _typ: "Strona sieci Web"
- _typ: "VideoObjectElementType"

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Kolejne kroki

[Przykłady zestawu SDK środowiska Node.js usługi cognitive services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
