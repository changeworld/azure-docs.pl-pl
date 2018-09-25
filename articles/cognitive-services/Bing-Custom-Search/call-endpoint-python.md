---
title: Wywoływanie punktu końcowego za pomocą języka Python — wyszukiwanie niestandardowe Bing — Microsoft Cognitive Services
description: Ten przewodnik Szybki Start pokazano, jak żądanie wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego przy użyciu języka Python do wywoływania punktu końcowego usługi Bing Custom Search.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 3666e92372e9bed80e5c0c7991dcac730cebb588
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967596"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Wywołanie punktu końcowego usługi Bing Custom Search (Python)

Ten przewodnik Szybki Start pokazano, jak żądanie wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego za pomocą języka Python, aby wywołać punkt końcowy wyszukiwania niestandardowego Bing. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Wystąpienie wyszukiwania niestandardowego gotowych do użycia. Zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](quick-start.md).
- [Python](https://www.python.org/) zainstalowane.
- Klucz subskrypcji. Klucz subskrypcji możesz uzyskać po aktywowaniu usługi [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), lub za pomocą klucza płatnej subskrypcji w pulpicie nawigacyjnym platformy Azure (zobacz [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić ten przykład, wykonaj następujące kroki:

1. Utwórz folder dla kodu.  
  
2. Z wiersza polecenia administratora lub terminalu przejdź do folderu, który został utworzony.  
  
3. Zainstaluj **żądań** modułu python:  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. Utwórz plik o nazwie BingCustomSearch.py w folderze, który został utworzony i skopiuj następujący kod do niego. Zastąp **YOUR-SUBSCRIPTION-KEY** i **YOUR-CUSTOM-CONFIG-ID** z kluczem subscriptioin i konfiguracji identyfikatora.  
  
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

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie środowiska obsługiwanego interfejsu użytkownika](./hosted-ui.md)
- [Korzystanie ze znaczników dekoracji, aby wyróżnić tekst](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)
