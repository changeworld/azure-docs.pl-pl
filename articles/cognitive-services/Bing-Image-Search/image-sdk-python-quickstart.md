---
title: 'Szybki Start: Żądania i Filtruj obrazy przy użyciu zestawu SDK języka Python'
description: W tym przewodniku Szybki Start, żądania i Filtruj obrazy zwrócone przez wyszukiwanie obrazów Bing, przy użyciu języka Python.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 4729f103bb9b50d4ff039907db8eb677f3dc290a
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "41987624"
---
 # <a name="quickstart-request-and-filter-images-using-the-sdk-and-python"></a>Szybki Start: Żądania i Filtruj obrazy przy użyciu zestawu SDK i Python

Zestaw SDK wyszukiwania obrazów Bing zawiera funkcje interfejsu API REST dla sieci web kwerend i wyniki analizy. 

[Źródła kodu dla przykładowych zestawach SDK wyszukiwania obrazów Bing Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) jest dostępny w repozytorium.

## <a name="application-dependencies"></a>Zależności aplikacji
Jeśli jeszcze nie masz, zainstaluj język Python. Zestaw SDK jest zgodny z języka Python 2.7 3.3, 3.4, 3.5 i 3.6.

Ogólne zalecenia dotyczące programowania w języku Python jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html). Zainstaluj i zainicjuj środowisko wirtualne przy użyciu [modułu venv](https://pypi.python.org/pypi/virtualenv). Należy zainstalować virtualenv for Python 2.7.
```
python -m venv mytestenv
```
Instalowanie składników zależnych zestawu SDK wyszukiwania obrazów Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Obraz wyszukiwania klienta
Pobierz [klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze *wyszukiwania*. Dodaj import:
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Utwórz wystąpienie obiektu `CognitiveServicesCredentials`i tworzyć wystąpienia klienta:
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Wyszukiwanie obrazów w zapytaniu (Yosemite) filtrowany pod kątem animowane pliki GIF i panoramicznym. Sprawdź liczbę wyników i wydrukuj insightsToken, adres URL miniatury i sieci web adres URL pierwszego wyniku.
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
Wyszukiwanie obrazów (Yosemite) filtrowany pod kątem animowane pliki GIF i panoramicznym.  Sprawdź liczbę wyników.  Wydrukować `insightsToken`, `thumbnail url` i `web url` pierwszego wyniku.
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

Pobierz popularne wyniki:
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>Kolejne kroki

[Cognitive przykłady zestawu SDK języka Python usługi](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


