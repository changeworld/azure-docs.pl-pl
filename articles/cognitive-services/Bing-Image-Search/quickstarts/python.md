---
title: 'Szybki Start: Wysyłania zapytań do wyszukiwania przy użyciu interfejsu API wyszukiwania obrazów Bing i języka Python'
description: W tym przewodniku Szybki Start możesz wysyłać zapytania wyszukiwania interfejsu API wyszukiwania Bing w celu uzyskania listy obrazów przy użyciu języka Python.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: 42b9af2d6c7223dc3f5d269dd2003f1c99d8c7da
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578331"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Szybki Start: Wysyłanie zapytania przy użyciu interfejsu API REST i Python

Ten przewodnik Szybki Start umożliwia utworzenie pierwszego wywołania do interfejsu API wyszukiwania obrazów Bing i odbierać odpowiedź w formacie JSON. Ta prosta aplikacja języka Python wysyła zapytanie wyszukiwania do interfejsu API i wyświetli nieprzetworzone wyniki.

Podczas tej aplikacji został napisany w języku Python, interfejs API jest zgodny z większość języków programowania usługi sieci Web typu RESTful.

W tym przykładzie można uruchomić jako notesu programu Jupyter na [MyBinder](https://mybinder.org) , klikając polecenie Uruchom integratora znaczków: 

[![Obiekt wiążący](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Ponadto kod źródłowy dla tego przykładu jest dostępna [w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-quickstart"></a>Uruchamianie szybkiego startu

Aby rozpocząć pracę, należy ustawić `subscription_key` kluczowi ważnej subskrypcji dla usługi interfejsu API Bing.

```python
subscription_key = None
assert subscription_key
```

Następnie upewnij się, że `search_url` punktu końcowego jest poprawna. W tym tylko jeden punkt końcowy jest używany dla interfejsami API wyszukiwania Bing. Jeśli wystąpią błędy autoryzacji, należy dokładnie sprawdzić tę wartość na punkt końcowy wyszukiwania Bing w pulpicie nawigacyjnym platformy Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Ustaw `search_term` do wyszukiwania obrazów Szczeniaki.


```python
search_term = "puppies"
```

Z następującego bloku używa `requests` biblioteki w języku Python do wyróżnienia do interfejsów API wyszukiwania Bing i zwracają wyniki w postaci obiektu JSON. Sprawdź, czy przekazanie klucza interfejsu API za pośrednictwem `headers` słownik i wyszukaj termin za pośrednictwem `params` słownika. Aby wyświetlić pełną listę opcji, które mogą służyć do odfiltrowania wyników wyszukiwania, zobacz [interfejsu API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) dokumentacji.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` Obiekt zawiera rzeczywiste obrazów oraz rozbudowane metadane, takie jak powiązane elementy. Na przykład następujący wiersz kodu można wyodrębnić adresy URL miniatury dla pierwszych 16 wyników.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Następnie użyj `PIL` biblioteki, aby pobrać obrazy miniatur i `matplotlib` biblioteki do renderowania je na siatce$ \times 4 4 USD.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
plt.show()
```

## <a name="sample-json-response"></a>Przykładowa odpowiedź JSON

Odpowiedzi z interfejsu API wyszukiwania obrazów Bing są zwracane w formacie JSON. Ta przykładowa odpowiedź została obcięta do Pokaż jeden wynik.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący aplikacji jednostronicowej wyszukiwania obrazów Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także 

* [Co to jest wyszukiwanie obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj prezentację online dla interaktywne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Uzyskaj bezpłatne klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentacja usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)