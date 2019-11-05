---
title: Wykonywanie zapytania dotyczącego punktu końcowego kontenera zamiany tekstu na mowę
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491096"
---
Kontener zapewnia [interfejsy API punktu końcowego oparte na protokole REST](../rest-text-to-speech.md). Istnieje wiele [przykładowych projektów kodu źródłowego](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) dla dostępnych wariantów platformy, architektury i języka.

Przy użyciu *standardowego kontenera zamiany tekstu na mowę* należy zależeć od ustawień regionalnych i głosu pobranego tagu obrazu. Na przykład jeśli pobrano znacznik `latest` domyślne ustawienia regionalne są `en-US` i `JessaRUS` głosu. `{VOICE_NAME}` argument będzie następnie [`en-US-JessaRUS`](../language-support.md#standard-voices). Zobacz przykład SSML poniżej:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Jednak w przypadku *niestandardowej zamiany tekstu na mowę* należy uzyskać **dźwięk/model** z [niestandardowego portalu głosowego](https://aka.ms/custom-voice-portal). Niestandardowa nazwa modelu jest równoznaczna z nazwą głosu. Przejdź do strony **szkoleń** i skopiuj **dźwięk/model** , który ma być używany jako argument `{VOICE_NAME}`.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Niestandardowy model głosowy — nazwa głosu":::

Zobacz przykład SSML poniżej:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Utwórzmy żądanie HTTP POST, dostarczając kilka nagłówków i ładunek danych. Zastąp symbol zastępczy `{VOICE_NAME}` własną wartością.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

To polecenie:

* Tworzy żądanie HTTP POST dla punktu końcowego `speech/synthesize/cognitiveservices/v1`.
* Określa `Accept` nagłówek `audio/*`
* Określa `Content-Type` nagłówek `application/ssml+xml`, aby uzyskać więcej informacji, zobacz [treść żądania](../rest-text-to-speech.md#request-body).
* Określa `X-Microsoft-OutputFormat` nagłówek `riff-16khz-16bit-mono-pcm`, aby uzyskać więcej opcji, zobacz [dane wyjściowe audio](../rest-text-to-speech.md#audio-outputs).
* Wysyła żądanie [SSML (Speech synteza Markup Language)](../speech-synthesis-markup.md) , `{VOICE_NAME}` do punktu końcowego.