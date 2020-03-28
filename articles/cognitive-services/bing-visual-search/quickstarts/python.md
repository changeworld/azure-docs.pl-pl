---
title: 'Szybki start: uzyskaj szczegółowe informacje o obrazach przy użyciu interfejsu API REST i pythona — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje na jego temat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: b56f6743b642904349797ac5b6167194f7916b45
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446586"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Szybki start: uchodź szczegółowe informacje o obrazach za pomocą interfejsu API REST wyszukiwania wizualnego Bing i języka Python

Ten szybki start umożliwia nawiąkczenie pierwszego połączenia z interfejsem API wyszukiwania wizualnego Bing i wyświetl wyniki. Ta aplikacja języka Python przekazuje obraz do interfejsu API i wyświetla informacje, które zwraca. Chociaż ta aplikacja jest napisana w języku Python, INTERFEJS API jest usługą sieci Web RESTful kompatybilną z większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicjowanie aplikacji

1. Utwórz nowy plik języka Python w ulubionym ide `import` lub edytorze i dodaj następującą instrukcję:

    ```python
    import requests, json
    ```

2. Utwórz zmienne dla klucza subskrypcji, punkt końcowy i ścieżkę do obrazu, który przekazujesz. `BASE_URI`może to być globalny punkt końcowy poniżej lub niestandardowy punkt końcowy [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlany w witrynie Azure portal dla zasobu:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
    Podczas przekazywania obrazu lokalnego dane formularza `Content-Disposition` muszą zawierać nagłówek. Należy ustawić `name` jego parametr na "image" `filename` i można ustawić parametr na dowolny ciąg. Zawartość formularza zawiera dane binarne obrazu. Maksymalny rozmiar obrazu, który można przesłać, to 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

3. Utwórz obiekt słownika, aby przechowywać informacje nagłówka żądania. Powiąż klucz `Ocp-Apim-Subscription-Key`subskrypcji z ciągiem , jak pokazano poniżej:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Utwórz inny słownik zawierający obraz, który jest otwierany i przesyłany podczas wysyłania żądania:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analizowanie odpowiedzi w formacie JSON

1. Utwórz metodę `print_json()` wywoływaną do podjęcia odpowiedzi interfejsu API i wydrukuj JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Wysyłanie żądania

1. Użyj polecenia `requests.post()`, aby wysłać żądanie do interfejsu API wyszukiwania wizualnego Bing. Dołącz ciąg do punktu końcowego, nagłówka i informacji o pliku. `response.json()` Drukowanie `print_json()`z:

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji sieci Web wyszukiwania wizualnego](../tutorial-bing-visual-search-single-page-app.md)
