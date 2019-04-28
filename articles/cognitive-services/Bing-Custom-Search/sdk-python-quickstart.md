---
title: 'Szybki start: Wywołanie punktu końcowego usługi Bing Custom Search przy użyciu zestawu SDK języka Python | Dokumentacja firmy Microsoft'
titleSuffix: Azure Cognitive Services
description: Umożliwia uzyskiwanie wyników wyszukiwania niestandardowego Bing niestandardowe wyszukiwania zestawu SDK dla języka Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: c4c5059bc57ea33357145f6b119456dc6c5bdb7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946645"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Szybki start: Wywołanie punktu końcowego usługi Bing Custom Search przy użyciu zestawu SDK języka Python 

Użyj tego przewodnika Szybki Start, aby rozpocząć żądanie wyniki wyszukiwania z wystąpienia wyszukiwania niestandardowego Bing, przy użyciu zestawu SDK języka Python. Chociaż wyszukiwanie niestandardowe Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK wyszukiwania niestandardowego Bing umożliwia łatwe integrowanie usługi z Twoimi aplikacjami. Kod źródłowy dla tego przykładu znajduje się na [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) za pomocą obsługi dodatkowych błędów i adnotacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Zobacz [Szybki start: Tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing](quick-start.md), aby uzyskać więcej informacji.
- Środowisko Python [2.x lub 3.x](https://www.python.org/) 
- [Wyszukiwanie niestandardowe Bing zestawu SDK dla języka Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Zainstaluj zestaw Python SDK

Zainstaluj zestaw SDK wyszukiwania niestandardowego Bing za pomocą następującego polecenia.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

Utwórz nowy plik w języku Python w ulubionym edytorze lub IDE, a następnie dodaj następujące instrukcje importu.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Tworzenie klienta wyszukiwania oraz wysłać żądanie

1. Utwórz zmienną dla klucza subskrypcji.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Utwórz wystąpienie obiektu `CustomSearchClient`przy użyciu `CognitiveServicesCredentials` obiektu przy użyciu klucza subskrypcji. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. Wyślij żądanie wyszukiwania za pomocą `client.custom_instance.search()`. Dołącz swoje termin wyszukiwania, aby `query` parametru, a następnie ustaw `custom_config` do niestandardowego Identyfikatora konfiguracji do użycia wystąpienia wyszukiwania. Można uzyskać Identyfikatora z [portalu wyszukiwania niestandardowego Bing](https://www.customsearch.ai/), klikając **produkcji** kartę.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Wyniki wyszukiwania

Jeśli znaleziono żadnych wyników wyszukiwania strony sieci web, Uzyskaj pierwszy z nich i wydrukować jej nazwę, adres URL i łączna liczba stron sieci web, które można odnaleźć.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](./tutorials/custom-search-web-page.md)
