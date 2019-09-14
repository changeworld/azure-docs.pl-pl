---
title: 'Szybki start: Interfejs API tłumaczenia mowy w usłudze Translator dla języka Python'
titlesuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API tłumaczenia mowy w usłudze Translator.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7189563ebbcc5ae1a167f99ff8704aff16d0feac
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966477"
---
# <a name="quickstart-translator-speech-api-with-python"></a>Szybki start: Interfejs API tłumaczenia mowy w usłudze Translator dla języka Python
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

W tym artykule pokazano, jak używać interfejsu API tłumaczenia mowy w usłudze Translator do tłumaczenia wypowiedzi z pliku wav.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten kod, potrzebne jest środowisko języka [Python 3.x](https://www.python.org/downloads/).

Konieczne też będzie zainstalowanie [pakietu websocket-client](https://pypi.python.org/pypi/websocket-client) dla języka Python.

Plik wav o nazwie „speak.wav” musi znajdować się w tym samym folderze co plik wykonywalny, który skompilujesz przy użyciu poniższego kodu. Ten plik wav powinien być plikiem zapisanym w standardzie PCM, o 16-bitowej rozdzielczości, częstotliwości 16 kHz w formacie mono.

Musisz mieć również [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsem API tłumaczenia mowy w usłudze Microsoft Translator**. Będziesz potrzebować klucza płatnej subskrypcji dostępnego na [pulpicie nawigacyjnym platformy Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Tłumaczenie mowy

Poniższy kod tłumaczy mowę z jednego języka na inny.

1. Utwórz nowy projekt języka Python w Twoim ulubionym środowisku IDE.
2. Dodaj kod przedstawiony poniżej.
3. Zastąp wartość `key` kluczem dostępu właściwym dla Twojej subskrypcji.
4. Uruchom program.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate'
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray()


def on_open(client):
    print("Connected.")

# r = read. b = binary.
    with open(input_file, mode='rb') as file:
        data = file.read()

    print("Sending audio.")
    client.send(data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print("Sending silence.")
    client.send(bytearray(32000), websocket.ABNF.OPCODE_BINARY)


def on_data(client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print("Received text data.")
        print(message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print("Received binary data.")
        print("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
            # w = write. b = binary.
            with open(output_file, mode='wb') as file:
                file.write(output)
                print("Wrote data to output file.")
            client.close()
    else:
        print("Received data of type: " + str(message_type))


def on_error(client, error):
    print("Connection error: " + str(error))


def on_close(client):
    print("Connection closed.")


client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print("Connecting...")
client.run_forever()
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
