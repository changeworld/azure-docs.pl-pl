---
title: Python Przewodnik Szybki start dotyczący usługi Azure Cognitive Services, interfejs API mowy usługi Microsoft Translator | Dokumentacja firmy Microsoft
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu interfejsu API mowy usługi Microsoft Translator w usługach Microsoft Cognitive Services na platformie Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jaswel
ms.openlocfilehash: 8bf904b2029790d64a806fcf4a7e4860579a5a2f
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39204854"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-python"></a>Przewodnik Szybki start dotyczący usługi Microsoft Translator interfejs API rozpoznawania mowy za pomocą języka Python 
<a name="HOLTop"></a>

Ten artykuł pokazuje, jak używać interfejsu API mowy usługi Microsoft Translator do tłumaczenia używany w pliku .wav słów.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [Python 3.x](https://www.python.org/downloads/) do uruchamiania tego kodu.

Musisz zainstalować [pakietu klienta protokołu websocket](https://pypi.python.org/pypi/websocket-client) dla języka Python.

Konieczne będzie plik wav o nazwie "speak.wav", w tym samym folderze co plik wykonywalny, który kompilujesz z poniższym kodem. Ten plik w formacie .wav należy w module PCM standardowe, 16-bitowych, 16kHz, format mono. Można uzyskać taki plik WAV z [zamiany tekstu na mowę interfejsu API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

Konieczne jest posiadanie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejs API mowy usługi Microsoft Translator**. Konieczne będzie klucza z płatnej licencji usługi [pulpitu nawigacyjnego platformy Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Tłumaczenie mowy

Poniższy kod wykonuje translację mowy z jednego języka do innego.

1. Utwórz nowy projekt języka Python w Twoim ulubionym środowisku IDE.
2. Dodaj kod, przedstawione poniżej.
3. Zastąp `key` wartością prawidłowy klucz dostępu dla Twojej subskrypcji.
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

Pomyślne wynikiem jest utworzenie pliku o nazwie "speak2.wav". Plik zawiera tłumaczeń wyrazów, używany w "speak.wav".

[Powrót do początku](#HOLTop)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący mowy usługi Translator](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie funkcji rozpoznawania mowy w usłudze Translator](../overview.md)
[dokumentacja interfejsu API](http://docs.microsofttranslator.com/speech-translate.html)
