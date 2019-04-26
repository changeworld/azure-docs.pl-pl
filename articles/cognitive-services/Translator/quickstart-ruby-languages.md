---
title: 'Szybki start: Uzyskiwanie obsługiwanych języków, Ruby — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start uzyskasz listę języków obsługiwanych na potrzeby tłumaczenia, transliteracji, wyszukiwania w słowniku oraz wyszukiwania przykładów przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator i języka Ruby.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
origin.date: 02/08/2019
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: 1b92adcf528a1ccd00983e6c0dd952fefab5dd7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60509905"
---
# <a name="quickstart-get-supported-languages-with-the-translator-text-rest-api-ruby"></a>Szybki start: Uzyskiwanie obsługiwanych języków przy użyciu interfejsu API REST tłumaczenia tekstu w usłudze Translator (Ruby)

W tym przewodniku Szybki start uzyskasz listę języków obsługiwanych na potrzeby tłumaczenia, transliteracji, wyszukiwania w słowniku oraz wyszukiwania przykładów przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten kod, potrzebne jest środowisko języka [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) lub jego nowsza wersja.

## <a name="languages-request"></a>Żądanie Languages

Następujący kod umożliwia uzyskanie listy języków obsługiwanych na potrzeby tłumaczenia, transliteracji, wyszukiwania w słowniku oraz wyszukiwania przykładów przy użyciu metody [Languages](./reference/v3-0-languages.md).

1. Utwórz nowy projekt języka Ruby w ulubionym edytorze kodu.
2. Dodaj kod przedstawiony poniżej.
3. Uruchom program.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'

host = 'https://api.translator.azure.cn'
path = '/languages?api-version=3.0'

uri = URI (host + path)

request = Net::HTTP::Get.new(uri)

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))

output_path = 'output.txt'

# Write response to file
File.open(output_path, 'w' ) do |output|
    output.print json
end
```

## <a name="languages-response"></a>Odpowiedź na żądanie Languages

Po pomyślnym przetworzeniu żądania jest zwracana odpowiedź w formacie JSON, jak pokazano na poniższym przykładzie:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z kodem przykładowym z tego przewodnika Szybki start i innych, dotyczącym między innymi tłumaczenia i transliteracji, a także z innymi projektami przykładowymi dotyczącymi tłumaczenia tekstu w usłudze Translator i znajdującymi się w usłudze GitHub.

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Ruby w usłudze GitHub](https://aka.ms/TranslatorGitHub?type=&language=ruby)

