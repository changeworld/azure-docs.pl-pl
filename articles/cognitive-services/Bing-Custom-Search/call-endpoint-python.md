---
title: 'Szybki start: wywoływanie punktu końcowego przy użyciu języka Python — wyszukiwanie niestandardowe Bing'
titlesuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start pokazano, jak wysłać żądanie wyników wyszukiwania z poziomu wystąpienia usługi wyszukiwania niestandardowego, wywołując punkt końcowy wyszukiwania niestandardowego Bing za pomocą języka Python.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: d16b7354b0ef727ba3c670b97105e1bd6f99034e
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815464"
---
# <a name="quickstart-call-bing-custom-search-endpoint-python"></a>Szybki start: wywoływanie punktu końcowego wyszukiwania niestandardowego Bing (Python)

W tym przewodniku Szybki start pokazano, jak wysłać żądanie wyników wyszukiwania z poziomu wystąpienia usługi wyszukiwania niestandardowego, wywołując punkt końcowy wyszukiwania niestandardowego Bing za pomocą języka Python. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Gotowe do użycia wystąpienie usługi wyszukiwania niestandardowego. Zobacz [Tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing](quick-start.md).
- Zainstalowane środowisko języka [Python](https://www.python.org/).
- Klucz subskrypcji. Klucz subskrypcji możesz uzyskać, aktywując [bezpłatną wersję próbną](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), lub użyć klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure (zobacz [Konto interfejsu Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


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
