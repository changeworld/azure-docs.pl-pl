---
title: Tłumaczenie tekstu w usłudze Translator — uzyskiwanie długości zdań przy użyciu języka Python | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: W tym przewodniku Szybki start określisz długości zdań w tekście przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator i języka Python w usługach Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 7b822dfa2db1b9d106d6d569fb26d9f6e8109c83
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "43770987"
---
# <a name="quickstart-get-sentence-lengths-with-python"></a>Szybki start: uzyskiwanie długości zdań w tekście przy użyciu języka Python

W tym przewodniku Szybki start odnajdziesz długość zdań w tekście przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten kod, potrzebne jest środowisko języka [Python 3.x](https://www.python.org/downloads/).

Aby korzystać z interfejsu API tłumaczenia tekstu w usłudze Translator, potrzebny jest również klucz subskrypcji. Zobacz [How to sign up for the Translator Text API (Jak zarejestrować się w interfejsie API tłumaczenia tekstu w usłudze Translator)](translator-text-how-to-signup.md).

## <a name="breaksentence-request"></a>Żądanie BreakSentence

Poniższy kod dzieli tekst źródłowy na zdania przy użyciu metody [BreakSentence](./reference/v3-0-break-sentence.md).

1. Utwórz nowy projekt języka Python w ulubionym edytorze kodu.
2. Dodaj kod przedstawiony poniżej.
3. Zastąp wartość `subscriptionKey` kluczem dostępu właściwym dla Twojej subskrypcji.
4. Uruchom program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, uuid, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/breaksentence?api-version=3.0'

params = ''

text = 'How are you? I am fine. What did you do today?'

def breakSentences (content):

    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': str(uuid.uuid4())
    }

    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path + params, content, headers)
    response = conn.getresponse ()
    return response.read ()

requestBody = [{
    'Text' : text,
}]
content = json.dumps(requestBody, ensure_ascii=False).encode('utf-8')
result = breakSentences (content)

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
output = json.dumps(json.loads(result), indent=4, ensure_ascii=False)

print (output)
```

## <a name="breaksentence-response"></a>Odpowiedź na żądanie BreakSentence

Po pomyślnym przetworzeniu żądania jest zwracana odpowiedź w formacie JSON, jak pokazano na poniższym przykładzie:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z kodem przykładowym z tego przewodnika Szybki start i innych, dotyczącym między innymi tłumaczenia i transliteracji, a także z innymi projektami przykładowymi dotyczącymi tłumaczenia tekstu w usłudze Translator i znajdującymi się w usłudze GitHub.

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Python w usłudze GitHub](https://aka.ms/TranslatorGitHub?type=&language=python)
