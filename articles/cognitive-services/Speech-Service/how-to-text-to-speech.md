---
title: Użyj tekst na mowę, za pomocą usług mowy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Użyj tekst na mowę usługi mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 6c358b5a40b1d8e91c2e1af5eb493b13604cf82e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045062"
---
# <a name="use-text-to-speech-in-speech-service"></a>Użyj "Tekst na mowę" w usłudze mowy

Usługa mowy udostępnia funkcje tekst na mowę, za pomocą prostego żądania HTTP. OPUBLIKUJ tekst, który ma być używany do odpowiedniego punktu końcowego i usługa zwraca plik dźwiękowy (`.wav`) zawierający syntezatora mowy. Aplikacja może następnie używać tego audio jako na potrzeby.

Treść wpisu żądania tekst na mowę, może być zwykły tekst (ASCII lub UTF8) lub [SSML](speech-synthesis-markup.md) dokumentu. Żądań w postaci zwykłego tekstu naturalnych charakteryzuje się głos domyślny. W większości przypadków chcesz użyć treści SSML. Żądanie HTTP musi zawierać token autoryzacji. 

Punkty końcowe regionalnych tekst na mowę są wyświetlane tutaj. Użyj jednej odpowiednie do Twojej subskrypcji.

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

Zobacz [głosy tekst na mowę](supported-languages.md#text-to-speech) listę dostępnych głosów i ich nazw.

## <a name="make-a-request"></a>Tworzenie żądania

Tekst na mowę HTTP żądań w trybie POST tekstem wymawiane w treści żądania. Maksymalna długość treści żądania HTTP to 1024 znaki. Żądanie musi mieć następujące nagłówki: 

Nagłówek|Wartości|Komentarze
-|-|-
|`Content-Type` | `application/ssml+xml` | Format tekstu wejściowego.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | Format audio danych wyjściowych.
|`User-Agent`   |Nazwa aplikacji | Nazwa aplikacji jest wymagana i musi być krótsza niż 255 znaków.
| `Authorization`   | Token autoryzacji uzyskane z uwzględnieniem klucz subskrypcji do usługi tokenu. Każdy token jest ważny przez 10 minut. Zobacz [interfejsów API REST: uwierzytelnianie](rest-apis.md#authentication).

> [!NOTE]
> Jeśli wybrany głosu i format wyjściowy inne szybkości transmisji bitów, dźwięk jest próbkowany odpowiednio do potrzeb. Głosy 24khz nie obsługują `audio-16khz-16kbps-mono-siren` i `riff-16khz-16kbps-mono-siren` formatów. 

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

Treść odpowiedzi o stanie 200 zawiera audio w formacie określonym produktem wyjściowym.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Jeśli wystąpi błąd, są używane poniżej kodów stanu. Treść odpowiedzi błędu zawiera również opis problemu.

|Kod|Opis|Problem|
|-|-|-|
400 |Nieprawidłowe żądanie |Wymagany parametr jest Brak, pusty lub zerowy. Lub wartość przekazywana do każdego wymaganego lub opcjonalnego parametru jest nieprawidłowa. Typowym problemem jest nagłówek jest zbyt długa.
401|Brak autoryzacji |Żądanie nie jest autoryzowane. Upewnij się, że klucz subskrypcji lub token jest prawidłowy.
413|Jednostka żądania jest za duży|Dane wejściowe SSML jest dłuższa niż 1024 znaki.
|502|Zła brama    | Problem z siecią lub po stronie serwera. Może również oznaczać nieprawidłowy nagłówek.

Aby uzyskać więcej informacji na tekst na mowę interfejsu API REST, zobacz [interfejsów API REST](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
