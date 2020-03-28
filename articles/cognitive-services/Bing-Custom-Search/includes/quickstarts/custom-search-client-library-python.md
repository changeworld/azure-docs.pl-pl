---
title: Szybki start biblioteki klienta usługi Bing Custom Search Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78252884"
---
Wprowadzenie do biblioteki klienta wyszukiwania niestandardowego Bing dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Interfejs API wyszukiwania niestandardowego Bing umożliwia tworzenie dostosowanych do potrzeb wyszukiwarek bez reklam dla tematów, na których Ci zależy. Kod źródłowy dla tego przykładu można znaleźć na [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Użyj biblioteki klienta wyszukiwania niestandardowego Bing dla języka Python, aby:
* Znajdź wyniki wyszukiwania w internecie z wystąpienia wyszukiwania niestandardowego Bing.

[Dokumentacja](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) | [referencyjna Przykłady pakietu kodu źródłowego](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [(PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [biblioteki](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Zobacz [Szybki start: tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing, aby](../../quick-start.md) uzyskać więcej informacji.
- Środowisko Python [2.x lub 3.x](https://www.python.org/) 
- [Niestandardowy moduł SDK wyszukiwania Bing dla języka Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Instalowanie biblioteki klienta języka Python

Zainstaluj bibliotekę klienta wyszukiwania niestandardowego Bing za pomocą następującego polecenia.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

Utwórz nowy plik Języka Python w ulubionym edytorze lub IDE i dodaj następujące importy.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Tworzenie klienta wyszukiwania i wysyłanie żądania

1. Utwórz zmienną dla klucza subskrypcji i punktu końcowego.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Utwórz wystąpienie `CustomSearchClient`obiektu `CognitiveServicesCredentials` , używając obiektu z kluczem subskrypcji. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Wyślij żądanie wyszukiwania `client.custom_instance.search()`za pomocą pliku . Dołącz wyszukiwany termin `query` do parametru `custom_config` i ustaw identyfikator konfiguracji niestandardowej, aby użyć wystąpienia wyszukiwania. Identyfikator można uzyskać z [portalu wyszukiwania niestandardowego Bing,](https://www.customsearch.ai/)klikając kartę **Produkcja.**

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Wyświetlanie wyników wyszukiwania

Jeśli znaleziono wyniki wyszukiwania stron internetowych, pobierz pierwszą i wydrukuj jego nazwę, adres URL i łączną liczbę znalezionych stron internetowych.

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
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](../../tutorials/custom-search-web-page.md)
