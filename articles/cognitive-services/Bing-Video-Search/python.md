---
title: Szybki Start języka Python dla usług Azure kognitywnych, wideo wyszukiwania usługi Bing interfejsu API | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API wyszukiwania usługi Bing wideo w kognitywnych usług Microsoft Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: ce4356f05e69540bc3bc3241e2ec1751ff7a7276
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347512"
---
# <a name="quickstart-for-bing-video-search-api-with-python"></a>Szybki Start dla wideo wyszukiwania usługi Bing interfejsu API za pomocą języka Python

W tym przewodniku przedstawiono sposób korzystania z usługi Bing wideo wyszukiwania interfejsu API, część kognitywnych usług Microsoft Azure. Zapoznaj się [dokumentacja interfejsu API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) uzyskać szczegółowe informacje techniczne dotyczące interfejsów API.

W tym przykładzie można uruchomić jako notesu Jupyter na [MyBinder](https://mybinder.org) , klikając polecenie Uruchom integratora znaczków: 

[![Obiekt wiążący](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Wymagania wstępne
Musi mieć [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsy API wyszukiwania usługi Bing**. [Bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca dla tego przewodnika Szybki Start. Należy klucz dostępu podany przy wywołaniu metody aktywacji bezpłatną wersję próbną lub może używać klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="running-the-walkthrough"></a>Uruchomiona przewodnika

Najpierw należy ustawić `subscription_key` do klucza interfejsu API dla usługi interfejsu API Bing.


```python
subscription_key = None
assert subscription_key
```

Następnie sprawdź, czy `search_url` punktu końcowego jest poprawna. W tym tylko jeden punkt końcowy jest używany do wyszukiwania usługi Bing interfejsów API. Jeśli wystąpią błędy autoryzacji dokładnie, czy ta wartość względem punktu końcowego wyszukiwania usługi Bing w pulpicie nawigacyjnym platformy Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

Ustaw `search_term` do wyszukania wideo kocięta


```python
search_term = "kittens"
```

Z poniższego bloku używa `requests` biblioteki w języku Python do wyróżnienia do wyszukiwania usługi Bing interfejsów API i zwrócenie wyników jako obiekt JSON. Sprawdź, czy jest przekazywana klucza interfejsu API za pomocą `headers` słownika i wyszukiwanie termin za pośrednictwem `params` słownika. Aby zapoznać się z pełną listą opcje, które mogą służyć do filtrowania wyników wyszukiwania, zapoznaj się [interfejsu API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) dokumentacji.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` Obiekt zawiera odpowiednie pliki wideo wraz z zaawansowanych metadanych. Aby przejrzeć pola wideo, użyj jej `embedHtml` właściwości i wstaw go w `IFrame`.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Filmy wideo stronicowania](paging-videos.md)
> [Resizing i przycinanie obrazów miniatur](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>Zobacz także 

 [Wyszukiwanie w sieci web wideo](search-the-web.md) [Wypróbuj](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
