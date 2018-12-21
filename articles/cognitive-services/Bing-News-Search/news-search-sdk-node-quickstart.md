---
title: 'Szybki start: Wyszukiwanie wiadomości — zestaw SDK wyszukiwania wiadomości Bing dla platformy Node.js'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wyszukać wiadomości przy użyciu zestawu SDK wyszukiwania wiadomości Bing dla platformy Node.js i przetworzyć odpowiedź.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 0fa78474d6400f890626859c32915c8657f92d4e
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249349"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Szybki start: Wyszukiwanie wiadomości przy użyciu zestawu SDK wyszukiwania wiadomości Bing dla platformy Node.js

Zestaw SDK wyszukiwania wiadomości Bing używa funkcji interfejsu API REST do wykonywania zapytań dotyczących wiadomości i analizowania wyników. 

[Kod źródłowy przykładów zestawu SDK wyszukiwania wiadomości Bing dla platformy Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) jest dostępny w serwisie Git Hub.

## <a name="application-dependencies"></a>Zależności aplikacji
Pobierz [klucz dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze **Wyszukiwanie**.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/). 

Aby skonfigurować aplikację konsolową przy użyciu zestawu SDK wyszukiwania wiadomości Bing:
* Uruchom polecenie `npm install ms-rest-azure` w środowisku programistycznym.
* Uruchom polecenie `npm install azure-cognitiveservices-newssearch` w środowisku programistycznym.

## <a name="news-search-client"></a>Klient wyszukiwania wiadomości
Pobierz [klucz dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze *Wyszukiwanie*. Utwórz wystąpienie elementu `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Następnie utwórz wystąpienie klienta:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Użyj klienta, aby przeprowadzić wyszukiwanie przy użyciu tekstu zapytania — w tym przykładzie jest to „Winter Olympics” (zimowe igrzyska olimpijskie):
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
Ten kod wyświetla elementy `result.value` w konsoli bez analizowania tekstu. Wyniki, jeśli zostały zwrócone w danej kategorii, obejmują następujące elementy:
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObject'
- _type: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Następne kroki

[Przykłady zastosowania zestawu Cognitive Services SDK dla platformy Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
