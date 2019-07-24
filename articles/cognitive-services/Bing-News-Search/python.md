---
title: 'Szybki start: Wykonaj wyszukiwanie w wiadomościach za pomocą języka Python i interfejsu API REST wyszukiwanie wiadomości Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania do interfejs API REST wyszukiwania wiadomości Bing przy użyciu języka Python i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 6/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2fc67cbfedccd69ff9063b0575ae51197eceb09d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423686"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Szybki start: Wyszukiwanie wiadomości przy użyciu języka Python i interfejsu API REST wyszukiwania wiadomości Bing

Ten przewodnik Szybki start umożliwi Ci utworzenie pierwszego wywołania do interfejsu API wyszukiwania wiadomości Bing i odebranie odpowiedzi JSON. Ta prosta aplikacja JavaScript wysyła zapytanie wyszukiwania do interfejsu API i przetwarza wyniki. Mimo że ta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną ze standardem RESTful i większością języków programowania.

Ten przykład kodu można uruchomić jako notes programu Jupyter w witrynie [MyBinder](https://mybinder.org). W tym celu należy kliknąć znaczek Launch Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Kod źródłowy tego przykładu jest dostępny także w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik języka Python w ulubionym środowisku IDE lub edytorze, a następnie zaimportuj moduł żądania. Utwórz zmienne dla klucza subskrypcji, punktu końcowego i terminu wyszukiwania. Punkt końcowy usługi można znaleźć na pulpicie nawigacyjnym platformy Azure.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Tworzenie parametrów dla żądania

1. Dodaj klucz subskrypcji do nowego słownika przy użyciu ciągu `"Ocp-Apim-Subscription-Key"` jako klucza. Zrób to samo dla parametrów wyszukiwania.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Wysyłanie żądania i odbieranie odpowiedzi

1. Użyj biblioteki żądań, aby wywołać interfejs API wyszukiwania wizualnego Bing przy użyciu klucza subskrypcji i obiektów słownika utworzonych w ostatnim kroku.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. Element `search_results` zawiera odpowiedź z interfejsu API w postaci obiektu JSON. Masz dostęp do opisów artykułów zawartych w odpowiedzi.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Wyświetlanie wyników

Następnie opisy te można przedstawić w formie tabeli, w której słowa kluczowe wyszukiwania są wyróżnione za pomocą **pogrubienia**.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-news-search-single-page-app.md)
