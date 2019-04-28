---
title: 'Szybki start: zestaw SDK wyszukiwania wizualnego Bing i język Python'
titleSuffix: Azure Cognitive Services
description: Konfigurowanie aplikacji konsolowej zestawu SDK wyszukiwania wizualnego w języku Python.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: 970b3e7e8e45e5d5249fb5a45c966d9395b130a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829297"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-python"></a>Szybki start: uzyskiwanie szczegółowych informacji o obrazach przy użyciu zestawu SDK wyszukiwania wizualnego Bing dla języka Python

Użyj tego przewodnika Szybki start, aby rozpocząć uzyskiwanie szczegółowych danych obrazu w usłudze wyszukiwania wizualnego Bing przy użyciu zestawu SDK dla języka Python. Chociaż wyszukiwanie wizualne Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe integrowanie usługi z Twoimi aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) 

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko [Python](https://www.python.org/) 2.x lub 3.x
* Zalecane jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html). Zainstaluj i zainicjuj środowisko wirtualne przy użyciu [modułu venv](https://pypi.python.org/pypi/virtualenv). Zainstaluj virtualenv dla środowiska Python w wersji 2.7.
* Zestaw SDK wyszukiwania wizualnego Bing dla języka Python. Możesz go zainstalować za pomocą następujących poleceń:
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`



[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następujące instrukcje importu. 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchClient
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    ```
2. Utwórz zmienne dla klucza subskrypcji, identyfikatora konfiguracji niestandardowej i obrazu, który chcesz przekazać. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. Tworzenie wystąpienia klienta

    ```python
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"))
    ```

## <a name="send-the-search-request"></a>Wysyłanie żądania wyszukiwania

1. Mając otwarty plik obrazu, zserializuj funkcję `VisualSearchRequest()` i przekaż ją jako parametr `knowledge_request` dla funkcji `visual_search()`.

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. Jeśli zostaną zwrócone jakiekolwiek wyniki, wydrukuj te wyniki, tagi oraz akcje w pierwszym tagu.

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-visual-search-single-page-app.md)
