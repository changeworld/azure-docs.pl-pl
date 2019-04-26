---
title: Jak Stream Audio transferu pakietowego | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Jak używać fragmentaryczne trasfer do wysyłania strumienia audio do usługi rozpoznawania mowy Bing
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d9796cf60e2695c21d781131c935d24891401efa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515005"
---
# <a name="chunked-transfer-encoding"></a>Kodowanie fragmentaryczne transferu

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Także zamiana mowy na tekst, interfejs API rozpoznawania mowy firmy Microsoft pozwala wysyłać audio jako jeden całego fragmentu lub skalowane audio na małe fragmenty. Efektywne przesyłanie strumieniowe audio i zredukowanie opóźnień transkrypcji, zalecane jest użycie [Kodowanie fragmentaryczne transferu](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) do przesyłania strumieniowego audio do usługi. Inne implementacje może spowodować większych opóźnień postrzegane przez użytkowników. Aby uzyskać więcej informacji, zobacz [strumieni Audio](../concepts.md#audio-streams) strony.

> [!NOTE]
> Nie może przekazać więcej niż 10 sekund audio w dowolnej jedno żądanie, a czas trwania całkowite żądanie nie może przekraczać 14 sekund.
> [!NOTE]
> Należy określić kodowanie, tylko wtedy, gdy używasz transferu pakietowego [interfejsów API REST](../GetStarted/GetStartedREST.md) wywołać usługę rozpoznawania mowy. Aplikacje, które używają [biblioteki klienckie](../GetStarted/GetStartedClientLibraries.md) nie trzeba konfigurować Kodowanie fragmentaryczne transferu.

Poniższy kod przedstawia sposób Ustaw Kodowanie fragmentaryczne transferu, a także wysłać plik dźwiękowy jest podzielony na fragmenty 1024 bajtów.

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
