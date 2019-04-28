---
title: 'Szybki start: Konwertowanie skryptu tekstowego, PHP — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start przekonwertujesz tekst w jednym języku z danego systemu zapisu na inny przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator i języka PHP.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
origin.date: 02/08/2019
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: a737b9ee73c567f397fda672a8ed7840f626de64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60879395"
---
# <a name="quickstart-transliterate-text-with-the-translator-text-rest-api-php"></a>Szybki start: Transliteracja tekstu przy użyciu interfejsu API REST tłumaczenia tekstu w usłudze Translator (PHP)

W tym przewodniku Szybki start przekonwertujesz tekst w jednym języku z danego systemu zapisu na inny przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten kod, potrzebne jest środowisko języka [PHP 5.6.x](https://php.net/downloads.php).

Aby korzystać z interfejsu API tłumaczenia tekstu w usłudze Translator, potrzebny jest również klucz subskrypcji. Zobacz [How to sign up for the Translator Text API (Jak zarejestrować się w interfejsie API tłumaczenia tekstu w usłudze Translator)](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Żądanie Transliterate

Następujący kod konwertuje tekst w jednym języku z danego systemu zapisu na inny przy użyciu metody [Transliterate](./reference/v3-0-transliterate.md).

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
$region = 'your region';
$host = "https://api.translator.azure.cn";
$path = "/transliterate?api-version=3.0";

// Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
$params = "&language=ja&fromScript=jpan&toScript=latn";

// Transliterate "good afternoon".
$text = "こんにちは";

if (!function_exists('com_create_guid')) {
  function com_create_guid() {
    return sprintf( '%04x%04x-%04x-%04x-%04x-%04x%04x%04x',
        mt_rand( 0, 0xffff ), mt_rand( 0, 0xffff ),
        mt_rand( 0, 0xffff ),
        mt_rand( 0, 0x0fff ) | 0x4000,
        mt_rand( 0, 0x3fff ) | 0x8000,
        mt_rand( 0, 0xffff ), mt_rand( 0, 0xffff ), mt_rand( 0, 0xffff )
    );
  }
}

function Transliterate ($host, $path, $key, $params, $content, $region) {

    $headers = "Content-type: application/json\r\n" .
        "Content-length: " . strlen($content) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n" .
        "Ocp-Apim-Subscription-Region: $region\r\n" .
        "X-ClientTraceId: " . com_create_guid() . "\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $content
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path . $params, false, $context);
    return $result;
}

$requestBody = array (
    array (
        'Text' => $text,
    ),
);
$content = json_encode($requestBody);

$result = Transliterate ($host, $path, $key, $params, $content, $region);

// Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
// We want to avoid escaping any Unicode characters that result contains. See:
// https://php.net/manual/en/function.json-encode.php
$json = json_encode(json_decode($result), JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
echo $json;
?>
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
> [Zapoznaj się z przykładami dla języka PHP w usłudze GitHub](https://aka.ms/TranslatorGitHub?type=&language=php)

