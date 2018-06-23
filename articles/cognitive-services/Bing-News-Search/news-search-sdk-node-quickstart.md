---
title: Szybki Start węzła zestawu SDK wyszukiwania wiadomości | Dokumentacja firmy Microsoft
description: Konfigurowanie aplikacji konsoli SDK wyszukiwania wiadomości
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 4ae99aa100b697a0dd75863c6f0c3c556dfa3d21
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349473"
---
# <a name="news-search-sdk-node-quickstart"></a>Szybki Start węzła zestawu SDK wyszukiwania wiadomości

Zestaw SDK wyszukiwania usługi Bing wiadomości zawiera funkcje interfejsu API REST dla zapytań wiadomości oraz wyniki analizy. 

[Kod dla przykładów SDK wyszukiwania wiadomości Bing węzeł źródłowy](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) jest dostępna w Centrum Git.

## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK Search wiadomości Bing, uruchom `npm install azure-cognitiveservices-newssearch` w środowisku projektowania.

## <a name="news-search-client"></a>Klient wyszukiwania wiadomości
Pobierz [klucz dostępu usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) w obszarze *wyszukiwania*. Utwórz wystąpienie `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Następnie można utworzyć wystąpienia klienta:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Używanie klienta do wyszukiwania tekstem zapytania w tym przypadku "Zima Olimpiada":
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
Wyświetla kod `result.value` elementy do konsoli bez podczas analizowania tekstu. Wyniki, jeśli występują dla każdej kategorii, obejmują:
- _wprowadź: "NewsArticle"
- _wprowadź: "Strona sieci Web"
- _wprowadź: "VideoObject"
- _wprowadź: "ImageObject"

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Kolejne kroki

[Usługi kognitywnych przykłady Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
