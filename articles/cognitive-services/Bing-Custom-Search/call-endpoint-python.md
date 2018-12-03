---
title: 'Szybki start: wywoływanie punktu końcowego przy użyciu języka Python — wyszukiwanie niestandardowe Bing'
titlesuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start pokazano, jak wysłać żądanie wyników wyszukiwania z poziomu wystąpienia usługi wyszukiwania niestandardowego, wywołując punkt końcowy wyszukiwania niestandardowego Bing za pomocą języka Python.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: d7977f49a484665f133a119f491bd8986c0b6fc4
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310723"
---
# <a name="quickstart-call-bing-custom-search-endpoint-python"></a>Szybki start: wywoływanie punktu końcowego wyszukiwania niestandardowego Bing (Python)

W tym przewodniku Szybki start pokazano, jak wysłać żądanie wyników wyszukiwania z poziomu wystąpienia usługi wyszukiwania niestandardowego, wywołując punkt końcowy wyszukiwania niestandardowego Bing za pomocą języka Python. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Gotowe do użycia wystąpienie usługi wyszukiwania niestandardowego. Zobacz [Tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing](quick-start.md).
- Zainstalowane środowisko języka [Python](https://www.python.org/).
- Klucz subskrypcji. Klucz subskrypcji możesz uzyskać, aktywując [bezpłatną wersję próbną](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), lub użyć klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure (zobacz [Konto interfejsu Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).   Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).


## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić ten przykład, wykonaj następujące kroki:

1. Utwórz folder do przechowywania kodu.  
  
2. W wierszu polecenia lub terminalu z uprawnieniami administratora przejdź do właśnie utworzonego folderu.  
  
3. Zainstaluj moduł **requests** języka Python:  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. W utworzonym folderze utwórz plik o nazwie BingCustomSearch.py i skopiuj do niego następujący kod. Zastąp wartości **YOUR-SUBSCRIPTION-KEY** i **YOUR-CUSTOM-CONFIG-ID** odpowiednio kluczem subskrypcji i identyfikatorem konfiguracji.  
  
    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```  
  
7. Uruchom kod za pomocą następującego polecenia.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie środowiska hostowanego interfejsu użytkownika](./hosted-ui.md)
- [Wyróżnianie tekstu za pomocą znaczników dekoracji](./hit-highlighting.md)
- [Dzielenie na strony wyników wyszukiwania stron internetowych](./page-webpages.md)
