---
title: 'Szybki Start: wykonywanie wyszukiwania wiadomości za pomocą zestawu SDK dla języka Python — wyszukiwanie wiadomości Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wyszukać wiadomości przy użyciu zestawu SDK wyszukiwania wiadomości Bing dla języka Python i przetworzyć odpowiedź.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2f3d8c88e7949265f12405760acbccebb89d1df5
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379058"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Szybki Start: wykonywanie wyszukiwania wiadomości za pomocą zestawu SDK wyszukiwanie wiadomości Bing dla języka Python

Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości za pomocą zestawu SDK wyszukiwania wiadomości Bing dla języka Python. Mimo że wyszukiwanie wiadomości Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe zintegrowanie tej usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko [Python](https://www.python.org/) 2.x lub 3.x

Zalecane jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html) dla programowania w języku python. Środowisko wirtualne możesz zainstalować i zainicjować przy użyciu [modułu venv](https://pypi.python.org/pypi/virtualenv). Musi być zainstalowany moduł virtualenv dla języka Python 2.7. Środowisko wirtualne można utworzyć w następujący sposób:

```console
python -m venv mytestenv
```

Zależności zestawu SDK wyszukiwania wiadomości Bing można zainstalować za pomocą następującego polecenia:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik kodu Python w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki. Utwórz zmienną dla klucza subskrypcji i termin wyszukiwania.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Inicjowanie klienta i wysyłanie żądania

1. Utwórz wystąpienie elementu `CognitiveServicesCredentials`. Utwórz wystąpienie klienta:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Wyślij zapytanie wyszukiwania do interfejsu API wyszukiwania wiadomości i zapisz odpowiedź.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Analizowanie odpowiedzi

Jeśli zostaną zwrócone jakieś wyniki wyszukiwania, wydrukuj pierwszą stronę internetową wyników:

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-news-search-single-page-app.md)
