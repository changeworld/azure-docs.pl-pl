---
title: Tłumaczenie tekstu w usłudze Translator — uzyskiwanie obsługiwanych języków przy użyciu języka PHP | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: W tym przewodniku Szybki start uzyskasz listę języków obsługiwanych na potrzeby tłumaczenia, transliteracji, wyszukiwania w słowniku oraz wyszukiwania przykładów przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator i języka PHP w usługach Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: nolachar
ms.openlocfilehash: 95b48f15ffe8cea14f9ffb7612193b819e03f5f3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "43771075"
---
# <a name="quickstart-get-supported-languages-with-php"></a>Szybki start: uzyskiwanie obsługiwanych języków w języku PHP

W tym przewodniku Szybki start uzyskasz listę języków obsługiwanych na potrzeby tłumaczenia, transliteracji, wyszukiwania w słowniku oraz wyszukiwania przykładów przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten kod, potrzebne jest środowisko języka [PHP 5.6.x](http://php.net/downloads.php).

Aby korzystać z interfejsu API tłumaczenia tekstu w usłudze Translator, potrzebny jest również klucz subskrypcji. Zobacz [How to sign up for the Translator Text API (Jak zarejestrować się w interfejsie API tłumaczenia tekstu w usłudze Translator)](translator-text-how-to-signup.md).

## <a name="languages-request"></a>Żądanie Languages

Następujący kod umożliwia uzyskanie listy języków obsługiwanych na potrzeby tłumaczenia, transliteracji, wyszukiwania w słowniku oraz wyszukiwania przykładów przy użyciu metody [Languages](./reference/v3-0-languages.md).

1. Utwórz nowy projekt języka PHP w ulubionym edytorze kodu.
2. Dodaj kod przedstawiony poniżej.
3. Zastąp wartość `key` kluczem dostępu właściwym dla Twojej subskrypcji.
4. Uruchom program.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
$key = 'ENTER KEY HERE';

$host = "https://api.cognitive.microsofttranslator.com";
$path = "/languages?api-version=3.0";

$output_path = "output.txt";

function GetLanguages ($host, $path, $key) {

    $headers = "Content-type: text/xml\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // http://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'GET'
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$result = GetLanguages ($host, $path, $key);

// Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
// We want to avoid escaping any Unicode characters that result contains. See:
// http://php.net/manual/en/function.json-encode.php
$json = json_encode(json_decode($result), JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
$out = fopen($output_path, 'w');
fwrite($out, $json);
fclose($out);
?>
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
> [Zapoznaj się z przykładami dla języka PHP w usłudze GitHub](https://aka.ms/TranslatorGitHub?type=&language=php)
