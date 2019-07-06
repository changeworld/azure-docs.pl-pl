---
title: 'Szybki start: Konwertuj tekst na mowę, środowiska Node.js — usługi mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start dowiesz się, jak konwertować zamiany tekstu na mowę przy użyciu środowiska Node.js i interfejsu API REST zamiany tekstu na mowę. Przykładowy tekst uwzględnione w tym przewodniku mają strukturę jako język znaczników synteza mowy (SSML). Dzięki temu możliwe jest wybranie głos i język odpowiedzi mowy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 5a218db0527a5e1d5642cb485b75df894a275764
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604998"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Szybki start: Konwertowanie tekstu na mowę, przy użyciu środowiska Node.js

W tym przewodniku Szybki Start dowiesz się, jak konwertować zamiany tekstu na mowę przy użyciu środowiska Node.js i zamiany tekstu na mowę interfejsu API REST. Ma strukturę treści żądania, w tym przewodniku [mowy syntezy Markup Language (SSML)](speech-synthesis-markup.md), co pozwala wybrać głos i język w odpowiedzi.

Ten przewodnik Szybki Start wymaga [konta usług Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](get-started.md), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Środowisko [Node w wersji 8.12.x lub nowszej](https://nodejs.org/en/)
* Program [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) lub ulubiony edytor tekstów
* Klucz subskrypcji platformy Azure dla usług przetwarzania mowy. [Bezpłatnie Uzyskaj ją! ](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Tworzenie projektu i wymagają zależności

Utwórz nowy projekt Node.js przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `tts.js`.

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

Zamiany tekstu na mowę interfejsu API REST wymaga tokenu dostępu do uwierzytelniania. Aby uzyskać token dostępu, wymagana jest wymiana. Ta funkcja wymienia klucz subskrypcji usług przetwarzania mowy, aby uzyskać dostęp do tokenu przy użyciu `issueToken` punktu końcowego.

W tym przykładzie przyjęto założenie, że subskrypcji usług przetwarzania mowy znajduje się w regionie zachodnie stany USA. Jeśli używasz innego regionu, zaktualizuj wartość `uri`. Aby uzyskać pełną listę, zobacz [regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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
> Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [uwierzytelnienia przy użyciu tokenu dostępu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

W następnej sekcji utworzymy funkcja do wywołania interfejsu API zamiany tekstu na mowę i zapisywanie odpowiedzi syntezatora mowy.

## <a name="make-a-request-and-save-the-response"></a>Tworzenie żądania i zapisać odpowiedź

W tym miejscu możesz zacząć tworzenie żądań do interfejsu API zamiany tekstu na mowę i zapisywanie odpowiedzi mowy. W tym przykładzie przyjęto założenie, że używasz punktu końcowego zachodnie stany USA. Jeśli zasób jest zarejestrowany w innym regionie, pamiętaj o zaktualizowaniu `uri`. Aby uzyskać więcej informacji, zobacz [regionów usług przetwarzania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Następnie należy dodać wymagane nagłówki żądania. Upewnij się, że aktualizujesz `User-Agent` nazwą zasobu (znajdujący się w witrynie Azure portal) i ustaw `X-Microsoft-OutputFormat` do preferowanego danych wyjściowych audio. Aby uzyskać pełną listę formatów danych wyjściowych, zobacz [danych wyjściowych Audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Następnie konstruować treści żądania, za pomocą mowy syntezy Markup Language (SSML). W tym przykładzie definiuje strukturę i używa `text` dane wejściowe została utworzona wcześniej.

>[!NOTE]
> W tym przykładzie użyto `JessaRUS` czcionka głosowa. Aby uzyskać pełną listę Microsoft podany głosów/języków, zobacz [języki](language-support.md).
> Jeśli interesuje Cię tworzenie unikatowy, rozpoznawalny głos na marki, zobacz [tworzenia czcionki głosowe niestandardowe](how-to-customize-voice-font.md).

Na koniec wprowadzisz żądania do usługi. Jeśli żądanie zakończy się pomyślnie i zostanie zwrócony kod stanu 200, odpowiedź mowy jest zapisywany jako `TTSOutput.wav`.

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

To już prawie koniec. Ostatnim krokiem jest, aby utworzyć funkcję asynchroniczną. Ta funkcja będzie odczytywać Twój klucz subskrypcji ze zmiennej środowiskowej, wybór opcji Monituj o tekstu, uzyskać token, poczekaj, aż żądanie zakończyć, a następnie Konwertuj tekst na mowę i zapisać audio jako wav.

Jeśli masz doświadczenia w pracy ze zmiennymi środowiskowymi lub wolisz przetestuj na Twojej subskrypcji klucza zapisane na stałe jako ciąg znaków, należy zastąpić `process.env.SPEECH_SERVICE_KEY` z kluczem subskrypcji jako ciąg.

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

To wszystko, wszystko jest gotowe do uruchomienia zamiany tekstu na mowę przykładowej aplikacji. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
node tts.js
```

Po wyświetleniu monitu wpisz dowolną chcesz przekonwertować z zamiany tekstu na mowę. Jeśli to się powiedzie, plik mowy znajduje się w folderze projektu. Odtwarzać je za pomocą odtwarzacza media ulubionych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla platformy Node.js w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Zobacz także

* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Tworzenie niestandardowych voice czcionek](how-to-customize-voice-font.md)
* [Próbki głosu rekord do utworzenia niestandardowych voice](record-custom-voice-samples.md)
