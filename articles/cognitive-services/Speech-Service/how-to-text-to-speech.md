---
title: Użyj zamiany tekstu na mowę usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać zamiany tekstu na mowę w usłudze rozpoznawania mowy.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: erhopf
ms.openlocfilehash: 162b690d4b371cfe76738cd83ce484a3062d139f
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469921"
---
# <a name="use-text-to-speech-in-speech-service"></a>Użyj "Tekst na mowę" usługi mowy

Usługa rozpoznawania mowy udostępnia funkcjonalność zamiany tekstu na mowę za pośrednictwem prostego żądania HTTP. Możesz `POST` tekst, który ma być używany do odpowiednich punktów końcowych i usługi zwraca plik audio (`.wav`) zawierający syntezatora mowy. Aplikacja następnie można użyć tego audio jako na potrzeby.

Treść żądania zamiany tekstu na mowę, może być zwykły tekst (ASCII lub UTF8) lub [SSML](speech-synthesis-markup.md) dokumentu. Za pomocą głosu domyślne telefoniczną żądania w formacie zwykłego tekstu. W większości przypadków chcesz użyć SSML treści. Żądanie HTTP musi zawierać [autoryzacji](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#authentication) tokenu.

Punkty końcowe regionalnych zamiany tekstu na mowę są wyświetlane w tym miejscu. Użyj jednej odpowiednie dla Twojej subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Określ głosu

Aby określić głosu, użyj `<voice>` [SSML](speech-synthesis-markup.md) tagu. Na przykład:

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Zobacz [głosów zamiany tekstu na mowę](language-support.md#text-to-speech) Aby uzyskać listę dostępnych głosów i ich nazwy.

## <a name="make-a-request"></a>Wysyłanie żądania

Zamiana tekstu na mowę HTTP żądania w trybie WPIS z tekstem wymawiane w treści żądania. Maksymalna długość treści żądania HTTP to 1024 znaki. Żądanie musi mieć następujące nagłówki:

Nagłówek|Wartości|Komentarze
-|-|-
|`Content-Type` | `application/ssml+xml` | Format tekstu wejściowego.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`riff-16khz-16bit-mono-pcm`<br>`raw-8khz-8bit-mono-mulaw`<br>`riff-8khz-8bit-mono-mulaw`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | Format danych wyjściowych audio.
|`User-Agent`   |Nazwa aplikacji | Nazwa aplikacji jest wymagany i musi zawierać mniej niż 255 znaków.
| `Authorization`   | Token autoryzacji uzyskane poprzez przedstawienie klucz subskrypcji do usługi tokenu. Każdy token jest ważny przez 10 minut. Zobacz [interfejsów API REST: uwierzytelnianie](rest-apis.md#authentication).

> [!NOTE]
> Jeśli wybrany głosu i format danych wyjściowych inne szybkości transmisji bitów, audio jest próbkowany zgodnie z potrzebami.

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
413|Jednostka żądania jest zbyt duża|SSML danych wejściowych jest za duży lub zawiera więcej niż 3 `<voice>` elementów.
429|Zbyt wiele żądań|Przekroczono limit przydziału lub liczbę żądań dozwoloną przez subskrypcję.
|502|Zła brama    | Problem z siecią lub po stronie serwera. Może również oznaczać nieprawidłowy nagłówek.

Aby uzyskać więcej informacji na tekście interfejsu API REST usługi rozpoznawania mowy, zobacz [interfejsów API REST](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy, języka C++](quickstart-cpp-windows.md)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
- [Rozpoznawanie mowy w języku Java](quickstart-java-android.md)
