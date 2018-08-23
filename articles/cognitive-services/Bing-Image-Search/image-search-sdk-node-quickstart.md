---
title: 'Szybki Start: Żądania i Filtruj obrazów przy użyciu zestawu SDK w środowisku Node.js'
description: W tym przewodniku Szybki Start, żądania i Filtruj obrazy zwrócone przez wyszukiwanie obrazów Bing, przy użyciu środowiska Node.js.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e88c045b220192a617e6b8caf5d8d53f70a25b5e
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "41994387"
---
# <a name="quickstart-request-and-filter-images-using-the-sdk-and-nodejs"></a>Szybki Start: Żądania i filtrować obrazów za pomocą zestawu SDK i środowiska Node.js

Zestaw SDK wyszukiwania obrazów Bing zawiera funkcje interfejsu API REST dla obrazu kwerend i wyniki analizy. 

[Kod dla przykładowych zestawach SDK wyszukiwania obrazów Bing węzeł źródłowy](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) jest dostępny w repozytorium.

## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania obrazów Bing, uruchom `npm install azure-cognitiveservices-imagesearch` w środowisku programistycznym.

## <a name="image-search-client"></a>Obraz wyszukiwania klienta
Pobierz [klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze *wyszukiwania*. Utwórz wystąpienie obiektu `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Następnie utwórz wystąpienie klienta:
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
Użyj klienta do wyszukiwania tekstem zapytania w tym przypadku "El Capitan":
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

[Przykłady zestawu SDK środowiska Node.js usługi cognitive services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)