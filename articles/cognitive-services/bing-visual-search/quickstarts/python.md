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
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 7ec37b4c3bdeb924b3e35dbcb5d07a478611f631
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047130"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Szybki start: Uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i języka Python

Użyj tego przewodnika Szybki Start, aby wprowadzić pierwsze wywołanie interfejsu API wyszukiwania wizualnego Bing i przejrzysz wyniki. Ta aplikacja języka Python przekazuje obraz do interfejsu API i wyświetla informacje, które zwraca. Chociaż tej aplikacji został napisany w języku Python, interfejs API jest zgodny z większość języków programowania usługi sieci Web typu RESTful.

Podczas przekazywania lokalny obraz musi zawierać dane formularza `Content-Disposition` nagłówka. Należy ustawić jego `name` można ustawić parametru "image", a `filename` parametr dowolny ciąg. Zawartość formularza obejmują dane binarne obrazu. Rozmiar maksymalny obrazu, które można przekazać to 1 MB.

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

1. Utwórz nowy plik w języku Python w Twoim ulubionym środowiskiem IDE lub edytora i Dodaj następujący kod `import` instrukcji:

    ```python
    import requests, json
    ```

2. Utwórz zmienne swój klucz subskrypcji, punkt końcowy i ścieżkę do obrazu, który jest przekazywanie:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Tworzy obiekt słownika do przechowywania informacji nagłówka Twoje żądanie. Klucz subskrypcji należy powiązać ciąg `Ocp-Apim-Subscription-Key`, jak pokazano poniżej:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Utwórz inny słownik, który zawiera obraz, który jest otwarty i przekazane podczas wysyłania żądania:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analizowanie odpowiedzi w formacie JSON

1. Utwórz metodę o nazwie `print_json()` do wykonania w odpowiedzi interfejsu API i drukowania za pomocą pliku JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Wysyłanie żądania

1. Użyj polecenia `requests.post()`, aby wysłać żądanie do interfejsu API wyszukiwania wizualnego Bing. Dołącz ciąg do punktu końcowego, nagłówka i informacji o pliku. Drukuj `response.json()` z `print_json()`:

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z jednej strony wyszukiwania wizualnego](../tutorial-bing-visual-search-single-page-app.md)
