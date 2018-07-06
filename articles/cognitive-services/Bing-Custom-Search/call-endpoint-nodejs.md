---
title: Wywoływanie punktu końcowego za pomocą środowiska Node.js — wyszukiwanie niestandardowe Bing — Microsoft Cognitive Services
description: Ten przewodnik Szybki Start pokazano, jak żądanie wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego przy użyciu środowiska Node.js, aby wywołać punkt końcowy wyszukiwania niestandardowego Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 5d9391cc486dc868a1a291ccc7095291cddd3e4c
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858463"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Wywołanie punktu końcowego usługi Bing Custom Search (Node.js)

Ten przewodnik Szybki Start pokazano, jak żądanie wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego przy użyciu środowiska Node.js, aby wywołać punkt końcowy wyszukiwania niestandardowego Bing. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Wystąpienie wyszukiwania niestandardowego. Zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](quick-start.md).

- [Node.js](https://www.nodejs.org/) zainstalowane.

-  [Konto interfejsu API usług cognitive](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsy API wyszukiwania Bing**. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) jest wystarczająca na potrzeby tego przewodnika Szybki Start. Wymagany jest klucz dostępu podany przy wywołaniu metody Aktywuj bezpłatną wersję próbną lub klucz płatnej subskrypcji może używać z pulpitu nawigacyjnego platformy Azure.

## <a name="run-the-code"></a>Uruchamianie kodu

Aby wywołać punkt końcowy wyszukiwania niestandardowego Bing, wykonaj następujące kroki:

1. Utwórz folder dla kodu.

2. W wierszu polecenia lub terminalu przejdź do folderu, który został utworzony.

3. Zainstaluj **żądania** modułu węzła:
    <pre>
    npm install request
    </pre>
    
4. Utwórz plik BingCustomSearch.js i skopiuj następujący kod do niego.

5. Zastąp **YOUR-SUBSCRIPTION-KEY** i **YOUR-CUSTOM-CONFIG-ID** za pomocą Identyfikatora klucza i konfiguracji (zobacz krok 1).

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
- [Konfigurowanie środowiska obsługiwanego interfejsu użytkownika](./hosted-ui.md)
- [Korzystanie ze znaczników dekoracji, aby wyróżnić tekst](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)
