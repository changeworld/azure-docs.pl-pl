---
title: 'Szybki start: zestaw SDK wyszukiwania wizualnego Bing, Python'
titleSuffix: Azure Cognitive Services
description: Konfigurowanie aplikacji konsolowej zestawu SDK wyszukiwania wizualnego w języku Python.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: 935241d5574a59edfa256b74db3a0aeab2f7bf91
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458109"
---
# <a name="quickstart-bing-visual-search-sdk-python"></a>Szybki start: zestaw SDK wyszukiwania wizualnego Bing i język Python

Zestaw SDK wyszukiwania wizualnego Bing używa funkcji interfejsu API REST na potrzeby żądań internetowych i analizowania wyników.
[Kod źródłowy przykładów zestawu SDK wyszukiwania wizualnego Bing w języku Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) jest dostępny w usłudze Git Hub.

Scenariusze kodu są udokumentowane w następujących sekcjach:
* [Klient wyszukiwania wizualnego](#client)
* [Kompletna aplikacja konsolowa](#complete-console)
* [Wpis pliku binarnego obrazu z obiektem cropArea](#binary-crop)
* [Parametr KnowledgeRequest](#knowledge-req)
* [Tagi, akcje i element actionType](#tags-actions)

## <a name="application-dependencies"></a>Zależności aplikacji
* Klucz interfejsu API usług Cognitive Services jest wymagany do uwierzytelniania wywołań zestawu SDK. Utwórz konto, aby otrzymać [klucz bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=search-api-v7). Klucz wersji próbnej obowiązuje przez siedem dni i oferuje jedno wywołanie na sekundę. W przypadku scenariusza produkcyjnego [kup klucz dostępu](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7). Zobacz również [informacje o cenach](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/visual/).
* Jeśli nie masz języka Python, zainstaluj go. Zestaw SDK jest zgodny ze środowiskiem Python w wersjach 2.7, 3.3, 3.4, 3.5 i 3.6.
* W przypadku programowania w języku Python zalecane jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html). Zainstaluj i zainicjuj środowisko wirtualne przy użyciu [modułu venv](https://pypi.python.org/pypi/virtualenv). Zainstaluj virtualenv dla środowiska Python w wersji 2.7.
```
python -m venv mytestenv
```
Zainstaluj zależności zestawu SDK wyszukiwania wizualnego Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-visualsearch
```

<a name="client"></a> 
## <a name="visual-search-client"></a>Klient wyszukiwania wizualnego
Aby utworzyć wystąpienie klienta `VisualSearchAPI`, zaimportuj następujące biblioteki:
```
import http.client, urllib.parse
import json
import os.path
from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,
)
```
Zastąp wartość ciągu subscriptionKey prawidłowym kluczem subskrypcji.
```
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
```
Następnie utwórz wystąpienie klienta:
```
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```
Użyj klienta do wyszukiwania obrazów i analizowania wyników:
```
PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
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

<a name="complete-console"></a> 
## <a name="complete-console-application"></a>Kompletna aplikacja konsolowa

Następująca aplikacja konsolowa wykonuje wcześniej zdefiniowane zapytanie i analizuje wyniki:
```
import http.client, urllib.parse
import json
import os.path

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'

PATH = 'C:\\Users\\v-gedod\\azure-cognitive-samples\\mytestenv\\TestImages\\'

from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,)

from msrest.authentication import CognitiveServicesCredentials

client = VisualSearchAPI(CognitiveServicesCredentials(subscription_key))

image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
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


# Uncomment these methods to include code under the following headings of this documentation.
#search_image_binary_with_crop_area(client, subscription_key, PATH)
#search_url_with_filters(client, subscription_key)
#search_insights_token_with_crop_area(client, subscription_key)

```

Przykłady wyszukiwania Bing pokazują różne funkcje zestawu SDK.  Dodaj poniższe funkcje do zdefiniowanej wcześniej klasy `VisualSrchSDK`.

<a name="binary-crop"></a>
## <a name="image-binary-post-with-croparea"></a>Wpis pliku binarnego obrazu z obiektem cropArea

Poniższy kod wysyła plik binarny obrazu w treści żądania post wraz z obiektem cropArea.  Następnie wyświetla on token imageInsightsToken, liczbę tagów, liczbę akcji i pierwszy element actionType.

```
def search_image_binary_with_crop_area(client, sub_key, file_path):

    #client = VisualSearchAPI(CognitiveServicesCredentials(sub_key))

    image_path = os.path.join(file_path, "image.jpg")
    with open(image_path, "rb") as image_fd:

        crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)
        knowledge_request = VisualSearchRequest(image_info=ImageInfo(crop_area=crop_area))

        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(knowledge_request.serialize())

        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)

        if not result:
            print("No visual search result data.")
            return

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
<a name="knowledge-req"></a>
## <a name="knowledgerequest-parameter"></a>Parametr KnowledgeRequest

Poniższy kod wysyła adres URL obrazu w parametrze `knowledgeRequest` razem z filtrem witryny \"site:pinterest.com\". Następnie wyświetla on token `imageInsightsToken`, liczbę tagów, liczbę akcji i pierwszy element actionType.
```
def search_url_with_filters(client_in, sub_key):

    client = client_in

    image_url = "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80"
    filters = Filters(site="pinterest.com")

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(url=image_url),
        knowledge_request=KnowledgeRequest(filters=filters)
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with url of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

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
<a name="tags-actions"></a>
## <a name="tags-actions-and-actiontype"></a>Tagi, akcje i element actionType

Poniższy kod wysyła token szczegółowych informacji o obrazie w parametrze knowledgeRequest razem z obiektem cropArea. Następnie wyświetla on token imageInsightsToken, liczbę tagów, liczbę akcji i pierwszy element actionType.

```
    client = client_in

    image_insights_token = "bcid_CA6BDBEA28D57D52E0B9D4B254F1DF0D*ccid_6J+8V1zi*thid_R.CA6BDBEA28D57D52E0B9D4B254F1DF0D"
    crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(
            image_insights_token=image_insights_token,
            crop_area=crop_area
        ),
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with URL of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

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

## <a name="next-steps"></a>Następne kroki

[Przykłady zastosowania zestawu SDK usług Cognitive Services na platformie .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).