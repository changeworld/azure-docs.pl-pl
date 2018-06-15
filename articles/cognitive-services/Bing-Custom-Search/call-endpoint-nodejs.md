---
title: Punkt końcowy wywołanie za pomocą środowiska Node.js — wyszukiwanie niestandardowe Bing - Microsoft kognitywnych usług
description: Ta opcja szybkiego startu pokazano, jak żądania wyniki wyszukiwania z wystąpieniem niestandardowe wyszukiwanie przy użyciu środowiska Node.js do wywołania punktu końcowego niestandardowe wyszukiwania usługi Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 48fc234e15ce3b9172d766f6fae11b51a017ce70
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349772"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Wywołanie wyszukiwania usługi Bing niestandardowe punktu końcowego (Node.js)

Ta opcja szybkiego startu pokazano, jak żądania wyniki wyszukiwania z wystąpieniem niestandardowe wyszukiwanie przy użyciu środowiska Node.js do wywołania punktu końcowego niestandardowe wyszukiwania usługi Bing. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Wystąpienie wyszukiwania niestandardowego. Zobacz [Tworzenie pierwszego wystąpienia wyszukiwania usługi Bing niestandardowe](quick-start.md).

- [Node.js](https://www.nodejs.org/) zainstalowane.

-  [Konta usług interfejsu API kognitywnych](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsy API wyszukiwania usługi Bing**. [Bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) jest wystarczająca dla tego przewodnika Szybki Start. Należy klucz dostępu podany przy wywołaniu metody aktywacji bezpłatną wersję próbną lub może używać klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="run-the-code"></a>Uruchamianie kodu

Aby wywołać punktu końcowego wyszukiwania usługi Bing niestandardowe, wykonaj następujące kroki:

1. Utwórz folder dla kodu.
2. Z wiersza polecenia lub terminalu przejdź do folderu utworzonego.
3. Zainstaluj **żądania** modułu węzła:
    <pre>
    npm install request
    </pre>
4. Utwórz plik BingCustomSearch.js i skopiuj następujący kod do niego.
5. Zastąp **YOUR SUBSKRYPCJI klucza** i **YOUR-niestandardowe-CONFIG-ID** za pomocą Identyfikatora klucza i konfiguracji (zobacz krok 1).

    ``` javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    
    var options = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
          'q=' + searchTerm + 
          '&customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    
    request(options, function(error, response, body){
        var searchResponse = JSON.parse(body);
        for(var i = 0; i < searchResponse.webPages.value.length; ++i){
            var webPage = searchResponse.webPages.value[i];
            console.log('name: ' + webPage.name);
            console.log('url: ' + webPage.url);
            console.log('displayUrl: ' + webPage.displayUrl);
            console.log('snippet: ' + webPage.snippet);
            console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
            console.log();
        }
    })
    ```
6. Uruchom kod za pomocą następującego polecenia.
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie środowiska hostowanej interfejsu użytkownika](./hosted-ui.md)
- [Umożliwia znaczników decoration wyróżnianie tekstu](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)