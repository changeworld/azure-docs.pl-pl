---
title: 'Szybki start: zestaw Bing Video Search SDK, Node'
titleSuffix: Azure Cognitive Services
description: Konfiguracja aplikacji konsoli zestawu Bing Video Search SDK.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: rosh
ms.openlocfilehash: 9a2544fe5a3c0bac763b9aee79c36893ec56f351
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686691"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>Szybki start: zestaw Bing Video Search SDK na platformie Node

Zestaw Bing Video Search SDK używa funkcji interfejsu API REST na potrzeby zapytań i wyników analiz. 

[Kod źródłowy przykładów zestawu Bing Video Search SDK dla platformy Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) jest dostępny w usłudze Git Hub.

## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsolową przy użyciu zestawu SDK wyszukiwania wideo Bing:
* Uruchom polecenie `npm install ms-rest-azure` w środowisku programistycznym.
* Uruchom polecenie `npm install azure-cognitiveservices-videosearch` w środowisku programistycznym.

## <a name="video-search-client"></a>Klient wyszukiwania wideo
Pobierz [klucz dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze *Wyszukiwanie*. Utwórz wystąpienie elementu `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Następnie utwórz wystąpienie klienta:
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
Wyszukaj w wynikach.
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

## <a name="next-steps"></a>Następne kroki

[Przykłady zastosowania zestawu Cognitive Services SDK dla platformy Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
