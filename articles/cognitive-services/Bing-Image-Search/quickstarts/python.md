---
title: 'Szybki Start: Wysyłanie zapytania przy użyciu interfejsu API REST dla interfejsu API wyszukiwania obrazów Bing w języku Python'
description: W tym przewodniku Szybki Start możesz wysyłać zapytania wyszukiwania interfejsu API wyszukiwania Bing w celu uzyskania listy odpowiednie obrazy przy użyciu języka Python.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: bc527ba39b580935f113f56aa63f7bdd283ba304
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "41987533"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Szybki Start: Wysyłanie zapytania przy użyciu interfejsu API REST i Python

Interfejs API wyszukiwania obrazów Bing udostępnia środowisko podobne do Bing.com/Images, umożliwiając Wysyłanie zapytania wyszukiwania użytkowników do usługi Bing i uzyskanie listy odpowiednie obrazy.

W tym instruktażu przedstawiono prosty przykład wywoływanie interfejsu API wyszukiwania obrazów Bing i przetwarzanie końcowe wynikowy obiekt JSON. Aby uzyskać więcej informacji, zobacz [dokumentacji wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

W tym przykładzie można uruchomić jako notesu programu Jupyter na [MyBinder](https://mybinder.org) , klikając polecenie Uruchom integratora znaczków: 

[![Obiekt wiążący](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-walkthrough"></a>Uruchamianie przewodnik
Aby kontynuować z tym przewodnikiem, należy ustawić `subscription_key` klucza interfejsu API dla usługi interfejsu API Bing.


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

Następnie użyjemy `PIL` biblioteki, aby pobrać obrazy miniatur i `matplotlib` biblioteki do renderowania je na siatce$ \times 4 4 USD.


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
> [Samouczek dotyczący aplikacji jednostronicowej wyszukiwania obrazów Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także 

[Przegląd wyszukiwania obrazów Bing](../overview.md)  
[Wypróbuj](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Pobierz klucz bezpłatny dostęp próbny](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
