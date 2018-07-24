---
title: Przewodnik Szybki Start języka C# dla usługi Azure Cognitive Services, interfejs API mowy usługi Microsoft Translator | Dokumentacja firmy Microsoft
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu interfejsu API mowy usługi Microsoft Translator w usługach Microsoft Cognitive Services na platformie Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 4f12d74aedbcadc311cd9c5ccd12dc1ad3501dbf
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205106"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-c"></a>Przewodnik Szybki start dotyczący mowy usługi Microsoft Translator interfejsu API w języku C# 
<a name="HOLTop"></a>

Ten artykuł pokazuje, jak używać interfejsu API mowy usługi Microsoft Translator do tłumaczenia używany w pliku .wav słów.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) do uruchamiania tego kodu na Windows. (Bezpłatna wersja Community będzie działać.)

Konieczne będzie plik wav o nazwie "speak.wav", w tym samym folderze co plik wykonywalny, który kompilujesz z poniższym kodem. Ten plik w formacie .wav należy w module PCM standardowe, 16-bitowych, 16kHz, format mono. Można uzyskać taki plik WAV z [zamiany tekstu na mowę interfejsu API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

Konieczne jest posiadanie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejs API mowy usługi Microsoft Translator**. Konieczne będzie klucza z płatnej licencji usługi [pulpitu nawigacyjnego platformy Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Tłumaczenie mowy

Poniższy kod wykonuje translację mowy z jednego języka do innego.

1. Utwórz nowy projekt C# w Twoim ulubionym środowisku IDE.
2. Dodaj kod, przedstawione poniżej.
3. Zastąp `key` wartością prawidłowy klucz dostępu dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace TranslateSpeechQuickStart
{
    class Program
    {
        static string host = "wss://dev.microsofttranslator.com";
        static string path = "/speech/translate";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        async static Task Send (ClientWebSocket client, string input_path)
        {
            var audio = File.ReadAllBytes(input_path);
            var audio_out_buffer = new ArraySegment<byte>(audio);
            Console.WriteLine("Sending audio.");
            await client.SendAsync(audio_out_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            /* Make sure the audio file is followed by silence.
             * This lets the service know that the audio input is finished. */
            var silence = new byte[3200000];
            var silence_buffer = new ArraySegment<byte>(silence);
            await client.SendAsync(silence_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            Console.WriteLine("Done sending.");
            await client.CloseAsync(WebSocketCloseStatus.NormalClosure, "", CancellationToken.None);
        }

        async static Task Receive(ClientWebSocket client, string output_path)
        {
            var inbuf = new byte[102400];
            var segment = new ArraySegment<byte>(inbuf);
            var stream = new FileStream(output_path, FileMode.Create);

            Console.WriteLine("Awaiting response.");
            while (client.State == WebSocketState.Open)
            {
                var result = await client.ReceiveAsync(segment, CancellationToken.None);
                switch (result.MessageType)
                {
                    case WebSocketMessageType.Close:
                        Console.WriteLine("Received close message. Status: " + result.CloseStatus + ". Description: " + result.CloseStatusDescription);
                        await client.CloseAsync(WebSocketCloseStatus.NormalClosure, string.Empty, CancellationToken.None);
                        break;
                    case WebSocketMessageType.Text:
                        Console.WriteLine("Received text.");
                        Console.WriteLine(Encoding.UTF8.GetString(inbuf).TrimEnd('\0'));
                        break;
                    case WebSocketMessageType.Binary:
                        Console.WriteLine("Received binary data: " + result.Count + " bytes.");
                        stream.Write(inbuf, 0, result.Count);
                        break;
                }
            }

            stream.Close();
            stream.Dispose();
        }

        async static void TranslateSpeech()
        {
            var client = new ClientWebSocket();
            client.Options.SetRequestHeader ("Ocp-Apim-Subscription-Key", key);

            string from = "en-US";
            string to = "it-IT";
            string features = "texttospeech";
            string voice = "it-IT-Elsa";
            string api = "1.0";

            string input_path = "speak.wav";
            string output_path = "speak2.wav";

            string uri = host + path +
                "?from=" + from +
                "&to=" + to +
                "&api-version=" + api +
                "&features=" + features +
                "&voice=" + voice;

            Console.WriteLine("uri: " + uri);
            Console.WriteLine("Opening connection.");
            await client.ConnectAsync(new Uri(uri), CancellationToken.None);
            Console.WriteLine("Connection open.");
            Task.WhenAll(Send(client, input_path), Receive(client, output_path)).Wait();
        }

        static void Main()
        {
            TranslateSpeech();
            Console.ReadLine();
        }

    }
}
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
