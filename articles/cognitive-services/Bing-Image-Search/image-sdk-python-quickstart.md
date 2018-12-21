---
title: 'Szybki start: wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing dla języka Python'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki start umożliwia wyszukanie pierwszego obrazu przy użyciu zestawu SDK wyszukiwania obrazów Bing, który jest otoką dla interfejsu API i zawiera te same funkcje. Ta prosta aplikacja w języku Python wysyła zapytanie dotyczące wyszukania obrazu, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszego zwróconego obrazu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c0cb795f2d40963263cbbb15a54ba8af2953f5f0
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250369"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-python"></a>Szybki start: wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing dla języka Python

Ten przewodnik Szybki start umożliwia wyszukanie pierwszego obrazu przy użyciu zestawu SDK wyszukiwania obrazów Bing, który jest otoką dla interfejsu API i zawiera te same funkcje. Ta prosta aplikacja w języku Python wysyła zapytanie dotyczące wyszukania obrazu, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszego zwróconego obrazu.

Kod źródłowy dla tego przykładu jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) wraz z dodatkową obsługą błędów i adnotacjami.

## <a name="prerequisites"></a>Wymagania wstępne
Pobierz [klucz dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze **Wyszukiwanie**.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

* [Środowisko Python 2.7 lub 3.4](https://www.python.org/) lub nowsze.

* [Zestaw SDK wyszukiwania obrazów Bing](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) dla języka Python
    * Zainstaluj przy użyciu polecenia `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy skrypt w języku Python w ulubionym środowisku IDE lub edytorze z następującymi instrukcjami importu:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Utwórz zmienne dla klucza subskrypcji i szukanego terminu.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Tworzenie klienta wyszukiwania obrazów

3. Utwórz wystąpienie klasy `CognitiveServicesCredentials` i użyj go do utworzenia wystąpienia klienta:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
4. Wyślij zapytanie wyszukiwania do interfejsu API wyszukiwania obrazów Bing:
    ```python
    image_results = client.images.search(query=search_term)
    ```
## <a name="process-and-view-the-results"></a>Przetwarzanie i wyświetlanie wyników

Analizuj wyniki obrazów zwróconych w odpowiedzi.


Jeśli odpowiedź zawiera wyniki wyszukiwania, zapisz pierwszy wynik i wyświetl jego szczegóły, takie jak adres URL miniatury i oryginalny adres URL, wraz z całkowitą liczbą zwróconych obrazów.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Zobacz też

* [Czym jest funkcja wyszukiwania obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj interaktywny pokaz online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Pobieranie bezpłatnego klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Przykłady dla zestawu Azure Cognitive Services SDK w języku Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Dokumentacja usług Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
