---
title: 'Szybki start: lista głosów zamiany tekstu na mowę, Node.js - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak uzyskać pełną listę głosów standardowych i neuronowych dla regionu/punktu końcowego przy użyciu node.js. Lista jest zwracana jako JSON, a dostępność głosu zależy od regionu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 9fe8bc06aafd17518d37c35034fac9b566e079ce
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261554"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Szybki start: wyświetlanie listy głosów zamiany tekstu na mowę za pomocą pliku Node.js

W tym przewodniku Szybki start dowiesz się, jak uzyskać pełną listę głosów standardowych i neuronowych dla regionu/punktu końcowego przy użyciu node.js. Lista jest zwracana jako JSON, a dostępność głosu zależy od regionu. Aby uzyskać listę obsługiwanych regionów, zobacz [regiony](regions.md).

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](get-started.md), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* <a href="https://nodejs.org/en/" target="_blank">Węzeł 8.12.x lub nowszy<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>lub ulubiony edytor tekstu
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Zdobądź jeden za darmo!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Tworzenie projektu i wymaganie zależności

Utwórz nowy projekt Node.js przy użyciu ulubionego IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `npm install request request-promise`.

## <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Interfejs API REST między tekstem na mowę wymaga tokenu dostępu do uwierzytelniania. Aby uzyskać token dostępu, wymagana jest wymiana. Ta funkcja wymienia klucz subskrypcji usługi mowy `issueToken` dla tokenu dostępu przy użyciu punktu końcowego.

W tym przykładzie przyjęto założenie, że subskrypcja usługi mowy znajduje się w regionie Zachodnie stany USA. Jeśli używasz innego regionu, zaktualizuj wartość dla `uri`. Aby uzyskać pełną listę, zobacz [Regiony](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Skopiuj ten kod do projektu:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [Uwierzytelnianie przy użyciu tokenu dostępu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

W następnej sekcji utworzymy funkcję, aby uzyskać listę głosów i zapisać dane wyjściowe JSON do pliku.

## <a name="make-a-request-and-save-the-response"></a>Złożyć wniosek i zapisać odpowiedź

Tutaj masz zamiar zbudować żądanie i zapisać listę zwróconych głosów. W tym przykładzie przyjęto założenie, że używasz punktu końcowego zachodniego us. Jeśli zasób jest zarejestrowany w innym regionie, należy zaktualizować plik `uri`. Aby uzyskać więcej informacji, zobacz [Regiony usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Następnie dodaj wymagane nagłówki dla żądania. Na koniec złożysz wniosek do usługi. Jeśli żądanie zakończy się pomyślnie i zwracany jest kod stanu 200, odpowiedź jest zapisywana w pliku.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To już prawie koniec. Ostatnim krokiem jest utworzenie funkcji asynchroniiowej. Ta funkcja odczytuje klucz subskrypcji ze zmiennej środowiskowej, pobierz token, poczekaj na zakończenie żądania, a następnie zapisz odpowiedź JSON do pliku.

Jeśli nie znasz zmiennych środowiskowych lub wolisz przetestować klucz subskrypcji zakodowany na stałe jako ciąg, zamień `process.env.SPEECH_SERVICE_KEY` klucz subskrypcji jako ciąg.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko. Teraz możesz uruchomić przykładową aplikację. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla platformy Node.js w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Zobacz też

* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Tworzenie niestandardowych czcionek głosowych](how-to-customize-voice-font.md)
* [Nagrywanie próbek głosu w celu utworzenia niestandardowego głosu](record-custom-voice-samples.md)
