---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 4395e0a14819021bd1e4ae32c89ffb0cf8e07d00
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906502"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt przy użyciu ulubionego ŚRODOWISKA IDE lub `translate-text.js` edytora lub nowego folderu z plikiem o nazwie na pulpicie. Następnie skopiuj ten fragment kodu do projektu/pliku:

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `npm install request uuidv4`.

Te moduły są wymagane do utworzenia żądania HTTP i unikatowego identyfikatora dla nagłówka `'X-ClientTraceId'`.

## <a name="set-the-subscription-key-and-endpoint"></a>Ustawianie klucza subskrypcji i punktu końcowego

W tym przykładzie spróbuje odczytać klucz subskrypcji i `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` punkt `TRANSLATOR_TEXT_ENDPOINT`końcowy usługi Translator Text z następujących zmiennych środowiskowych: i . Jeśli nie znasz zmiennych środowiskowych, można `subscriptionKey` ustawić `endpoint` i jako ciągi i komentować instrukcje warunkowe.

Skopiuj ten kod do projektu:

```javascript
var key_var = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('Please set/export the following environment variable: ' + key_var);
}
var subscriptionKey = process.env[key_var];
var endpoint_var = 'TRANSLATOR_TEXT_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('Please set/export the following environment variable: ' + endpoint_var);
}
var endpoint = process.env[endpoint_var];
```

## <a name="configure-the-request"></a>Konfigurowanie żądania

Metoda `request()`, udostępniona przez moduł żądania, umożliwia nam przekazanie metody HTTP, parametrów żądania adresu URL, nagłówków i treści w formacie JSON w postaci obiektu `options`. W poniższym fragmencie kodu skonfigurujemy żądanie:

>[!NOTE]
> Aby uzyskać więcej informacji na temat punktów końcowych, tras i parametrów żądania, zobacz [Translator Text API 3.0: Transliterate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate) (Interfejs API 3.0 tłumaczenia tekstu w usłudze Translator: transliteracja).

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
    url: 'transliterate',
    qs: {
      'api-version': '3.0',
      'language': 'ja',
      'fromScript': 'jpan',
      'toScript': 'latn'
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'こんにちは'
    }],
    json: true,
};
```

Najprostszym sposobem uwierzytelniania żądania jest przekazanie klucza subskrypcji jako nagłówka `Ocp-Apim-Subscription-Key`. Ta metoda jest używana w tym przykładzie. Alternatywnie można wymienić klucz subskrypcji na token dostępu i przekazać go dalej jako nagłówek `Authorization` w celu zweryfikowania żądania.

Jeśli korzystasz z subskrypcji wielu usług usług Cognitive Services, należy również uwzględnić `Ocp-Apim-Subscription-Region` w nagłówkach żądań.

Aby uzyskać więcej informacji, zobacz [Authentication](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) (Uwierzytelnianie).

## <a name="make-the-request-and-print-the-response"></a>Wysyłanie żądania i wyświetlanie odpowiedzi

Następnie utworzymy żądanie przy użyciu metody `request()`. Pobiera ona obiekt `options` utworzony w poprzedniej sekcji jako pierwszy argument, a następnie wyświetla ulepszoną odpowiedź w formacie JSON.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> W tym przykładzie definiujemy żądanie HTTP w obiekcie `options`. Jednak moduł żądania obsługuje również metody ułatwiające, takie jak `.post` i `.get`. Aby uzyskać więcej informacji, zobacz [Convenience methods](https://github.com/request/request#convenience-methods) (Metody ułatwiające).

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To wszystko. Utworzono prosty program, który będzie wywoływał interfejs API tłumaczenia tekstu w usłudze Translator i zwracał odpowiedź w formacie JSON. Teraz nadszedł czas, aby uruchomić program:

```console
node transliterate-text.js
```

Jeśli chcesz porównać swój kod z naszym, kompletny przykład jest dostępny w witrynie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Przykładowa odpowiedź

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli klucz subskrypcji umieszczono na stałe w kodzie programu, pamiętaj, aby usunąć ten klucz subskrypcji po zakończeniu pracy z przewodnikiem Szybki start.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z odwołaniem do interfejsu API, aby zrozumieć wszystko, co można zrobić z interfejsem API tekstu translatora.

> [!div class="nextstepaction"]
> [Odwołanie API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
