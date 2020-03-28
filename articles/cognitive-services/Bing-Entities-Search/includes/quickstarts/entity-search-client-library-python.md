---
title: Szybki start biblioteki klienta usługi Bing Entity Search Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136760"
---
Ten przewodnik Szybki start umożliwia rozpoczęcie wyszukiwania jednostek za pomocą biblioteki klienta wyszukiwania jednostek Bing dla języka Python. Usługa Bing Entity Search ma interfejs API REST zgodny z większością języków programowania, ale biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko Python [2.x lub 3.x](https://www.python.org/)

* [Zestaw SDK wyszukiwania jednostek Bing dla języka Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Zalecane jest użycie środowiska wirtualnego języka Python. Środowisko wirtualne możesz zainstalować i zainicjować przy użyciu modułu venv. Virtualenv można zainstalować za pomocą:

```Console
python -m venv mytestenv
```

Zainstaluj bibliotekę klienta wyszukiwania jednostek Bing za pomocą:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następujące instrukcje importu. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Utwórz zmienną dla klucza subskrypcji i punktu końcowego. Tworzenie wystąpienia klienta przez utworzenie `CognitiveServicesCredentials` nowego obiektu za pomocą klucza.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
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
> [Tworzenie jednostronicowej aplikacji sieci Web](../../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../../overview.md )