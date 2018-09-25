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
ms.openlocfilehash: 73c31c7175bd4dfcb182fb76784937c176ac7702
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977880"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Wywołanie punktu końcowego usługi Bing Custom Search (Node.js)

Ten przewodnik Szybki Start pokazano, jak żądanie wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego za pomocą środowiska Node.js, aby wywołać punkt końcowy wyszukiwania niestandardowego Bing. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Wystąpienie wyszukiwania niestandardowego gotowych do użycia. Zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](quick-start.md).
- [Node.js](https://www.nodejs.org/) zainstalowane.
- Klucz subskrypcji. Klucz subskrypcji możesz uzyskać po aktywowaniu usługi [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), lub za pomocą klucza płatnej subskrypcji w pulpicie nawigacyjnym platformy Azure (zobacz [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    

## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić ten przykład, wykonaj następujące kroki:

1. Utwórz folder dla kodu.  
  
2. W wierszu polecenia lub terminalu przejdź do folderu, który został utworzony.  
  
3. Zainstaluj **żądania** modułu węzła:
    <pre>
    npm install request
    </pre>  
    
4. Utwórz plik o nazwie BingCustomSearch.js w folderze, który został utworzony i skopiuj następujący kod do niego. Zastąp **YOUR-SUBSCRIPTION-KEY** i **YOUR-CUSTOM-CONFIG-ID** klucz subskrypcji i konfiguracji identyfikatora.  
  
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
  
6. Uruchom kod za pomocą następującego polecenia:  
  
    ```    
    node BingCustomSearch.js
    ``` 

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie środowiska obsługiwanego interfejsu użytkownika](./hosted-ui.md)
- [Korzystanie ze znaczników dekoracji, aby wyróżnić tekst](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)
