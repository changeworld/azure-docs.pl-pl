---
title: 'Szybki start: Konwertowanie skryptu tekstowego, Ruby — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start przekonwertujesz tekst w jednym języku z danego systemu zapisu na inny przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator i języka Ruby.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
origin.date: 02/08/2019
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: 37ea719811d9b47dbe809278a0ad8f0bd9d25081
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60879161"
---
# <a name="quickstart-transliterate-text-with-the-translator-text-rest-api-ruby"></a>Szybki start: Transliteracja tekstu przy użyciu interfejsu API REST tłumaczenia tekstu w usłudze Translator (Ruby)

W tym przewodniku Szybki start przekonwertujesz tekst w jednym języku z danego systemu zapisu na inny przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten kod, potrzebne jest środowisko języka [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) lub jego nowsza wersja.

Aby korzystać z interfejsu API tłumaczenia tekstu w usłudze Translator, potrzebny jest również klucz subskrypcji. Zobacz [How to sign up for the Translator Text API (Jak zarejestrować się w interfejsie API tłumaczenia tekstu w usłudze Translator)](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Żądanie Transliterate

Następujący kod konwertuje tekst w jednym języku z danego systemu zapisu na inny przy użyciu metody [Transliterate](./reference/v3-0-transliterate.md).

1. Utwórz nowy projekt języka Ruby w ulubionym edytorze kodu.
2. Dodaj kod przedstawiony poniżej.
3. Zastąp wartość `key` kluczem dostępu właściwym dla Twojej subskrypcji.
4. Uruchom program.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'
region = 'your region'
host = 'https://api.translator.azure.cn'
path = '/transliterate?api-version=3.0'

# Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
params = "&language=ja&fromScript=jpan&toScript=latn";

uri = URI (host + path + params)

# Transliterate "good afternoon".
text = 'こんにちは'

content = '[{"Text" : "' + text + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['Ocp-Apim-Subscription-Region'] = region
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
```

## <a name="transliterate-response"></a>Odpowiedź na żądanie Transliterate

Po pomyślnym przetworzeniu żądania jest zwracana odpowiedź w formacie JSON, jak pokazano na poniższym przykładzie:

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z kodem przykładowym tego przewodnika Szybki start i innych, w tym obejmującym tłumaczenie i identyfikację języka, jak również innymi projektami przykładowymi dla tłumaczenia tekstu w usłudze Translator dostępnymi w usłudze GitHub.

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Ruby w usłudze GitHub](https://aka.ms/TranslatorGitHub?type=&language=ruby)

