---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 6591fd6eb232bf5fb242c9e08830324f864dac2f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837556"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt w języku Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do projektu w pliku o nazwie `detect.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Jeśli te moduły nie były używane, należy je zainstalować przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie: `pip install requests uuid`.

Pierwszy komentarz informuje interpreter języka Python o użyciu kodowania UTF-8. Następnie wymagane moduły są importowane w celu odczytania klucza subskrypcji ze zmiennej środowiskowej, skonstruowania żądania HTTP, utworzenia unikatowego identyfikatora i obsługi odpowiedzi JSON zwracanej przez interfejs API tłumaczenia tekstu w usłudze Translator.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Ustawianie klucza subskrypcji, punktu końcowego i ścieżki

Ten przykład spróbuje odczytać klucz subskrypcji tłumaczenie tekstu w usłudze Translator i punkt końcowy ze zmiennych środowiskowych: `TRANSLATOR_TEXT_KEY` i `TRANSLATOR_TEXT_ENDPOINT`. Jeśli nie znasz zmiennych środowiskowych, możesz ustawić `subscription_key` i `endpoint` jako ciągi i dodać komentarz do instrukcji warunkowych.

Skopiuj ten kod do projektu:

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Globalny punkt końcowy tłumaczenie tekstu w usłudze Translator jest ustawiony jako `endpoint`. `path` ustawia trasę `detect` i określa, że chcemy trafić w wersję 3 interfejsu API.

>[!NOTE]
> Aby uzyskać więcej informacji na temat punktów końcowych, tras i parametrów żądania, zobacz [interfejs API tłumaczenia tekstu w usłudze Translator 3,0: Detection](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect).

```python
path = '/detect?api-version=3.0'
constructed_url = endpoint + path
```

## <a name="add-headers"></a>Dodaj nagłówki

Najprostszym sposobem na uwierzytelnienie żądania jest przekazanie klucza subskrypcji jako nagłówka `Ocp-Apim-Subscription-Key`, który jest używany w tym przykładzie. Alternatywnie możesz wymienić klucz subskrypcji dla tokenu dostępu i przekazać token dostępu razem jako nagłówek `Authorization`, aby sprawdzić poprawność żądania. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Skopiuj ten fragment kodu do projektu:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Jeśli używasz subskrypcji usługi Cognitive Services, musisz także uwzględnić w parametrach żądania `Ocp-Apim-Subscription-Region`. [Dowiedz się więcej o uwierzytelnianiu w ramach subskrypcji wielu usług](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-detect-text-language"></a>Tworzenie żądania wykrywania języka tekstu

Zdefiniuj ciąg (lub ciągi), dla którego chcesz wykryć język:

```python
# You can pass more than one object in body.
body = [{
    'text': 'Salve, mondo!'
}]
```

Następnie utworzymy żądanie POST przy użyciu modułu `requests`. Przyjmuje trzy argumenty: połączony adres URL, nagłówki żądań i treść żądania:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Drukuj odpowiedź

Ostatnim krokiem jest drukowanie wyników. Ten fragment kodu prettifies wyniki, sortując klucze, ustawiając wcięcia i deklarując separatory elementów i kluczy.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Umieść wszystko razem

Jest to również prosty program, który wywoła interfejs API tłumaczenia tekstu w usłudze Translator i zwróci odpowiedź JSON. Teraz czas na uruchomienie programu:

```console
python detect.py
```

Jeśli chcesz porównać swój kod z naszą usługą, kompletny przykład jest dostępny w witrynie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Przykładowa odpowiedź

Po uruchomieniu przykładu na terminalu powinny zostać wyświetlone następujące elementy:

> [!NOTE]
> Znajdź skrót kraju/regionu na [liście języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "pt",
                "score": 1.0
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "en",
                "score": 1.0
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "it",
        "score": 1.0
    }
]
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli stałe klucz subskrypcji do programu, pamiętaj o usunięciu klucza subskrypcji po zakończeniu pracy z tym przewodnikiem Szybki Start.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API, aby zrozumieć wszystko, co można zrobić za pomocą interfejs API tłumaczenia tekstu w usłudze Translator.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
