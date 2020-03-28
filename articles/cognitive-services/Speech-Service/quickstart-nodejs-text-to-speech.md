---
title: 'Szybki start: konwertowanie tekstu na mowę, Node.js - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak konwertować tekst na mowę przy użyciu pliku Node.js i interfejsu API REST między tekstem a mową. Przykładowy tekst zawarty w tym przewodniku ma strukturę języka znaczników syntezy mowy (SSML). Dzięki temu można wybrać głos i język odpowiedzi mowy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b120acd25874585a744fb774aafe15d32d7baf08
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74976506"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Szybki start: konwertowanie tekstu na mowę przy użyciu pliku Node.js

W tym przewodniku Szybki start dowiesz się, jak konwertować tekst na mowę przy użyciu pliku Node.js i interfejsu API REST między tekstem a wiadomą. Treść żądania w tym przewodniku jest skonstruowana jako [język znaczników syntezy mowy (SSML),](speech-synthesis-markup.md)który pozwala wybrać głos i język odpowiedzi.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](get-started.md), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Środowisko [Node w wersji 8.12.x lub nowszej](https://nodejs.org/en/)
* Program [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) lub ulubiony edytor tekstów
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Zdobądź jeden za darmo!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Tworzenie projektu i wymaganie zależności

Utwórz nowy projekt Node.js przy użyciu ulubionego IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `tts.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `npm install request request-promise xmlbuilder readline-sync`.

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

W następnej sekcji utworzymy funkcję wywoływania interfejsu API zamiany tekstu na mowę i zapisywania syntetyzowanej odpowiedzi na mowę.

## <a name="make-a-request-and-save-the-response"></a>Złożyć wniosek i zapisać odpowiedź

W tym miejscu masz zamiar utworzyć żądanie do interfejsu API zamiany tekstu na mowę i zapisać odpowiedź mowy. W tym przykładzie przyjęto założenie, że używasz punktu końcowego zachodniego us. Jeśli zasób jest zarejestrowany w innym regionie, należy zaktualizować plik `uri`. Aby uzyskać więcej informacji, zobacz [Regiony usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Następnie należy dodać wymagane nagłówki dla żądania. Upewnij się, `User-Agent` że aktualizacja o nazwie zasobu (znajduje się `X-Microsoft-OutputFormat` w witrynie Azure portal) i ustawić preferowane dane wyjściowe audio. Aby uzyskać pełną listę formatów wyjściowych, zobacz [Wyjścia audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Następnie skonstruuj treść żądania przy użyciu języka znaczników syntezy mowy (SSML). Ten przykład definiuje strukturę i `text` używa danych wejściowych utworzonych wcześniej.

>[!NOTE]
> W tym przykładzie użyto czcionki głosowej. `JessaRUS` Aby uzyskać pełną listę głosów/języków dostarczonych przez firmę Microsoft, zobacz [Obsługa języków](language-support.md).
> Jeśli chcesz stworzyć unikalny, rozpoznawalny głos dla swojej marki, zobacz [Tworzenie niestandardowych czcionek głosowych.](how-to-customize-voice-font.md)

Na koniec złożysz wniosek do usługi. Jeśli żądanie zakończy się pomyślnie i zostanie zwrócony kod stanu 200, odpowiedź na mowę jest zapisywana jako `TTSOutput.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To już prawie koniec. Ostatnim krokiem jest utworzenie funkcji asynchroniiowej. Ta funkcja odczytuje klucz subskrypcji ze zmiennej środowiskowej, monituje o tekst, otrzymuje token, czeka na zakończenie żądania, a następnie konwertuje tekst na mowę i zapisuje dźwięk jako .wav.

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
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko, możesz uruchomić przykładową aplikację zamiany tekstu na mowę. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
node tts.js
```

Po wyświetleniu monitu wpisz wszystko, co chcesz przekonwertować z tekstu na mowę. Jeśli powiedzie się, plik mowy znajduje się w folderze projektu. Odtwórz go za pomocą ulubionego odtwarzacza multimedialnego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla platformy Node.js w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Zobacz też

* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Tworzenie niestandardowych czcionek głosowych](how-to-customize-voice-font.md)
* [Nagrywanie próbek głosu w celu utworzenia niestandardowego głosu](record-custom-voice-samples.md)
