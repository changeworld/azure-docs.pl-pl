---
title: Jednostka wyszukiwania szybkiego startu węzła zestawu SDK | Dokumentacja firmy Microsoft
description: Instalator encji wyszukiwania zestawu SDK aplikacji konsoli.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 2904ecfed33334458f9b6a9ca2500cd0bfef13bc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349776"
---
# <a name="entity-search-sdk-node-quickstart"></a>Jednostka wyszukiwania węzła zestawu SDK — Szybki Start

Zestaw SDK usługi Bing jednostek wyszukiwania zawiera funkcje interfejsu API REST dla zapytań jednostki oraz wyniki analizy. 

[Źródła kodu dla C# Bing encji wyszukiwania zestawu SDK przykłady](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) jest dostępna w Centrum Git.
## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania usługi Bing jednostki, uruchom `npm install azure-cognitiveservices-entitysearch` w środowisku projektowania.

## <a name="entity-search-client"></a>Jednostka wyszukiwania klienta
Pobierz [klucz dostępu usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) w obszarze *wyszukiwania*. Utwórz wystąpienie `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Następnie utwórz wystąpienie klienta i wyszukaj wyników:
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
Wyświetla kod `result.value` elementy do konsoli bez podczas analizowania tekstu.  Wyniki, jeśli występują dla każdej kategorii, obejmują:
- _wprowadź: "ID"
- _wprowadź: "ImageObject"

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Kolejne kroki

[Usługi kognitywnych przykłady Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)