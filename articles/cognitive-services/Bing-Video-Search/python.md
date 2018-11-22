---
title: 'Szybki start: wyszukiwanie wideo Bing, Python'
titlesuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API wyszukiwania wideo Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: ccc27481289ffc686e3e480685ba421c762e3718
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161088"
---
# <a name="quickstart-bing-video-search-api-with-python"></a>Szybki start: interfejs API wyszukiwania wideo Bing oraz język Python

W tym artykule pokazano, jak używać interfejsu API wyszukiwania wideo Bing, który jest częścią usług Microsoft Cognitive Services na platformie Azure. Zapoznaj się [dokumentacją interfejsu API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference), aby uzyskać szczegółowe informacje techniczne dotyczące interfejsów API.

Ten przykład można uruchomić jako notes programu Jupyter w witrynie [MyBinder](https://mybinder.org). W tym celu należy kliknąć ikonę Launch Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Wymagania wstępne
Trzeba mieć [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do **interfejsów API wyszukiwania Bing**. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca na potrzeby tego przewodnika Szybki start. Potrzebny jest klucz dostępu podany przy aktywacji bezpłatnej wersji próbnej lub klucz płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="running-the-walkthrough"></a>Uruchamianie przewodnika

Najpierw nadaj zmiennej `subscription_key` wartość klucza interfejsu API dla usługi interfejsu API Bing.


```python
subscription_key = None
assert subscription_key
```

Następnie upewnij się, że wartość `search_url` punktu końcowego jest poprawna. Obecnie tylko jeden punkt końcowy jest używany dla interfejsów API wyszukiwania Bing. Jeśli wystąpią błędy autoryzacji, dokładnie sprawdź tę wartość, porównując ją z punktem końcowym wyszukiwania w usłudze Bing na pulpicie nawigacyjnym platformy Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

Nadaj zmiennej `search_term` wartość kittens, aby wyszukiwać wideo z kociętami


```python
search_term = "kittens"
```

Następujący blok wykorzystuje bibliotekę `requests` w języku Python do wywoływania interfejsów API wyszukiwania Bing i zwracania wyników w postaci obiektu JSON. Zobacz, jak przekazujemy klucz interfejsu API za pośrednictwem słownika `headers` i wyszukiwany termin za pośrednictwem słownika `params`. Aby wyświetlić pełną listę opcji, które mogą służyć do odfiltrowania wyników wyszukiwania, zobacz dokumentację [interfejsu API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Obiekt `search_results` zawiera odpowiednie filmy wideo oraz obszerne metadane. Aby wyświetlić jeden z filmów, użyj jego właściwości `embedHtml` i wprowadź ją do `IFrame`.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Stronicowanie wyników wyszukiwania wideo](paging-videos.md)
> [Zmiana rozmiaru i przycinanie obrazów miniatur](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>Zobacz też 

 [Wyszukiwanie filmów wideo w Internecie](search-the-web.md) [Wypróbuj](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
