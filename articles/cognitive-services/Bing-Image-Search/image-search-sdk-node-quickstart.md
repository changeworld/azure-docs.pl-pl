---
title: Obraz wyszukiwania szybkiego startu węzła zestawu SDK | Dokumentacja firmy Microsoft
description: Instalator obraz wyszukiwania zestawu SDK aplikacji konsoli.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e4c8303e39accbb7caec15c0ef47d701971ce632
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349453"
---
# <a name="image-search-sdk-node-quickstart"></a>Obraz wyszukiwania węzła zestawu SDK — Szybki Start

Zestaw SDK wyszukiwania usługi Bing obraz zawiera funkcje interfejsu API REST dla zapytań obrazu oraz wyniki analizy. 

[Źródła kodu dla węzła Bing obraz wyszukiwania SDK przykłady](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) jest dostępna w Centrum Git.

## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania usługi Bing obrazu, uruchom `npm install azure-cognitiveservices-imagesearch` w środowisku projektowania.

## <a name="image-search-client"></a>Obraz wyszukiwania klienta
Pobierz [klucz dostępu usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) w obszarze *wyszukiwania*. Utwórz wystąpienie `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Następnie można utworzyć wystąpienia klienta:
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
Używanie klienta do wyszukiwania tekstem zapytania w tym przypadku "El Capitan":
```
client.imagesOperations.search('El Capitan', function (err, result, request, response) {
    if (err) throw err;
    console.log(result.value);
});

```
<!-- Need to sanitize result
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'ImageObjectElementType'

![Imageresults](media/node-sdk-quickstart-image-results.png)
-->

## <a name="next-steps"></a>Kolejne kroki

[Usługi kognitywnych przykłady Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)