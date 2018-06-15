---
title: Wideo węzła zestawu SDK wyszukiwania Szybki Start | Dokumentacja firmy Microsoft
description: Ustawienia dla zestawu SDK Search wideo aplikacji konsoli.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 5718c750288e0a5605db3296d2911cca5e03375c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349784"
---
# <a name="video-search-sdk-node-quickstart"></a>Wideo węzła zestawu SDK wyszukiwania Szybki Start

Zestaw SDK usługi Bing wideo Search zawiera funkcje interfejsu API REST dla zapytań wideo oraz wyniki analizy. 

[Kod dla przykładów SDK wyszukiwania wideo Bing węzeł źródłowy](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) jest dostępna w Centrum Git.

## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania usługi Bing wideo, uruchom `npm install azure-cognitiveservices-videosearch` w środowisku projektowania.

## <a name="video-search-client"></a>Wideo klienta wyszukiwania
Pobierz [klucz dostępu usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) w obszarze *wyszukiwania*. Utwórz wystąpienie `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Następnie można utworzyć wystąpienia klienta:
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
Wyniki wyszukiwania.
```
client.videosOperations.search('Interstellar Trailer').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Kolejne kroki

[Usługi kognitywnych przykłady Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
