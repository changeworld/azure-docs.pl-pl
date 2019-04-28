---
title: 'Szybki start: Wyszukiwanie wyrazów w słowniku dwujęzycznym, Python — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak można znaleźć alternatywne tłumaczenia i przykłady użycia dla określonego tekstu przy użyciu języka Python i interfejsu API REST tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 55cb9564205c99abc868413ebf43e575999198ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60879293"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-python"></a>Szybki start: wyszukiwanie wyrazów w słowniku dwujęzycznym za pomocą języka Python

W tym przewodniku Szybki start dowiesz się, jak można znaleźć alternatywne tłumaczenia i przykłady użycia dla określonego tekstu przy użyciu języka Python i interfejsu API REST tłumaczenia tekstu w usłudze Translator.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem tłumaczenia tekstu w usłudze Translator. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Środowisko Python 2.7.x lub 3.x
* Klucz subskrypcji platformy Azure na potrzeby tłumaczenia tekstu w usłudze Translator

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora lub Utwórz nowy folder na pulpicie. Skopiuj następujący fragment kodu do/folderu projektu do pliku o nazwie `dictionary-lookup.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
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
# below and add your subscription key. Then, be sure to delete your "os" import.
# subscriptionKey = 'put_your_key_here'
```

Globalny punkt końcowy interfejsu API tłumaczenia tekstu w usłudze Translator został ustawiony jako `base_url`. Element `path` ustawia trasę `dictionary/lookup` i określa, że chcemy korzystać z wersji 3 interfejsu API.

Elementy `params` służą do ustawiania języków źródłowych i wyjściowych. W tym przykładzie używamy języków angielskiego i hiszpańskiego: `en` i `es`.

>[!NOTE]
> Aby uzyskać więcej informacji na temat punktów końcowych, tras i parametrów żądania, zobacz [Translator Text API 3.0: wyszukiwanie w słowniku](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/dictionary/lookup?api-version=3.0'
params = '&from=en&to=es';
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

## <a name="create-a-request-to-find-alternate-translations"></a>Tworzenie żądania w celu znalezienia alternatywnych tłumaczeń

Zdefiniuj ciąg (lub ciągi), dla którego chcesz znaleźć tłumaczenia:

```python
# You can pass more than one object in body.
body = [{
    'text': 'Elephants'
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
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To wszystko. Utworzono prosty program, który będzie wywoływał interfejs API tłumaczenia tekstu w usłudze Translator i zwracał odpowiedź w formacie JSON. Teraz nadszedł czas, aby uruchomić program:

```console
python alt-translations.py
```

Jeśli chcesz porównać swój kod z naszym, kompletny przykład jest dostępny w witrynie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Przykładowa odpowiedź

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
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
* [Pobieranie listy obsługiwanych języków](quickstart-python-languages.md)
* [Określanie długości zdań na podstawie danych wejściowych](quickstart-python-sentences.md)
