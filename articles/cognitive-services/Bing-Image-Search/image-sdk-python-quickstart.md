---
title: Obraz wyszukiwania zestawu SDK Python — Szybki Start | Dokumentacja firmy Microsoft
description: Ustawienia dla zestawu SDK Search obrazu aplikacji konsoli.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: e30852439ad8ec2d5ddc667b75167e8b5d35be33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349449"
---
# <a name="image-search-sdk-python-quickstart"></a>Obraz wyszukiwania zestawu SDK Python — Szybki Start

Zestaw SDK wyszukiwania usługi Bing obraz zawiera funkcje interfejsu API REST dla zapytań sieci web oraz wyniki analizy. 

[Źródła kodu dla przykładów zestaw SDK Python usługi Bing obraz wyszukiwania](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) jest dostępna w Centrum Git.

## <a name="application-dependencies"></a>Zależności aplikacji
Jeśli nie masz jeszcze go, należy zainstalować Python. Zestaw SDK jest zgodny z Python 2.7 3.3, 3.4, 3.5 i 3,6.

Ogólne zalecenia dotyczące programowania Python jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html). Zainstaluj i zainicjować środowiska wirtualnego z [venv modułu](https://pypi.python.org/pypi/virtualenv). Musisz zainstalować virtualenv dla języka Python 2.7.
```
python -m venv mytestenv
```
Zainstaluj zależności Bing obraz wyszukiwania SDK:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Obraz wyszukiwania klienta
Pobierz [klucz dostępu usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) w obszarze *wyszukiwania*. Dodaj importów:
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Utwórz wystąpienie `CognitiveServicesCredentials`i Utwórz wystąpienie klienta:
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Wyszukiwanie obrazów w zapytaniu (Yosemite) przefiltrowana pod kątem animowane pliki GIF i obrazu. Sprawdź liczbę wyników i wydrukuj insightsToken, adres URL miniatury i sieci web adres URL pierwszego wyniku.
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
Wyszukiwanie obrazów (Yosemite) przefiltrowana pod kątem animowane pliki GIF i obrazu.  Sprawdź liczbę wyników.  Wydrukować `insightsToken`, `thumbnail url` i `web url` pierwszego wyniku.
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

Pobierz wyniki trendów:
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

[Zestaw SDK Python usługi kognitywnych — przykłady](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


