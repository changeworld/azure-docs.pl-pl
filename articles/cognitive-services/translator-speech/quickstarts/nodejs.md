---
title: Node.js Przewodnik Szybki start dotyczący usługi Azure Cognitive Services, interfejs API mowy usługi Microsoft Translator | Dokumentacja firmy Microsoft
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu interfejsu API mowy usługi Microsoft Translator w usługach Microsoft Cognitive Services na platformie Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 0c0f3120811bba164a07783bc7ce3b7af389fd2b
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205218"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-nodejs"></a>Przewodnik Szybki start dotyczący usługi Microsoft Translator interfejs API rozpoznawania mowy w środowisku Node.js 
<a name="HOLTop"></a>

Ten artykuł pokazuje, jak używać interfejsu API mowy usługi Microsoft Translator do tłumaczenia używany w pliku .wav słów.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz [Node.js 6](https://nodejs.org/en/download/) do uruchamiania tego kodu.

Musisz zainstalować [pakietu Websocket](https://www.npmjs.com/package/websocket) dla środowiska Node.js.

Będzie potrzebny plik WAV, o nazwie "speak.wav", w tym samym folderze co plik wykonywalny, który kompilujesz z poniższym kodem. Ten plik w formacie .wav należy w module PCM standardowe, 16-bitowych, 16 kHz, format mono. Można uzyskać taki plik WAV z [zamiany tekstu na mowę interfejsu API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

Konieczne jest posiadanie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejs API mowy usługi Microsoft Translator**. Potrzebujesz klucza z płatnej licencji usługi [pulpitu nawigacyjnego platformy Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Tłumaczenie mowy

Poniższy kod wykonuje translację mowy z jednego języka do innego.

1. Utwórz nowy projekt środowiska Node.js w Twoim ulubionym środowisku IDE.
2. Dodaj kod, przedstawione poniżej.
3. Zastąp `key` wartością prawidłowy klucz dostępu dla Twojej subskrypcji.
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
https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech
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

**Tłumaczenie mowy odpowiedzi**

Pomyślne wynikiem jest utworzenie pliku o nazwie "speak2.wav". Plik zawiera tłumaczeń wyrazów, używany w "speak.wav".

[Powrót do początku](#HOLTop)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący mowy usługi Translator](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie funkcji rozpoznawania mowy w usłudze Translator](../overview.md)
[dokumentacja interfejsu API](http://docs.microsofttranslator.com/speech-translate.html)
