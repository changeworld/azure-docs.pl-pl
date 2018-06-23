---
title: Wywołanie i odpowiedź — Szybki Start języka Python dla usług Azure kognitywnych, interfejsu API Bing obraz wyszukiwania | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API wyszukiwania usługi Bing obrazu w kognitywnych usług Microsoft Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 3b5d6a961ce4bcde8aaf73f1fbd30689a6c2c2d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347516"
---
# <a name="call-and-response-your-first-bing-image-search-query-in-python"></a>Wywołania i odpowiedzi: pierwszego zapytania wyszukiwania usługi Bing obrazu w języku Python

Interfejsu API wyszukiwania usługi Bing obraz zawiera środowisko podobne do Bing.com/Images, umożliwiając Wysyłanie zapytania wyszukiwania użytkownika do usługi Bing i wrócić listę odpowiednich obrazów.

W tym przewodniku przedstawiono prosty przykład wywoływanie interfejsu API wyszukiwania usługi Bing obrazu i przetwarzania końcowego wynikowy obiekt JSON. Aby uzyskać więcej informacji, zobacz [dokumentacji wyszukiwania usługi Bing obrazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

W tym przykładzie można uruchomić jako notesu Jupyter na [MyBinder](https://mybinder.org) , klikając polecenie Uruchom integratora znaczków: 

[![Obiekt wiążący](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Wymagania wstępne

Musi mieć [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsy API wyszukiwania usługi Bing**. [Bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca dla tego przewodnika Szybki Start. Należy klucz dostępu podany przy wywołaniu metody aktywacji bezpłatną wersję próbną lub może używać klucza płatnej subskrypcji z pulpitu nawigacyjnego platformy Azure.

## <a name="running-the-walkthrough"></a>Uruchomiona przewodnika
Aby kontynuować z tym przewodnikiem, ustaw `subscription_key` do klucza interfejsu API dla usługi interfejsu API Bing.


```python
subscription_key = None
assert subscription_key
```

Następnie sprawdź, czy `search_url` punktu końcowego jest poprawna. W tym tylko jeden punkt końcowy jest używany do wyszukiwania usługi Bing interfejsów API. Jeśli wystąpią błędy autoryzacji dokładnie, czy ta wartość względem punktu końcowego wyszukiwania usługi Bing w pulpicie nawigacyjnym platformy Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Ustaw `search_term` do wyszukania obrazy Szczeniaki.


```python
search_term = "puppies"
```

Z poniższego bloku używa `requests` biblioteki w języku Python do wyróżnienia do wyszukiwania usługi Bing interfejsów API i zwrócenie wyników jako obiekt JSON. Sprawdź, czy jest przekazywana klucza interfejsu API za pomocą `headers` słownika i wyszukiwanie termin za pośrednictwem `params` słownika. Aby zapoznać się z pełną listą opcje, które mogą służyć do filtrowania wyników wyszukiwania, zapoznaj się [interfejsu API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) dokumentacji.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` Obiektu zawiera rzeczywiste obrazów, oraz rozbudowane metadane, takie jak powiązane elementy. Na przykład następujący wiersz kodu może wyodrębnić miniaturę wyniki pierwszych 16 adresów URL.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Następnie możemy użyć `PIL` biblioteki, aby pobrać obrazy miniatur i `matplotlib` biblioteki do renderowania je na siatce$ $4 \times 4.


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
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek aplikacji jednej strony wyszukiwania usługi Bing obrazu](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie wyszukiwania usługi Bing obrazu](../overview.md)  
[Wypróbuj](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Pobierz klucz bezpłatnej wersji próbnej dostępu](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Dokumentacja interfejsu API Bing obraz wyszukiwania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
