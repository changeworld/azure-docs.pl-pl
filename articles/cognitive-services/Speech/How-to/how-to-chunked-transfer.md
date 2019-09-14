---
title: Jak przenieść strumień audio | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Jak używać fragmentarycznego trasfer do wysyłania strumienia audio do usługi rozpoznawanie mowy Bing
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 60428a7a5e28b7c9d6cd76f31374f24df48423a6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966489"
---
# <a name="chunked-transfer-encoding"></a>Kodowanie transferu fragmentarycznego

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Aby transkrypcja mowę na tekst, interfejs API rozpoznawania mowy firmy Microsoft umożliwia wysłanie dźwięku jako jednego fragmentu lub Chop dźwięk do małych fragmentów. W celu wydajnego przesyłania strumieniowego audio i skrócenia opóźnień transkrypcji zaleca się użycie [fragmentarycznego kodowania transferu](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) do przesyłania strumieniowego audio do usługi. Inne implementacje mogą skutkować wyższym opóźnieniem postrzeganym przez użytkownika. Aby uzyskać więcej informacji, zobacz stronę [strumienie audio](../concepts.md#audio-streams) .

> [!NOTE]
> Nie można przekazać więcej niż 10 sekund audio w jednym żądaniu, a łączny czas trwania żądania nie może przekroczyć 14 sekund.
> [!NOTE]
> Kodowanie transferu fragmentarycznego należy określić tylko wtedy, gdy używasz [interfejsów API REST](../GetStarted/GetStartedREST.md) do wywoływania usługi mowy. Aplikacje korzystające z [bibliotek klienckich](../GetStarted/GetStartedClientLibraries.md) nie muszą konfigurować kodowania transferu fragmentarycznego.

Poniższy kod pokazuje, jak ustawić kodowanie transferu fragmentarycznego i przesłać plik audio podzielony na fragmenty 1024-bajtowe.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
