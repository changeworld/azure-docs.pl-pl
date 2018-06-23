---
title: Python — Szybki Start dla usługi Azure kognitywnych usług Microsoft Translator mowy interfejsu API | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API usługi Microsoft Translator mowy w kognitywnych usług Microsoft Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 29722b3fa7fe61aff0b2406002453020d999ea41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347412"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-python"></a>Szybki Start dla Microsoft Translator mowy interfejsu API za pomocą języka Python 
<a name="HOLTop"></a>

W tym artykule przedstawiono sposób użycia interfejsu API usługi Microsoft Translator mowy tłumaczenie słowa używany w pliku WAV.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [Python 3.x](https://www.python.org/downloads/) do uruchomienia tego kodu.

Musisz zainstalować [pakiet klienta protokołu websocket](https://pypi.python.org/pypi/websocket-client) dla języka Python.

Konieczne będzie pliku wav o nazwie "speak.wav" w tym samym folderze co plik wykonywalny, który skompilować z kodu poniżej. Ten plik .wav powinna mieć PCM standardowe, 16-bitowy, 16kHz mono formatu. Można uzyskać taki plik .wav z [API mowy tekst Translator](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak).

Musi mieć [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsu API usługi Microsoft Translator mowy**. Konieczne będzie klucza płatnej subskrypcji, z Twojego [pulpitu nawigacyjnego Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Tłumaczenie mowy

Poniższy kod tłumaczy mowy z jednego języka do innego.

1. Utwórz nowy projekt języka Python w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
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
path = '/speech/translate';
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray ()

def on_open (client):
    print ("Connected.")

# r = read. b = binary.
    with open (input_file, mode='rb') as file:
        data = file.read()

    print ("Sending audio.")
    client.send (data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print ("Sending silence.")
    client.send (bytearray (32000), websocket.ABNF.OPCODE_BINARY)

def on_data (client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print ("Received text data.")
        print (message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print ("Received binary data.")
        print ("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
# w = write. b = binary.
            with open (output_file, mode='wb') as file:
                file.write (output)
                print ("Wrote data to output file.")
            client.close ()
    else:
        print ("Received data of type: " + str (message_type))

def on_error (client, error):
    print ("Connection error: " + str (error))

def on_close (client):
    print ("Connection closed.")

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

print ("Connecting...")
client.run_forever()
```

**Tłumaczenie mowy odpowiedzi**

Pomyślne wynikiem jest utworzenie pliku o nazwie "speak2.wav". Plik zawiera tłumaczenia słowa używany w "speak.wav".

[Powrót do początku](#HOLTop)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Translator samouczka mowy](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie mowy Translator](../overview.md)
[dokumentacja interfejsu API](http://docs.microsofttranslator.com/speech-translate.html)
