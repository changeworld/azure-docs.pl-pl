---
title: Jak strumieniem Audio transferu pakietowego | Dokumentacja firmy Microsoft
description: Jak używać podzielony trasfer wysłać strumień dźwiękowy do usługi mowy
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 7d02340932dfc547893c4c40cbe08978b7b93756
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347397"
---
# <a name="chunked-transfer-encoding"></a>Kodowanie transferu pakietowego

Wykonać transkrypcji mowy na tekst, rozpoznawanie mowy Microsoft interfejsu API umożliwia wysyłanie audio jako jednym fragmencie całego lub do dzielenia audio na małe fragmenty o różnych. Przesyłanie strumieniowe audio wydajne i zmniejszenia opóźnień zapisu, zaleca się używanie [kodowanie transferu pakietowego](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) do przesyłania strumieniowego audio do usługi. Implementacje może skutkować większego opóźnienia postrzegane przez użytkowników. Aby uzyskać więcej informacji, zobacz [strumieni Audio](../concepts.md#audio-streams) strony.

> [!NOTE]
> Nie może przekazać więcej niż 10 sekund dźwięku w dowolnym jedno żądanie, a żądanie całkowity czas trwania nie może przekraczać 14 sekund.
> [!NOTE]
> Należy określić kodowanie tylko wtedy, gdy używasz transferu pakietowego [interfejsów API REST](../GetStarted/GetStartedREST.md) do wywołania tej usługi mowy. Aplikacje używające [bibliotek klienckich](../GetStarted/GetStartedClientLibraries.md) nie trzeba konfigurować kodowanie transferu pakietowego.

Poniższy kod przedstawia sposób ustawić kodowanie transferu pakietowego i Wyślij plik dźwiękowy są dzielone na fragmenty 1024 bajtów.

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
