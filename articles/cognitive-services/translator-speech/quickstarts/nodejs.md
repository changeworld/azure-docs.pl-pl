---
title: 'Szybki start: Interfejs API tłumaczenia mowy w usłudze Translator dla oprogramowania Node.js'
titlesuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API tłumaczenia mowy w usłudze Translator.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 3/5/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fc5c6fe4040a8f4140cd18ff41373d50f74a6a55
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965412"
---
# <a name="quickstart-translator-speech-api-with-nodejs"></a>Szybki start: Interfejs API tłumaczenia mowy w usłudze Translator dla oprogramowania Node.js
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

W tym artykule pokazano, jak używać interfejsu API tłumaczenia mowy w usłudze Translator do tłumaczenia wypowiedzi z pliku wav.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten kod, potrzebne jest środowisko [Node.js 6](https://nodejs.org/en/download/).

Konieczne też będzie zainstalowanie [pakietu Websocket ](https://www.npmjs.com/package/websocket) dla środowiska Node.js.

Plik wav o nazwie „speak.wav” musi znajdować się w tym samym folderze, co plik wykonywalny, który skompilujesz przy użyciu poniższego kodu. Ten plik wav powinien być plikiem zapisanym w standardzie PCM, o 16-bitowej rozdzielczości, częstotliwości 16 kHz, w formacie mono. Taki plik wav można uzyskać z tematu [Interfejs API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Musisz mieć również [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsem API tłumaczenia mowy w usłudze Microsoft Translator**. Potrzebujesz klucza płatnej subskrypcji dostępnego na [pulpicie nawigacyjnym platformy Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Tłumaczenie mowy

Poniższy kod tłumaczy mowę z jednego języka na inny.

1. Utwórz nowy projekt środowiska Node.js w ulubionym środowisku IDE.
2. Dodaj kod przedstawiony poniżej.
3. Zastąp wartość `key` kluczem dostępu właściwym dla Twojej subskrypcji.
4. Uruchom program.

```nodejs
/* To install this dependency, run:
npm install websocket
*/
var wsClient = require('websocket').client;
var fs = require('fs');
/* To install this dependency, run:
npm install stream-buffers
*/
var streamBuffers = require('stream-buffers');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let key = 'ENTER KEY HERE';

let host = 'wss://dev.microsofttranslator.com';
let path = '/speech/translate';
let params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa';
let uri = host + path + params;

/* The input .wav file is in PCM 16bit, 16kHz, mono format.
You can obtain such a .wav file using the Text to Speech API. See:
https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis
*/
let input_path = 'speak.wav';

let output_path = 'speak2.wav';

function receive(message) {
    console.log ("Received message of type: " + message.type + ".");

    if (message.type == 'utf8') {
        var result = JSON.parse(message.utf8Data)
        console.log("Response type: " + result.type + ".");
        console.log("Recognized input as: " + result.recognition);
        console.log("Translation: " + result.translation);
    }
    else if (message.type == 'binary') {
/* This is needed to make sure message.binaryData is defined. See:
https://stackoverflow.com/questions/17546953/cant-access-object-property-even-though-it-exists-returns-undefined
*/
        let binary_data = JSON.parse (JSON.stringify (message.binaryData));

        if (binary_data.type == 'Buffer') {
/* Put the binary data in a Buffer - do not write it directly to the file. */
            let buffer = new Buffer(binary_data.data, 'binary');
/* Write the contents of the Buffer to the file. */
            fs.writeFile (output_path, buffer, 'binary', function (error) {
/* fs.writeFile calls the error-handling function even if there is no error, in which case error === null. See:
https://stackoverflow.com/questions/44473868/node-js-fs-writefile-err-returns-null
*/
                if (error !== null) {
                    console.log ("Error: " + error);
                }
            });
        }
    }
}

function send(connection, filename) {

    var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
        frequency: 100,
        chunkSize: 32000
    });

/* Make sure the audio file is followed by silence.
This lets the service know that the audio file is finished.
At 32 bytes per millisecond, this is 100 seconds of silence. */
    myReadableStreamBuffer.put(fs.readFileSync(filename));
    myReadableStreamBuffer.put(new Buffer(3200000));
    myReadableStreamBuffer.stop();

    myReadableStreamBuffer.on('data', function (data) {
        connection.sendBytes(data);
    });

    myReadableStreamBuffer.on('end', function () {
        console.log('Closing connection.');
        connection.close(1000);
    });
}

function connect() {
    var ws = new wsClient();

    ws.on('connectFailed', function (error) {
        console.log('Connection error: ' + error.toString());
    });

    ws.on('connect', function (connection) {
        console.log('Connected.');

        connection.on('message', receive);

        connection.on('close', function (reasonCode, description) {
            console.log('Connection closed: ' + reasonCode);
        });

        connection.on('error', function (error) {
            console.log('Connection error: ' + error.toString());
        });

        send(connection, input_path);
    });

    ws.connect(uri, null, null, { 'Ocp-Apim-Subscription-Key' : key });
}

connect();
```

**Wynik tłumaczenia mowy**

Pomyślnym wynikiem jest utworzenie pliku o nazwie „speak2.wav”. Ten plik zawiera tłumaczenie słów wymawianych w pliku „speak.wav”.

[Powrót do początku](#HOLTop)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący tłumaczenia mowy w usłudze Translator](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Zobacz także

[Omówienie tłumaczenia mowy w usłudze Translator](../overview.md)
[Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
