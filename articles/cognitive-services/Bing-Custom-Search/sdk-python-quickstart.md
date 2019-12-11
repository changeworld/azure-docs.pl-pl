---
title: 'Szybki Start: wywoływanie punktu końcowego wyszukiwanie niestandardowe Bing przy użyciu zestawu SDK języka Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć żądanie wyników wyszukiwania z wystąpienia wyszukiwanie niestandardowe Bing przy użyciu zestawu SDK języka Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 98070ab8f715305fe01c940026ecf5f49f8c28f9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976047"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Szybki Start: wywoływanie punktu końcowego wyszukiwanie niestandardowe Bing przy użyciu zestawu SDK języka Python 

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć żądanie wyników wyszukiwania z wystąpienia wyszukiwanie niestandardowe Bing przy użyciu zestawu SDK języka Python. Chociaż wyszukiwanie niestandardowe Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK wyszukiwania niestandardowego Bing umożliwia łatwe integrowanie usługi z Twoimi aplikacjami. Kod źródłowy tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) z dodatkową obsługą błędów i adnotacjami.

## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie pierwszego wystąpienia wyszukiwanie niestandardowe Bing](quick-start.md) .
- Środowisko Python [2.x lub 3.x](https://www.python.org/) 
- [Zestaw wyszukiwanie niestandardowe Bing SDK dla języka Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Instalowanie zestawu SDK języka Python

Zainstaluj zestaw SDK wyszukiwanie niestandardowe Bing przy użyciu poniższego polecenia.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

Utwórz nowy plik w języku Python w ulubionym edytorze lub w środowisku IDE i Dodaj następujące Importy.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Tworzenie klienta wyszukiwania i wysyłanie żądania

1. Utwórz zmienną dla klucza subskrypcji.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Utwórz wystąpienie `CustomSearchClient`przy użyciu obiektu `CognitiveServicesCredentials` z kluczem subskrypcji. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. Wyślij żądanie wyszukiwania za pomocą `client.custom_instance.search()`. Dołącz termin wyszukiwania do `query` parametru i ustaw `custom_config` na identyfikator konfiguracji niestandardowej, aby użyć wystąpienia wyszukiwania. Możesz uzyskać identyfikator z [portalu wyszukiwanie niestandardowe Bing](https://www.customsearch.ai/), klikając kartę **produkcja** .

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Wyświetlanie wyników wyszukiwania

Jeśli znaleziono jakiekolwiek wyniki wyszukiwania stron sieci Web, Pobierz pierwszy z nich i wydrukuj jego nazwę, adres URL i łączną liczbę znalezionych stron sieci Web.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](./tutorials/custom-search-web-page.md)
