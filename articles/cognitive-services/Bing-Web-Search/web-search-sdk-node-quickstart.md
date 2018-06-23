---
title: Wyszukiwanie w sieci Web — Szybki Start węzła zestawu SDK | Dokumentacja firmy Microsoft
description: Instalator sieci Web wyszukiwania zestawu SDK aplikacji konsoli.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 44f7f97f6c442df3fbb1e5e08189b8db7d4b9db0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349484"
---
# <a name="web-search-sdk-node-quickstart"></a>Wyszukiwanie zestawu SDK węzła sieci Web — Szybki Start

Zestaw SDK wyszukiwania usługi Bing sieci Web zawiera funkcje interfejsu API REST dla zapytań sieci web oraz wyniki analizy.

[Kod dla przykładów SDK wyszukiwania w sieci Web Bing węzeł źródłowy](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js) jest dostępna w Centrum Git.

## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania usługi Bing sieci Web, należy uruchomić `npm install azure-cognitiveservices-websearch` w środowisku projektowania.

## <a name="web-search-client"></a>Klient sieci Web wyszukiwania
Pobierz [klucz dostępu usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) w obszarze *wyszukiwania*. Utwórz wystąpienie `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Następnie można utworzyć wystąpienia klienta:
```
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```
Wyszukaj wyników:
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
Wyświetla kod `result.value` elementy do konsoli bez podczas analizowania tekstu.  Wyniki, jeśli występują dla każdej kategorii, obejmują:
- _wprowadź: "ImageObject"
- _wprowadź: "NewsArticle"
- _wprowadź: "Strona sieci Web"
- _wprowadź: "VideoObjectElementType"

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Kolejne kroki

[Usługi kognitywnych przykłady Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
