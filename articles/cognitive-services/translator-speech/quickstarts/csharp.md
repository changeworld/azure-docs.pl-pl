---
title: 'Szybki start: interfejs API tłumaczenia mowy w usłudze Translator — C#'
titlesuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API tłumaczenia mowy w usłudze Translator.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: quickstart
ms.date: 3/5/2018
ms.author: v-jaswel
ROBOTS: NOINDEX
ms.openlocfilehash: 7d1f5968fe6ddffce3194f070b6a17aca4d3d9ab
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995044"
---
# <a name="quickstart-translator-speech-api-with-c"></a>Szybki start: interfejs API tłumaczenia mowy w usłudze Translator — C# 
<a name="HOLTop"></a>

W tym artykule pokazano, jak używać interfejsu API tłumaczenia mowy w usłudze Translator do tłumaczenia wypowiedzi z pliku wav.

## <a name="prerequisites"></a>Wymagania wstępne

Do uruchamiania tego kodu w systemie Windows jest potrzebny [program Visual Studio 2017](https://www.visualstudio.com/downloads/). (Można korzystać z bezpłatnej wersji Community Edition).

Plik wav o nazwie „speak.wav” musi znajdować się w tym samym folderze co plik wykonywalny, który skompilujesz przy użyciu poniższego kodu. Ten plik wav powinien być plikiem zapisanym w standardzie PCM, o 16-bitowej rozdzielczości, częstotliwości 16 kHz w formacie mono.

Musisz mieć również [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsem API tłumaczenia mowy w usłudze Microsoft Translator**. Będziesz potrzebować klucza płatnej subskrypcji dostępnego na [pulpicie nawigacyjnym platformy Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Tłumaczenie mowy

Poniższy kod tłumaczy mowę z jednego języka na inny.

1. Utwórz nowy projekt języka C# w ulubionym środowisku IDE.
2. Dodaj kod przedstawiony poniżej.
3. Zastąp wartość `key` kluczem dostępu właściwym dla Twojej subskrypcji.
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

**Wynik tłumaczenia mowy**

Pomyślnym wynikiem jest utworzenie pliku o nazwie „speak2.wav”. Ten plik zawiera tłumaczenie słów wymawianych w pliku „speak.wav”.

[Powrót do początku](#HOLTop)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący tłumaczenia mowy w usłudze Translator](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Zobacz też 

[Omówienie tłumaczenia mowy w usłudze Translator](../overview.md)
[Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
