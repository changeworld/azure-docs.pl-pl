---
title: 'Szybki start: sprawdzanie pisowni za pomocą interfejsu API REST sprawdzania pisowni Bing i języka Python'
titlesuffix: Azure Cognitive Services
description: Rozpocznij korzystanie z interfejsu API REST sprawdzania pisowni Bing, aby sprawdzać pisownię i poprawność gramatyczną.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: e95006c6448bf1179d33bcd00c16d6e4246db148
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887322"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Szybki start: sprawdzanie pisowni za pomocą interfejsu API REST sprawdzania pisowni Bing i języka Python

Użyj tego przewodnika Szybki start, aby wykonać pierwsze wywołanie interfejsu API REST sprawdzania pisowni Bing. Ta prosta aplikacja w języku Python wysyła żądanie do interfejsu API i zwraca listę sugerowanych poprawek. Chociaż ta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy tej aplikacji jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko języka Python w wersji [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="initialize-the-application"></a>Inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następującą instrukcję importu.

   ```python
   import requests
   import json
   ```

2. Utwórz zmienne dla tekstu, w którym ma być sprawdzana pisownia, klucza subskrypcji oraz punktu końcowego sprawdzania pisowni Bing.

    ```python
    api_key = "enter-your-key-here"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Tworzenie parametrów dla żądania

1. Utwórz nowy słownik, w którym element `text` jest kluczem, a tekst — wartością.

    ```python
    data = {'text': example_text}
    ```

2. Dodaj parametry dla żądania. Ustaw wartość parametru `mkt` na swój rynek rynku, a wartość parametru `mode` na `proof`. 

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Dodaj nagłówek `Content-Type` i dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Wysyłanie żądania i odczytywanie odpowiedzi

1. Wyślij żądanie POST, używając biblioteki żądań.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Uzyskaj i wydrukuj odpowiedź w formacie JSON.
    
    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorials/spellcheck.md)

- [Czym jest interfejs API sprawdzania pisowni Bing?](../overview.md)
- [Dokumentacja interfejsu API sprawdzania pisowni Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
