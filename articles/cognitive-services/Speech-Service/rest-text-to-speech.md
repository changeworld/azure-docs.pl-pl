---
title: Dokumentacja interfejsu API zamiany tekstu na mowę (REST) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API REST zamiany tekstu na mowę. W tym artykule dowiesz się o opcjach autoryzacji, opcje zapytania, jak struktury żądania i odpowiedzi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: ab0891653f449b13f50dc43b196cf16a2f71370e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975826"
---
# <a name="text-to-speech-rest-api"></a>Interfejs API REST zamiany tekstu na mowę

Usługa mowy umożliwia [Konwertowanie tekstu na mowę](#convert-text-to-speech) i [Pobieranie listy obsługiwanych głosów](#get-a-list-of-voices) dla regionu przy użyciu zestawu interfejsów API REST. Każdy dostępny punkt końcowy jest skojarzony z regionem. Wymagany jest klucz subskrypcji dla punktu końcowego/regionu, który ma być używany.

Interfejs API REST zamiany tekstu na mowę obsługuje neuronowych i standardowe głosy zamiany tekstu na mowę, z których każdy obsługuje określony język i dialekt identyfikowany przez ustawienia regionalne.

* Aby uzyskać pełną listę głosów, zobacz temat [Obsługa języków](language-support.md#text-to-speech).
* Aby uzyskać informacje o dostępności regionalnej, zobacz [regiony](regions.md#text-to-speech).

> [!IMPORTANT]
> Koszty różnią się w zależności od standardowych, niestandardowych i neuronowych głosów. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Przed użyciem tego interfejsu API należy zrozumieć następujące informacje:

* Zamiany tekstu na mowę interfejsu API REST wymaga nagłówka autoryzacji. Oznacza to, trzeba wykonać wymiany tokenu dostępu do usługi. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Pobieranie listy głosów

Punkt końcowy `voices/list` umożliwia uzyskanie pełnej listy głosów dla określonego regionu/punktu końcowego.

### <a name="regions-and-endpoints"></a>Regiony i punktów końcowych

| Region | Punkt końcowy |
|--------|----------|
| Australia Wschodnia | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brazylia Południowa | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kanada Środkowa | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Środkowe stany USA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Azja Wschodnia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Wschodnie stany USA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Wschodnie stany USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Francja Środkowa | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Indie Środkowe | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japonia Wschodnia | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Korea Środkowa | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Północno-środkowe stany USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa Północna | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Południowo-środkowe stany USA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Azja Południowo-Wschodnia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Południowe Zjednoczone Królestwo | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa Zachodnia | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Zachodnie stany USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Zachodnie stany USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Nagłówki żądań

Ta tabela zawiera listę wymaganych i opcjonalnych nagłówków dla żądań zamiany tekstu na mowę.

| Nagłówek | Opis | Wymagane / opcjonalne |
|--------|-------------|---------------------|
| `Authorization` | Token autoryzacji poprzedzone wyrazem `Bearer`. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Wymagane |

### <a name="request-body"></a>Treść żądania

Treść żądania `GET` do tego punktu końcowego nie jest wymagana.

### <a name="sample-request"></a>Przykładowe żądanie

To żądanie wymaga tylko nagłówka autoryzacji.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Przykładowa odpowiedź

Ta odpowiedź została obcięta w celu zilustrowania struktury odpowiedzi.

> [!NOTE]
> Dostępność głosu jest różna w zależności od regionu/punktu końcowego.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi wskazuje sukces lub typowych błędów.

| Kod stanu HTTP | Opis | Możliwa przyczyna |
|------------------|-------------|-----------------|
| 200 | OK | Żądanie zakończyło się pomyślnie. |
| 400 | Nieprawidłowe żądanie | Wymagany parametr jest Brak, pusta lub równa null. Lub wartość przekazana do każdego wymaganego lub opcjonalnego parametru jest nieprawidłowa. Typowym problemem jest nagłówkiem, który jest za długi. |
| 401 | Brak autoryzacji | Żądanie nie jest autoryzowany. Zaznacz, aby upewnić się, że klucz subskrypcji lub token jest prawidłowy i w poprawny region. |
| 429 | Zbyt wiele żądań | Przekroczono limit przydziału lub liczbę żądań dozwoloną przez subskrypcję. |
| 502 | Zła brama | Problem z siecią lub po stronie serwera. Może również oznaczać nieprawidłowy nagłówek. |


## <a name="convert-text-to-speech"></a>Zamiana tekstu na mowę

Punkt końcowy `v1` umożliwia konwertowanie zamiany tekstu na mowę przy użyciu [języka SSML (Speech syntezing Language)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regiony i punktów końcowych

Te regiony są obsługiwane w przypadku zamiany tekstu na mowę przy użyciu interfejsu API REST. Upewnij się, wybierz pozycję punkt końcowy, który odpowiada Twoim regionie subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Nagłówki żądań

Ta tabela zawiera listę wymaganych i opcjonalnych nagłówków dla żądań zamiany tekstu na mowę.

| Nagłówek | Opis | Wymagane / opcjonalne |
|--------|-------------|---------------------|
| `Authorization` | Token autoryzacji poprzedzone wyrazem `Bearer`. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Wymagane |
| `Content-Type` | Określa typ zawartości dla podanego tekstu. Akceptowane wartości: `application/ssml+xml`. | Wymagane |
| `X-Microsoft-OutputFormat` | Określa format danych wyjściowych audio. Aby uzyskać pełną listę akceptowanych wartości, zobacz [danych wyjściowych audio](#audio-outputs). | Wymagane |
| `User-Agent` | Nazwa aplikacji. Podana wartość musi być krótsza niż 255 znaków. | Wymagane |

### <a name="audio-outputs"></a>Dane wyjściowe audio

Jest to lista obsługiwanych formatów audio, które są wysyłane do wszystkich żądań jako `X-Microsoft-OutputFormat` nagłówka. Każdy łączy w sobie szybkość transmisji bitów i typ kodowania. Usługa mowy obsługuje 24 kHz, 16 kHz i 8 kHz danych wyjściowych audio.

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
> Jeśli wybrany głosu i format danych wyjściowych inne szybkości transmisji bitów, audio jest próbkowany zgodnie z potrzebami. Jednakże głosy 24 kHz nie obsługują formatów danych wyjściowych `audio-16khz-16kbps-mono-siren` i `riff-16khz-16kbps-mono-siren`.

### <a name="request-body"></a>Treść żądania

Treść każdego żądania `POST` jest wysyłana jako [Język znaczników syntezy mowy (SSML)](speech-synthesis-markup.md). SSML umożliwia wybranie głosu i języka przez funkcję syntezy mowy zwracaną przez usługę zamiany tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz temat [Obsługa języków](language-support.md#text-to-speech).

> [!NOTE]
> W przypadku użycia niestandardowego głosu treść żądania może być wysyłana jako zwykły tekst (ASCII lub UTF-8).

### <a name="sample-request"></a>Przykładowe żądanie

To żądanie HTTP używa SSML w celu określenia połączeń głosowych i języka. Treść nie może przekraczać 1000 znaków.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-JessaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Zobacz nasze Przewodniki Szybki Start, aby poznać przykłady dotyczące języka:

* [.NET Core, C#](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi wskazuje sukces lub typowych błędów.

| Kod stanu HTTP | Opis | Możliwa przyczyna |
|------------------|-------------|-----------------|
| 200 | OK | Żądanie powiodło się; treść odpowiedzi jest plik audio. |
| 400 | Nieprawidłowe żądanie | Wymagany parametr jest Brak, pusta lub równa null. Lub wartość przekazana do każdego wymaganego lub opcjonalnego parametru jest nieprawidłowa. Typowym problemem jest nagłówkiem, który jest za długi. |
| 401 | Brak autoryzacji | Żądanie nie jest autoryzowany. Zaznacz, aby upewnić się, że klucz subskrypcji lub token jest prawidłowy i w poprawny region. |
| 413 | Jednostka żądania jest zbyt duża | Dane wejściowe SSML jest dłuższa niż 1024 znaki. |
| 415 | Nieobsługiwany typ nośnika | Istnieje możliwość, że podano niewłaściwy `Content-Type`. `Content-Type` powinna być ustawiona na `application/ssml+xml`. |
| 429 | Zbyt wiele żądań | Przekroczono limit przydziału lub liczbę żądań dozwoloną przez subskrypcję. |
| 502 | Zła brama | Problem z siecią lub po stronie serwera. Może również oznaczać nieprawidłowy nagłówek. |

W przypadku stanu HTTP `200 OK`, treść odpowiedzi zawiera plik audio w formacie żądanej. Ten plik można odtwarzać, jak ma przesyłane, zapisany do buforu lub zapisany w pliku.

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
