---
title: 'Szybki start: Zestaw SDK wyszukiwania jednostek Bing, Node'
titleSuffix: Azure Cognitive Services
description: Konfigurowanie aplikacji konsolowej zestawu SDK wyszukiwania jednostek Bing za pomocą środowiska Node.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 0a61a6b1ada68307af7e7e574cba9910841f5939
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153015"
---
# <a name="quickstart-bing-entity-search-sdk-with-node"></a>Szybki start: Zestaw SDK wyszukiwania jednostek Bing dla platformy Node

Zestaw SDK wyszukiwania jednostek Bing zawiera funkcje interfejsu API REST na potrzeby wykonywania zapytań dotyczących jednostek i analizowania wyników. 

[Kod źródłowy przykładów zestawu Bing Entity Search SDK w języku C#](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) jest dostępny w witrynie Git Hub.
## <a name="application-dependencies"></a>Zależności aplikacji
Pobierz [klucz dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze **Wyszukiwanie**.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Aby skonfigurować aplikację konsolową przy użyciu zestawu SDK wyszukiwania jednostek Bing:
* Uruchom polecenie `npm install ms-rest-azure` w środowisku programistycznym.
* Uruchom polecenie `npm install azure-cognitiveservices-entitysearch` w środowisku programistycznym.

## <a name="entity-search-client"></a>Klient wyszukiwania jednostek
Pobierz [klucz dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze *Wyszukiwanie*. Utwórz wystąpienie elementu `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Następnie utwórz wystąpienie klienta i wyszukaj wyniki:
```
const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');

let entitySearchApiClient = new EntitySearchAPIClient(credentials);

entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
    console.log(result.queryContext);
    console.log(result.entities.value);
    console.log(result.entities.value[0].description);
}).catch((err) => {
    throw err;
});

```
Ten kod wyświetla elementy `result.value` w konsoli bez analizowania tekstu.  Wyniki, jeśli zostały zwrócone w danej kategorii, obejmują następujące elementy:
- _type: 'Thing'
- _type: 'ImageObject'

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Następne kroki

[Przykłady zastosowania zestawu Cognitive Services SDK dla platformy Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
