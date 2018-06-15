---
title: Punkt końcowy wywołanie za pomocą usług kognitywnych Python - wyszukiwania usługi Bing niestandardowe - Microsoft
description: Ta opcja szybkiego startu pokazano, jak żądanie wyniki wyszukiwania z wystąpienia niestandardowego wyszukiwania przy użyciu języka Python do wywołania punktu końcowego niestandardowe wyszukiwania usługi Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349773"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Wywołanie wyszukiwania usługi Bing niestandardowe punktu końcowego (Python)

Ta opcja szybkiego startu pokazano, jak żądanie wyniki wyszukiwania z wystąpienia niestandardowego wyszukiwania przy użyciu języka Python do wywołania punktu końcowego niestandardowe wyszukiwania usługi Bing. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Wystąpienie wyszukiwania niestandardowego. Zobacz [Tworzenie pierwszego wystąpienia wyszukiwania usługi Bing niestandardowe](quick-start.md).

-  [Python](https://www.python.org/) zainstalowane.

- A [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsy API wyszukiwania usługi Bing**. [Bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) jest wystarczająca dla tego przewodnika Szybki Start. Należy klucz dostępu podany przy wywołaniu metody aktywacji bezpłatną wersję próbną lub może używać klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure. 

## <a name="run-the-code"></a>Uruchamianie kodu

Aby wywołać punktu końcowego wyszukiwania usługi Bing niestandardowe, wykonaj następujące kroki:

1. Utwórz folder dla kodu.
2. Z wiersza polecenia administratora lub w terminalu przejdź do folderu utworzonego.
3. Zainstaluj **żądań** modułu python:
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. Utwórz plik BingCustomSearch.py i skopiuj następujący kod do niego.
8. Zastąp **YOUR SUBSKRYPCJI klucza** i **YOUR-niestandardowe-CONFIG-ID** za pomocą Identyfikatora klucza i konfiguracji (zobacz krok 1).

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
- [Konfigurowanie środowiska hostowanej interfejsu użytkownika](./hosted-ui.md)
- [Umożliwia znaczników decoration wyróżnianie tekstu](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)