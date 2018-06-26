---
title: Visual wyszukiwania zestawu SDK Python — Szybki Start | Dokumentacja firmy Microsoft
description: Instalator Visual wyszukiwania zestawu SDK Python aplikacji konsoli.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: f7a1f275f9059abdceaef577fb5ca722c9951366
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939467"
---
# <a name="visual-search-sdk-python-quickstart"></a>Visual wyszukiwania zestawu SDK Python — Szybki Start

Visual SDK wyszukiwania usługi Bing korzysta z funkcji interfejsu API REST dla żądań sieci web i wyniki analizy.
[Źródła kodu dla języka Python Bing Visual wyszukiwania SDK próbek](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) jest dostępna w Centrum Git.

Opisano scenariusze kod w następujących pozycji:
* [Klient Visual wyszukiwania](#client)
* [Aplikacja konsolowa ukończone](#complete-console)
* [Obraz binarny post z cropArea](#binary-crop)
* [Parametr KnowledgeRequest](#knowledge-req)
* [Tagi, działania i actionType](#tags-actions)

## <a name="application-dependencies"></a>Zależności aplikacji
* Klucz interfejsu API usługi kognitywnych jest wymagany do uwierzytelnienia wywołania SDK. Zaloguj się do [bezpłatnej wersji próbnej klucza](https://azure.microsoft.com/try/cognitive-services/?api=search-api-v7). Klucz wersji próbnej jest prawidłowa na siedem dni, z 1 wywołania na sekundę. W scenariuszu produkcyjnym [kupić klucz dostępu](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7). Zobacz też [uzyskać informacje o cenach](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/visual/).
* Jeśli nie masz jeszcze go, należy zainstalować Python. Zestaw SDK jest zgodny z Python 2.7 3.3, 3.4, 3.5 i 3,6.
* Ogólne zalecenia dotyczące programowania Python jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html). Zainstaluj i zainicjować środowiska wirtualnego z [venv modułu](https://pypi.python.org/pypi/virtualenv). Zainstaluj virtualenv for Python 2.7.
```
python -m venv mytestenv
```
Zainstaluj zestaw SDK usługi Bing Visual wyszukiwania zależności:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-visualsearch
```

<a name="client"></a> 
## <a name="visual-search-client"></a>Visual klienta wyszukiwania
Aby utworzyć wystąpienie `VisualSearchAPI` klienta, zaimportuj następujące biblioteki:
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
Zastąp wartość ciągu subscriptionKey swoim kluczem ważnej subskrypcji.
```
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
```
Następnie można utworzyć wystąpienia klienta:
```
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```
Używanie klienta do wyszukiwania obrazów i przeanalizować wyników:
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
## <a name="complete-console-application"></a>Aplikacja konsolowa ukończone

Następującej aplikacji konsoli wykonuje zapytanie uprzednio zdefiniowany i analizuje wyniki:
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

Przykłady wyszukiwania usługi Bing pokazują różne funkcje zestawu SDK.  Dodaj następujące funkcje do uprzednio zdefiniowanej `VisualSrchSDK` klasy.

<a name="binary-crop"></a>
## <a name="image-binary-post-with-croparea"></a>Obraz binarny post z cropArea

Poniższy kod wysyła obraz binarny w treści żądania post, wraz z obiektem cropArea.  Następnie drukuje imageInsightsToken, liczba tagów, liczbę akcji i actionType pierwszy.

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

Poniższy kod wysyła adres url obrazu w `knowledgeRequest` parametru, wraz z \"witryny: www.bing.com\" filtru. A następnie wyświetla `imageInsightsToken`, liczba tagów, liczbę akcji i actionType pierwszy.
```
def search_url_with_filters(client_in, sub_key):

    client = client_in

    image_url = "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80"
    filters = Filters(site="www.bing.com")

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
## <a name="tags-actions-and-actiontype"></a>Tagi, działania i actionType

Poniższy kod wysyła token obrazu wgląd w parametrze knowledgeRequest wraz z obiektem cropArea. Następnie drukuje imageInsightsToken, liczba tagów, liczbę akcji i actionType pierwszy.

```
    client = client_in

    image_insights_token = "bcid_113F29C079F18F385732D8046EC80145*ccid_oV/QcH95*mid_687689FAFA449B35BC11A1AE6CEAB6F9A9B53708*thid_R.113F29C079F18F385732D8046EC80145"
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

## <a name="next-steps"></a>Kolejne kroki

[Usługi kognitywnych przykłady zestawu .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).