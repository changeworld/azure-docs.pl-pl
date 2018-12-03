---
title: 'Szybki start: wywoływanie punktu końcowego za pomocą środowiska Node.js — wyszukiwanie niestandardowe Bing'
titlesuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start pokazano, w jaki sposób wysyłać żądania wyników wyszukiwania z niestandardowego wystąpienia wyszukiwania przy użyciu środowiska Node.js w celu wywołania punktu końcowego wyszukiwania niestandardowego Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: c0c97dd52f8fc3ff590c86f32f794beeb00f4b05
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310256"
---
# <a name="quickstart-call-bing-custom-search-endpoint-nodejs"></a>Szybki start: wywoływanie punktu końcowego wyszukiwania niestandardowego Bing (Node.js)

W tym przewodniku Szybki start pokazano, w jaki sposób wysyłać żądania wyników wyszukiwania z niestandardowego wystąpienia wyszukiwania przy użyciu środowiska Node.js w celu wywołania punktu końcowego wyszukiwania niestandardowego Bing. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Gotowe do użycia wystąpienie usługi wyszukiwania niestandardowego. Zobacz [Tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing](quick-start.md).
- Zainstalowane środowisko [Node.js](https://www.nodejs.org/).
- Klucz subskrypcji. Klucz subskrypcji możesz uzyskać, aktywując [bezpłatną wersję próbną](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), lub użyć klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure (zobacz [Konto interfejsu Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).   Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić ten przykład, wykonaj następujące kroki:

1. Utwórz folder do przechowywania kodu.  
  
2. W wierszu polecenia lub terminalu przejdź do właśnie utworzonego folderu.  
  
3. Zainstaluj moduł węzła **request**.
    <pre>
    npm install request
    </pre>  
    
4. W utworzonym folderze utwórz plik o nazwie BingCustomSearch.js i skopiuj do niego następujący kod. Zastąp wartości **YOUR-SUBSCRIPTION-KEY** i **YOUR-CUSTOM-CONFIG-ID** odpowiednio kluczem subskrypcji i identyfikatorem konfiguracji.  
  
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

## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie środowiska hostowanego interfejsu użytkownika](./hosted-ui.md)
- [Wyróżnianie tekstu za pomocą znaczników dekoracji](./hit-highlighting.md)
- [Dzielenie na strony wyników wyszukiwania stron internetowych](./page-webpages.md)
