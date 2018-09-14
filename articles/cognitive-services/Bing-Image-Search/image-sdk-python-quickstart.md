---
title: 'Szybki Start: Wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing i języka Python'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start umożliwia wyszukiwanie i znajdowanie obrazów w sieci web przy użyciu zestawu SDK wyszukiwania obrazów Bing i języka Python.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 4a24f1e4e051b627034f1d4664e94e0f47c43014
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578297"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-python"></a>Szybki Start: Wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing i języka Python

Ten przewodnik Szybki Start umożliwia wyszukiwanie pierwsze obraz przy użyciu zestawu SDK wyszukiwania obrazów Bing, w jest otoką dla interfejsu API, która zawiera te same funkcje. Ta prosta aplikacja języka Python wysyła kwerendę wyszukiwania obrazów, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszy obraz zwracane.

Kod źródłowy dla tego przykładu jest dostępny [w serwisie Github](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) za pomocą obsługi dodatkowych błędów i adnotacji.

## <a name="prerequisites"></a>Wymagania wstępne 

* [Środowisko Python 2.7 lub 3.4](https://www.python.org/) lub nowszy.

* [Wyszukiwania obrazów platformy Azure zestaw SDK](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) dla języka Python
    * Instalowanie przy użyciu `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i Inicjowanie aplikacji

1. Utwórz nowy skrypt języka Python w Twoim ulubionym środowisku IDE lub edytora, a także następujące instrukcje importu:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Utwórz zmienne dla Twojego klucza i wyszukiwania okres obowiązywania subskrypcji.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Tworzenie obrazu klienta wyszukiwania

3. Utwórz wystąpienie obiektu `CognitiveServicesCredentials`i użyć go do utworzenia wystąpienia klienta:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
4. Wyślij zapytanie wyszukiwania do interfejsu API wyszukiwania obrazów Bing:
    ```python
    image_results = client.images.search(query=search_term)
    ```
## <a name="process-and-view-the-results"></a>Przetwarzanie i wyświetlić wyniki

Analizowanie wyników obraz zwrócona w odpowiedzi.


Jeśli odpowiedź zawiera wyniki wyszukiwania, przechowywania pierwszego wyniku i wydrukować jej szczegóły, takie jak miniatura URL, oryginalny adres URL wraz z całkowitą liczbą zwrócił obrazów.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący aplikacji jednostronicowej wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Zobacz także 

* [Co to jest wyszukiwanie obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj prezentację online dla interaktywne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Uzyskaj bezpłatne klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Przykłady zestawu SDK usługi Azure Cognitive Services w języku Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Dokumentacja usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)