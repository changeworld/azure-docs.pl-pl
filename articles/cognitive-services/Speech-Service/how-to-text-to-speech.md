---
title: Użyj tekstu na mowę przy użyciu usług przetwarzania mowy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Użyj tekstu na mowę w usłudze rozpoznawania mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 0ace89e04baf81776f82edd002b93b944c752051
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860136"
---
# <a name="use-text-to-speech-in-speech-service"></a>Użyj "Tekst na mowę" usługi mowy

Usługa rozpoznawania mowy udostępnia funkcjonalność zamiany tekstu na mowę za pośrednictwem prostego żądania HTTP. Gdy OPUBLIKUJESZ tekst, który ma być używany do odpowiednich punktów końcowych, a usługa zwraca plik audio (`.wav`) zawierający syntezatora mowy. Aplikacja następnie można użyć tego audio jako na potrzeby.

Treść żądania zamiany tekstu na mowę, może być zwykły tekst (ASCII lub UTF8) lub [SSML](speech-synthesis-markup.md) dokumentu. Za pomocą głosu domyślne telefoniczną żądania w formacie zwykłego tekstu. W większości przypadków chcesz użyć SSML treści. Żądanie HTTP musi zawierać [autoryzacji](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#authentication) tokenu. 

Punkty końcowe regionalnych zamiany tekstu na mowę są wyświetlane w tym miejscu. Użyj jednej odpowiednie dla Twojej subskrypcji.

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Określ głosu

Aby określić głosu, użyj `<voice>` [SSML](speech-synthesis-markup.md) tagu. Na przykład:

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Zobacz [głosów zamiany tekstu na mowę](supported-languages.md#text-to-speech) Aby uzyskać listę dostępnych głosów i ich nazwy.

## <a name="make-a-request"></a>Wyślij żądanie

Zamiana tekstu na mowę HTTP żądania w trybie WPIS z tekstem wymawiane w treści żądania. Maksymalna długość treści żądania HTTP to 1024 znaki. Żądanie musi mieć następujące nagłówki: 

Nagłówek|Wartości|Komentarze
-|-|-
|`Content-Type` | `application/ssml+xml` | Format tekstu wejściowego.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | Format danych wyjściowych audio.
|`User-Agent`   |Nazwa aplikacji | Nazwa aplikacji jest wymagany i musi zawierać mniej niż 255 znaków.
| `Authorization`   | Token autoryzacji uzyskane poprzez przedstawienie klucz subskrypcji do usługi tokenu. Każdy token jest ważny przez 10 minut. Zobacz [interfejsów API REST: uwierzytelnianie](rest-apis.md#authentication).

> [!NOTE]
> Jeśli wybrany głosu i format danych wyjściowych inne szybkości transmisji bitów, audio jest próbkowany zgodnie z potrzebami. nie obsługują głosów 24khz `audio-16khz-16kbps-mono-siren` i `riff-16khz-16kbps-mono-siren` formaty danych wyjściowych. 

Poniżej przedstawiono przykładowe żądanie.

```xml
POST /cognitiveservices/v1
HTTP/1.1
Host: westus.tts.speech.microsoft.com
X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
User-Agent: Test TTS application
Authorization: (authorization token)

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
</voice> </speak>
```

Treść odpowiedzi ze stanem 200 zawiera audio w formacie określonym produktem wyjściowym.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Jeśli wystąpi błąd, używane są poniższe kody stanu. Treść odpowiedzi błędu zawiera również opis problemu.

|Kod|Opis|Problem|
|-|-|-|
400 |Nieprawidłowe żądanie |Wymagany parametr jest Brak, pusta lub równa null. Lub wartość przekazana do każdego wymaganego lub opcjonalnego parametru jest nieprawidłowa. Typowym problemem jest nagłówkiem, który jest za długi.
401|Brak autoryzacji |Żądanie nie jest autoryzowany. Zaznacz, aby upewnić się, że klucz subskrypcji lub token jest prawidłowy.
413|Jednostka żądania jest zbyt duża|Dane wejściowe SSML jest dłuższa niż 1024 znaki.
|502|Zła brama    | Problem z siecią lub po stronie serwera. Może również oznaczać nieprawidłowy nagłówek.

Aby uzyskać więcej informacji na tekście interfejsu API REST usługi rozpoznawania mowy, zobacz [interfejsów API REST](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
