---
title: 'Quickstart: Search for entities with the SDK for Python - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Use this quickstart to search for entities with the Bing Entity Search SDK for Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 7720304cc017db86b052cee39ca0430e5c1bf9c2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327123"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Szybki start: zestaw Bing Entity Search SDK i język Python

Użyj tego poradnika Szybki start, aby zacząć wyszukiwać jednostki za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka Python. Mimo że wyszukiwanie jednostek Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe zintegrowanie tej usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko Python [2.x lub 3.x](https://www.python.org/)

* [Zestaw SDK wyszukiwania jednostek Bing dla języka Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Zalecane jest użycie środowiska wirtualnego języka Python. Środowisko wirtualne możesz zainstalować i zainicjować przy użyciu modułu venv. You can install virtualenv with:

```Console
python -m venv mytestenv
```

Zainstaluj zestaw SDK wyszukiwania jednostek Bing za pomocą następującego polecenia:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następujące instrukcje importu. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Utwórz zmienną na potrzeby klucza subskrypcji, a następnie utwórz wystąpienie klienta, tworząc za jego pomocą obiekt `CognitiveServicesCredentials`.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Wysyłanie żądania wyszukiwania i odbieranie odpowiedzi

1. Wyślij żądanie wyszukiwania do wyszukiwania jednostek Bing za pomocą funkcji `client.entities.search()` zapytania wyszukiwania. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Jeśli jednostki zostały zwrócone, przekonwertuj element `entity_data.entities.value` do listy, a następnie wyświetl pierwszy wynik.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../overview.md )