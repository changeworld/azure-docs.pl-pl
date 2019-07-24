---
title: 'Szybki start: Zestaw Bing Entity Search SDK, język Python'
titleSuffix: Azure Cognitive Services
description: Konfiguracja pod kątem aplikacji konsoli zestawu Bing Entity Search SDK.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: aahi
ms.openlocfilehash: 9574abefc9747a0718b4d995f4efa01388aee336
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404913"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Szybki start: Zestaw Bing Entity Search SDK i język Python

Użyj tego poradnika Szybki start, aby zacząć wyszukiwać jednostki za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka Python. Mimo że wyszukiwanie jednostek Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe zintegrowanie tej usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko Python [2.x lub 3.x](https://www.python.org/)

* [Zestaw SDK wyszukiwania jednostek Bing dla języka Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Zalecane jest użycie środowiska wirtualnego języka Python. Środowisko wirtualne możesz zainstalować i zainicjować przy użyciu modułu venv. Możesz zainstalować virtualenv z:

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../overview.md )