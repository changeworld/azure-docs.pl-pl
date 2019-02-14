---
title: 'Szybki start: Uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i języka Python'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje na jego temat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 873da64592c2c2e925d8731d4b1154db95bed31d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863231"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Szybki start: Pierwsze zapytanie wyszukiwania wizualnego Bing w języku Python

Ten przewodnik Szybki start umożliwia utworzenie pierwszego wywołania interfejsu API wyszukiwania wizualnego Bing i wyświetlenie wyników wyszukiwania. Ta prosta aplikacja JavaScript przekazuje obraz do interfejsu API i wyświetla zwrócone informacje na jego temat. Aplikacja jest napisana w języku JavaScript, natomiast interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Podczas przekazywania lokalnego obrazu dane formularza POST muszą zawierać nagłówek Content-Disposition. Jego parametr `name` musi mieć wartość "image", a parametr `filename` może być ustawiony na dowolny ciąg. Zawartością formularza jest plik binarny obrazu. Maksymalny rozmiar obrazu, który można przekazać, wynosi 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://www.python.org/)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następującą instrukcję importu.

    ```python
    import requests, json
    ```

2. Utwórz zmienne dla klucza subskrypcji, punkt końcowy i ścieżkę do obrazu, który przekazujesz.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Utwórz obiekt słownika do przechowywania informacji nagłówka swoich żądań. Powiąż klucz subskrypcji z ciągiem `Ocp-Apim-Subscription-Key`, jak pokazano poniżej.

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Utwórz kolejny słownik, zawierający obraz, który zostanie otwarty i przekazany po wysłaniu żądania. 

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analizowanie odpowiedzi w formacie JSON

1. Utwórz metodę o nazwie `print_json()` do generowania odpowiedzi interfejsu API i drukowania pliku JSON.

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Wysyłanie żądania

1. Użyj polecenia `requests.post()`, aby wysłać żądanie do interfejsu API wyszukiwania wizualnego Bing. Dołącz ciąg do punktu końcowego, nagłówka i informacji o pliku. Drukowanie `response.json()` za pomocą `print_json()`

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
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](../tutorial-bing-visual-search-single-page-app.md)
