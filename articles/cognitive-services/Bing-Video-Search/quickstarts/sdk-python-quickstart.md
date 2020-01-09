---
title: 'Szybki Start: wyszukiwanie filmów wideo przy użyciu zestawu SDK dla języka Python — wyszukiwanie wideo Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wysyłać żądania wyszukiwania wideo za pomocą zestawu SDK wyszukiwania wideo Bing dla języka Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: c1afea4e6cacc1f6e9ac84ca20e638836540396e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448389"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>Szybki Start: wykonywanie wyszukiwania wideo przy użyciu zestawu SDK wyszukiwanie wideo Bing dla języka Python

Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości za pomocą zestawu SDK wyszukiwania wiadomości Bing dla języka Python. Chociaż wyszukiwanie wideo Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe integrowanie usługi z Twoimi aplikacjami. Kod źródłowy dla tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) z dodatkowymi adnotacjami i funkcjami.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Środowisko [Python](https://www.python.org/) 2.x lub 3.x
- Zestaw SDK wyszukiwania wideo Bing dla języka Python

Zalecane jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html) języka Python. Środowisko wirtualne możesz zainstalować i zainicjować przy użyciu [modułu venv](https://pypi.python.org/pypi/virtualenv). Zainstaluj moduł virtualenv dla środowiska Python w wersji 2.7 za pomocą następującego polecenia:

```console
python -m venv mytestenv
```

Zainstaluj zestaw SDK wyszukiwania wideo Bing za pomocą następującego polecenia:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następujące instrukcje importu. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Utwórz zmienną dla klucza subskrypcji. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>Tworzenie klienta wyszukiwania

Utwórz wystąpienie obiektu `CognitiveServicesCredentials` i utwórz wystąpienie klienta:

```python
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Wysyłanie żądania wyszukiwania i odbieranie odpowiedzi

1. Użyj funkcji `client.videos.search()` na potrzeby zapytania wyszukiwania, aby wysłać żądanie do interfejsu API wyszukiwania wideo Bing i uzyskać odpowiedź.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Jeśli odpowiedź zawiera wyniki wyszukiwania, pobierz pierwszy z nich i wyświetl jego identyfikator, nazwę i adres URL.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie internetowej aplikacji jednostronicowej](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także 

- [Co to jest interfejs API wyszukiwania wideo Bing?](../overview.md)
- [Cognitive services .NET SDK samples (Przykłady zestawów SKD .NET usług Cognitive Services)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
