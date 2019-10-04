---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c180a5d751b44346760706b1bd3d1e8acb70099d
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837517"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do projektu w pliku o nazwie `detect.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Jeśli te moduły nie były używane, należy je zainstalować przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie: `npm install request uuidv4`.

Te moduły są wymagane do skonstruowania żądania HTTP i utworzenia unikatowego identyfikatora dla nagłówka `'X-ClientTraceId'`.

## <a name="set-the-subscription-key-and-endpoint"></a>Ustawianie klucza subskrypcji i punktu końcowego

Ten przykład spróbuje odczytać klucz subskrypcji tłumaczenie tekstu w usłudze Translator i punkt końcowy z tych zmiennych środowiskowych: `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` i `TRANSLATOR_TEXT_ENDPOINT`. Jeśli nie znasz zmiennych środowiskowych, możesz ustawić `subscriptionKey` i `endpoint` jako ciągi i dodać komentarz do instrukcji warunkowych.

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

## <a name="configure-the-request"></a>Skonfiguruj żądanie

Metoda `request()`, dostępna za pośrednictwem modułu żądania, pozwala nam przekazać metodę HTTP, adres URL, parametry żądania, nagłówki i treść JSON jako obiekt `options`. W tym fragmencie kodu skonfigurujemy żądanie:

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
    url: 'detect',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Salve, mondo!'
    }],
    json: true,
};
```
Najprostszym sposobem na uwierzytelnienie żądania jest przekazanie klucza subskrypcji jako nagłówka `Ocp-Apim-Subscription-Key`, który jest używany w tym przykładzie. Alternatywnie możesz wymienić klucz subskrypcji dla tokenu dostępu i przekazać token dostępu razem jako nagłówek `Authorization`, aby sprawdzić poprawność żądania.

Jeśli używasz subskrypcji usługi Cognitive Services, musisz także uwzględnić w nagłówkach żądań `Ocp-Apim-Subscription-Region`.

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>Utwórz żądanie i wydrukuj odpowiedź

Następnie utworzymy żądanie przy użyciu metody `request()`. Przyjmuje obiekt `options` utworzony w poprzedniej sekcji jako pierwszy argument, a następnie drukuje odpowiedź prettified JSON.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> W tym przykładzie definiujemy żądanie HTTP w obiekcie `options`. Jednak moduł żądania obsługuje również wygodne metody, takie jak `.post` i `.get`. Aby uzyskać więcej informacji, zobacz [wygodne metody](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Umieść wszystko razem

Jest to również prosty program, który wywoła interfejs API tłumaczenia tekstu w usłudze Translator i zwróci odpowiedź JSON. Teraz czas na uruchomienie programu:

```console
node detect.js
```

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
