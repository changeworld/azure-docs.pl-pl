---
title: 'Szybki start: uzyskiwanie długości zdań, Python — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak określać długość zdań (w znakach) przy użyciu języka Python i interfejsu API REST tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 2aede18f3b093f52a205e053c72cac5a2e6e6b1b
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357901"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-python"></a>Szybki start: korzystanie z interfejsu API tłumaczenia tekstu w usłudze Translator do określania długości zdań przy użyciu języka Python

W tym przewodniku Szybki start dowiesz się, jak określać długość zdań (w znakach) przy użyciu języka Python i interfejsu API REST tłumaczenia tekstu w usłudze Translator.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem tłumaczenia tekstu w usłudze Translator. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Środowisko Python 2.7.x lub 3.x
* Klucz subskrypcji platformy Azure na potrzeby tłumaczenia tekstu w usłudze Translator

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `sentence-length.py`.

```python
# -*- coding: utf-8 -*-
import os
import requests
import uuid
import json
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `pip install requests uuid`.

Pierwszy komentarz informuje interpreter języka Python, aby używać kodowania UTF-8. Następnie wymagane moduły są importowane w celu odczytania klucza subskrypcji ze zmiennej środowiskowej, skonstruowania żądania http, utworzenia unikatowego identyfikatora i obsłużenia odpowiedzi JSON zwracanej przez interfejs API tłumaczenia tekstu w usłudze Translator.

## <a name="set-the-subscription-key-base-url-and-path"></a>Ustawianie klucza subskrypcji, podstawowego adresu URL i ścieżki

Ten przykładowy kod spróbuje odczytać klucz subskrypcji na potrzeby tłumaczenia tekstu w usłudze Translator ze zmiennej środowiskowej `TRANSLATOR_TEXT_KEY`. Jeśli nie chcesz korzystać ze zmiennych środowiskowych, możesz ustawić element `subscriptionKey` jako ciąg i oznaczyć instrukcję warunkową jako komentarz.

Skopiuj ten kod do projektu:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

Globalny punkt końcowy interfejsu API tłumaczenia tekstu w usłudze Translator został ustawiony jako `base_url`. Element `path` ustawia trasę `breaksentence` i określa, że chcemy korzystać z wersji 3 interfejsu API.

`params` w tym przykładzie służą do ustawiania języka dostarczonego tekstu. `params` nie są wymagane dla trasy `breaksentence`. Jeśli żądanie nie zawiera tych informacji, interfejs API spróbuje wykryć język dostarczonego tekstu i w odpowiedzi poda te informacje wraz ze współczynnikiem ufności.

>[!NOTE]
> Aby uzyskać więcej informacji na temat punktów końcowych, tras i parametrów żądania, zobacz [Translator Text API 3.0: Languages](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) (Interfejs API 3.0 tłumaczenia tekstu w usłudze Translator: języki).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/breaksentence?api-version=3.0'
params = '&language=en'
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>Dodawanie nagłówków

Najprostszym sposobem uwierzytelniania żądania jest przekazanie klucza subskrypcji jako nagłówka `Ocp-Apim-Subscription-Key`. Ta metoda jest używana w tym przykładzie. Alternatywnie można wymienić klucz subskrypcji na token dostępu i przekazać go dalej jako nagłówek `Authorization` w celu zweryfikowania żądania. Aby uzyskać więcej informacji, zobacz [Authentication](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) (Uwierzytelnianie).

Skopiuj ten fragment kod do projektu:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-determine-sentence-length"></a>Tworzenie żądania w celu określenia długości zdania

Zdefiniuj zdanie (lub zdania), których długość chcesz określić:

```python
# You can pass more than one object in body.
body = [{
    'text': 'How are you? I am fine. What did you do today?'
}]
```

Następnie utworzymy żądanie POST przy użyciu modułu `requests`. Przyjmuje on trzy argumenty: połączony adres URL, nagłówki żądania i treść żądania:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Wyświetlanie odpowiedzi

Ostatnim krokiem jest wyświetlenie wyników. Ten fragment kodu ulepsza wyniki, sortując klucze, ustawiając wcięcia i deklarując separatory elementów i kluczy.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To wszystko. Utworzono prosty program, który będzie wywoływał interfejs API tłumaczenia tekstu w usłudze Translator i zwracał odpowiedź w formacie JSON. Teraz nadszedł czas, aby uruchomić program:

```console
python sentence-length.py
```

Jeśli chcesz porównać swój kod z naszym, kompletny przykład jest dostępny w witrynie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Przykładowa odpowiedź

```json
[
    {
        "sentLen": [
            13,
            11,
            22
        ]
    }
]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli klucz subskrypcji umieszczono na stałe w kodzie programu, pamiętaj, aby usunąć ten klucz subskrypcji po zakończeniu pracy z przewodnikiem Szybki start.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Python w usłudze GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Zobacz także

Dowiedz się, jak używać interfejsu API tłumaczenia tekstu w usłudze Translator w następujących celach:

* [Tłumaczenie tekstu](quickstart-python-translate.md)
* [Transliteracja tekstu](quickstart-python-transliterate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-python-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-python-dictionary.md)
* [Pobieranie listy obsługiwanych języków](quickstart-python-languages.md)
