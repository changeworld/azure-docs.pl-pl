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
ms.openlocfilehash: 88bf82805ba46abf79b7899e0428a83485062302
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504971"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Wywołanie punktu końcowego usługi Bing Custom Search (Python)

Ten przewodnik Szybki Start pokazano, jak żądanie wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego przy użyciu języka Python do wywoływania punktu końcowego usługi Bing Custom Search. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Wystąpienie wyszukiwania niestandardowego. Zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](quick-start.md).

-  [Python](https://www.python.org/) zainstalowane.

- A [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsy API wyszukiwania Bing**. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) jest wystarczająca na potrzeby tego przewodnika Szybki Start. Wymagany jest klucz dostępu podany przy wywołaniu metody Aktywuj bezpłatną wersję próbną lub klucz płatnej subskrypcji może używać z pulpitu nawigacyjnego platformy Azure. 

## <a name="run-the-code"></a>Uruchamianie kodu

Aby wywołać punkt końcowy wyszukiwania niestandardowego Bing, wykonaj następujące kroki:

1. Utwórz folder dla kodu.

2. Z wiersza polecenia administratora lub terminalu przejdź do folderu, który został utworzony.

3. Zainstaluj **żądań** modułu python:

    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
    
7. Utwórz plik BingCustomSearch.py i skopiuj następujący kod do niego.

8. Zastąp **YOUR-SUBSCRIPTION-KEY** i **YOUR-CUSTOM-CONFIG-ID** za pomocą Identyfikatora klucza i konfiguracji (zobacz krok 1).

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
9. Uruchom kod za pomocą następującego polecenia.
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie środowiska obsługiwanego interfejsu użytkownika](./hosted-ui.md)
- [Korzystanie ze znaczników dekoracji, aby wyróżnić tekst](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)
