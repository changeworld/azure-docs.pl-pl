---
title: Zamiany tekstu na mowę dokumentacja interfejsu API (REST) — usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać zamiany tekstu na mowę interfejsu API REST. W tym artykule dowiesz się o opcjach autoryzacji, opcje zapytania, jak struktury żądania i odpowiedzi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d687665a7f9b18d42dcd45953c15d2fbc6d71d2f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335831"
---
# <a name="text-to-speech-rest-api"></a>Interfejs API REST zamiany tekstu na mowę

Usługi mowy umożliwiają konwertowanie zamiany tekstu na mowę przy użyciu interfejsu API REST. Każdy punkt końcowy, dostępny jest skojarzone z regionem. Aplikacja wymaga klucza subskrypcji dla punktu końcowego, który ma być używany. Zamiany tekstu na mowę interfejsu API REST obsługuje neuronowych i standard głosów zamiany tekstu na mowę, z których każdy obsługuje określonego języka i dialektu, identyfikowane za pomocą ustawień regionalnych.

* Aby uzyskać pełną listę głosów, zobacz [języki](language-support.md#text-to-speech).
* Aby uzyskać informacje o dostępności regionalnej, zobacz [regionów](regions.md#text-to-speech).

> [!IMPORTANT]
> Koszty się różnić w przypadku standardowych, niestandardowych i neuronowych głosów. Aby uzyskać więcej informacji, zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Przed użyciem tego interfejsu API, należy zrozumieć:

* Zamiany tekstu na mowę interfejsu API REST wymaga nagłówka autoryzacji. Oznacza to, trzeba wykonać wymiany tokenu dostępu do usługi. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regiony i punktów końcowych

Te regiony są obsługiwane w przypadku przekształcania mowy na tekst przy użyciu interfejsu API REST. Upewnij się, wybierz pozycję punkt końcowy, który odpowiada Twoim regionie subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="request-headers"></a>Nagłówki żądań

Ta tabela zawiera wymagane i opcjonalne nagłówki dla żądania zamiany mowy na tekst.

| Nagłówek | Opis | Wymagane / opcjonalne |
|--------|-------------|---------------------|
| `Authorization` | Token autoryzacji poprzedzone wyrazem `Bearer`. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Wymagane |
| `Content-Type` | Określa typ zawartości dla podanego tekstu. Akceptowane wartości: `application/ssml+xml`. | Wymagane |
| `X-Microsoft-OutputFormat` | Określa format danych wyjściowych audio. Aby uzyskać pełną listę akceptowanych wartości, zobacz [danych wyjściowych audio](#audio-outputs). | Wymagane |
| `User-Agent` | Nazwa aplikacji. Podana wartość musi być krótsza niż 255 znaków. | Wymagane |

## <a name="audio-outputs"></a>Dane wyjściowe audio

Jest to lista obsługiwanych formatów audio, które są wysyłane do wszystkich żądań jako `X-Microsoft-OutputFormat` nagłówka. Każdy łączy w sobie szybkość transmisji bitów i typ kodowania. Usługi mowy obsługują 24 KHz, 16 KHz, a następnie generuje 8 KHz audio.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Jeśli wybrany głosu i format danych wyjściowych inne szybkości transmisji bitów, audio jest próbkowany zgodnie z potrzebami. Jednak nie obsługują głosów 24khz `audio-16khz-16kbps-mono-siren` i `riff-16khz-16kbps-mono-siren` formaty danych wyjściowych.

## <a name="request-body"></a>Treść żądania

Treść każdego `POST` żądanie jest wysyłane jako [mowy syntezy Markup Language (SSML)](speech-synthesis-markup.md). SSML pozwala wybrać, głos i język syntezatora mowy zwracane przez usługę zamiany tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [języki](language-support.md#text-to-speech).

> [!NOTE]
> Jeśli używasz niestandardowych voice, treści żądania mogą być wysyłane jako zwykły tekst (ASCII lub UTF-8).

## <a name="sample-request"></a>Przykładowe żądanie

To żądanie HTTP używa SSML w celu określenia połączeń głosowych i języka. Treść nie może przekraczać 1000 znaków.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Zobacz naszymi przewodnikami szybkiego startu przykłady specyficzny dla języka:

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

## <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi wskazuje sukces lub typowych błędów.

| Kod stanu HTTP | Opis | Możliwa przyczyna |
|------------------|-------------|-----------------|
| 200 | OK | Żądanie powiodło się; treść odpowiedzi jest plik audio. |
| 400 | Nieprawidłowe żądanie | Wymagany parametr jest Brak, pusta lub równa null. Lub wartość przekazana do każdego wymaganego lub opcjonalnego parametru jest nieprawidłowa. Typowym problemem jest nagłówkiem, który jest za długi. |
| 401 | Brak autoryzacji | Żądanie nie jest autoryzowany. Zaznacz, aby upewnić się, że klucz subskrypcji lub token jest prawidłowy i w poprawny region. |
| 413 | Jednostka żądania jest zbyt duża | Dane wejściowe SSML jest dłuższa niż 1024 znaki. |
| 429 | Zbyt wiele żądań | Przekroczono limit przydziału lub liczbę żądań dozwoloną przez subskrypcję. |
| 502 | Zła brama | Problem z siecią lub po stronie serwera. Może również oznaczać nieprawidłowy nagłówek. |

W przypadku stanu HTTP `200 OK`, treść odpowiedzi zawiera plik audio w formacie żądanej. Ten plik można odtwarzać, jak ma przesyłane, zapisany do buforu lub zapisany w pliku.

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
