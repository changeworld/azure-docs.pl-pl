---
title: interfejs API zamiany tekstu na mowę usługi Microsoft Speech | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Korzystanie z interfejsu API zamiany tekstu na mowę w celu zapewnienia konwersji zamiany tekstu na mowę w czasie rzeczywistym na różne głosy i Języki
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ee9b0b47fb88cba948bc06db6eb83fe9c076fe40
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966870"
---
# <a name="bing-text-to-speech-api"></a>Interfejs API zamiany tekstu Bing na mowę

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Wprowadzenie

Dzięki interfejsowi API zamiany tekstu Bing na mowę aplikacja może wysyłać żądania HTTP do serwera w chmurze, w którym tekst jest natychmiastowo syntezą głosu i zwracany jako plik dźwiękowy. Ten interfejs API może być używany w wielu różnych kontekstach w celu zapewnienia konwersji zamiany tekstu na mowę w czasie rzeczywistym na wiele różnych głosów i języków.

## <a name="VoiceSynReq"></a>Żądanie syntezy głosu

### <a name="Subscription"></a>Token autoryzacji

Każde żądanie syntezy głosowej wymaga tokenu dostępu tokenu sieci Web JSON (JWT). Token dostępu JWT jest przesyłany przez nagłówek żądania mowy. Token ma czas wygaśnięcia wynoszący 10 minut. Aby uzyskać informacje na temat subskrybowania i uzyskiwania kluczy interfejsu API, które są używane do pobierania prawidłowych tokenów dostępu JWT, zobacz [Cognitive Services Subscription](https://azure.microsoft.com/try/cognitive-services/).

Klucz interfejsu API jest przesyłany do usługi tokenu. Na przykład:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Informacje nagłówka wymagane w celu uzyskania dostępu do tokenu są następujące.

Name| Format | Opis
----|----|----
OCP-Apim-Subscription-Key | ASCII | Twój klucz subskrypcji

Usługa tokenu zwraca token dostępu JWT jako `text/plain`. Następnie token JWT jest przesyłany `Base64 access_token` do punktu końcowego mowy jako nagłówek autoryzacji poprzedzony ciągiem. `Bearer` Na przykład:

`Authorization: Bearer [Base64 access_token]`

Aby uzyskać dostęp do usługi zamiany tekstu na mowę, klienci muszą używać następującego punktu końcowego:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Do momentu uzyskania tokenu dostępu z kluczem subskrypcji zgodnie z wcześniejszym opisem ten link spowoduje wygenerowanie `403 Forbidden` błędu odpowiedzi.

### <a name="Http"></a>Nagłówki HTTP

W poniższej tabeli przedstawiono nagłówki HTTP, które są używane na potrzeby żądań syntezy głosu.

nagłówek |Value |Komentarze
----|----|----
Content-Type | Application/SSML + XML | Typ zawartości wejściowej.
X-OutputFormat | **1.** SSML-16khz-16bit-mono-TTS <br> **2.** RAW-16khz-16bit-mono-PCM <br>**3.** audio-16khz-16kbps-mono-Siren <br> **4.** RIFF-16khz-16kbps-mono-Siren <br> **5.** RIFF-16khz-16bit-mono-PCM <br> **6.** audio-16khz-128kbitrate-mono-MP3 <br> **7.** audio-16khz-64kbitrate-mono-MP3 <br> **8.** audio-16khz-32kbitrate-mono-MP3 | Wyjściowy format audio.
X-Search-AppId | Identyfikator GUID (tylko szesnastkowo, bez kresek) | Identyfikator, który jednoznacznie identyfikuje aplikację kliencką. Może to być identyfikator sklepu dla aplikacji. Jeśli nie jest dostępny, identyfikator może być wygenerowany przez użytkownika dla aplikacji.
X-Search-ClientID | Identyfikator GUID (tylko szesnastkowo, bez kresek) | Identyfikator, który jednoznacznie identyfikuje wystąpienie aplikacji dla każdej instalacji.
User-Agent | Nazwa aplikacji | Nazwa aplikacji jest wymagana i musi być krótsza niż 255 znaków.
Authorization | Token autoryzacji |  Zobacz sekcję <a href="#Subscription">Token autoryzacji</a> .

### <a name="InputParam"></a>Parametry wejściowe

Żądania do interfejsu API zamiany mowy na tekst Bing są wykonywane za pomocą wywołań POST protokołu HTTP. Nagłówki są określone w poprzedniej sekcji. Treść zawiera dane wejściowe języka SSML (Speech synteza Markup Language), które reprezentuje tekst do wypróbowania. Opis znacznika służący do kontrolowania aspektów mowy, takich jak język i płeć osoby mówiącej, można znaleźć w [specyfikacji W3C SSML](https://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Maksymalny rozmiar obsługiwanego wejścia SSML to 1 024 znaków, łącznie ze wszystkimi tagami.

###  <a name="SampleVoiceOR"></a>Przykład: żądanie wyjściowe głosu

Przykładem żądania wyjścia głosowego jest:

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Odpowiedź na dane wyjściowe głosu

Interfejs API zamiany tekstu Bing na mowę używa protokołu HTTP POST, aby wysłać dźwięk z powrotem do klienta. Odpowiedź interfejsu API zawiera strumień audio i koder-dekoder, który jest zgodny z żądanym formatem danych wyjściowych. Dźwięk zwrócony dla danego żądania nie może przekroczyć 15 sekund.

### <a name="SuccessfulRecResponse"></a>Przykład: Pomyślne odpowiedzi syntezy

Poniższy kod stanowi przykład odpowiedzi JSON do pomyślnego żądania syntezy głosu. Komentarze i formatowanie kodu są przeznaczone do celów tego przykładu i pomijane w rzeczywistej odpowiedzi.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Przykład: błąd syntezy

Poniższy przykładowy kod przedstawia odpowiedź JSON do niepowodzenia zapytania syntezy głosowej:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Odpowiedzi na błędy

Błąd | Opis
----|----
Nieprawidłowe żądanie HTTP/400 | Brak wymaganego parametru, jest on pusty lub ma wartość null lub jest on nieprawidłowy. Jedną z przyczyn uzyskania odpowiedzi "Nieprawidłowa" jest przekazanie wartości ciągu przekraczającej dozwoloną długość. Zostanie uwzględniony Krótki opis problematycznego parametru.
HTTP/401 — nieautoryzowany | Żądanie nie jest autoryzowany.
HTTP/413 RequestEntityTooLarge  | Dane wejściowe SSML są większe niż obsługiwane.
HTTP/502 BadGateway | Występuje problem związany z siecią lub problem po stronie serwera.

Przykładem odpowiedzi na błąd jest następujący:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Zmiana danych wyjściowych głosu za pośrednictwem SSML

Funkcja zamiany tekstu w Speech API firmy Microsoft obsługuje SSML 1,0, zgodnie z definicją w [języku W3C Speech synteza Markup Language (SSML) w wersji 1,0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). W tej sekcji przedstawiono przykłady zmiany pewnych cech wygenerowanych danych wyjściowych, takich jak częstotliwość mówienia, wymowa itp., przy użyciu tagów SSML.

1. Dodawanie przerwy

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
   ```

2. Zmień częstotliwość wypowiedzi

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

3. Wymowa

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
   ```

4. Zmiany woluminu

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

5. Zmień wysokość

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

6. Zmień rozkład Prosody

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> Zwróć uwagę, że dane audio należy 8k lub z 16 KB, które zostały zapisane w następującym formacie: **Kod CRC** (CRC-32): 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ ~; **Flaga formatu audio**: 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ ~; **Liczba próbek**: 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ 0x7FFFFFFF; **Rozmiar treści binarnej**: 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ 0x7FFFFFFF; **Treść binarna**: n bajtów.

## <a name="SampleApp"></a>Przykładowa aplikacja

Aby uzyskać szczegółowe informacje dotyczące implementacji, zobacz [Przykładowa aplikacja do zamiany tekstu na mowę w języku Visual C#.NET](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Obsługiwane ustawienia regionalne i czcionki głosowe

W poniższej tabeli przedstawiono niektóre obsługiwane ustawienia regionalne i powiązane z nimi czcionki głosowe.

Ustawienia regionalne | Płeć | Mapowanie nazwy usługi
---------|--------|------------
AR-EG * | Kobieta | "Microsoft Server mowy tekstu na głos mowy (ar np Hoda)"
ar-SA | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ar-SA Naayf)"
bg-BG | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (bg-BG Ivanowi)"
ES urzędu certyfikacji | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (Kanada ES, HerenaRUS)"
cs-CZ | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (cs-CZ, Jakub)"
Akcelerator deweloperski w wersji DK | Kobieta | "Microsoft Server mowy Text na głos mowy (da-DK HelleRUS)"
de-AT | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-AT, Michael)"
de-CH | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-CH, Karsten)"
de-DE. | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, Hedda)"
de-DE. | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, HeddaRUS)"
de-DE. | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, Stefan, Apollo)"
el-GR | Mężczyzna | "Microsoft Server mowy Text na głos mowy (el-GR Stefanos)"
EN-AU | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-AU, Catherine)"
EN-AU | Kobieta | "Microsoft Server mowy Text na głos mowy (en-AU HayleyRUS)"
EN-CA | Kobieta | "Microsoft Server mowy Text na głos mowy (en-CA Anna)"
EN-CA | Kobieta | "Microsoft Server mowy Text na głos mowy (en-CA HeatherRUS)"
en-GB | Kobieta | "Microsoft Server mowy Text na głos mowy (en-GB Susan, Apollo)"
en-GB | Kobieta | "Microsoft Server mowy Text na głos mowy (en-GB HazelRUS)"
en-GB | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-GB George, Apollo)"
EN-IE | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-IE, Sean)"
EN-IN | Kobieta | "Microsoft Server mowy Text na głos mowy (en-IN, Heera, Apollo)"
EN-IN | Kobieta | "Microsoft Server mowy Text na głos mowy (en-IN PriyaRUS)"
EN-IN | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-IN, Ravi, Apollo)"
en-US | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, ZiraRUS)"
en-US | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, JessaRUS)"
en-US | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en US, BenjaminRUS)"
es-ES | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, Laura, Apollo)"
es-ES | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, HelenaRUS)"
es-ES | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, urządzenia Pablo, Apollo)"
es-MX | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosu (es-MX, HildaRUS)"
es-MX | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosu (es-MX, Raul, Apollo)"
fi-FI | Kobieta | "Microsoft Server mowy Text na głos mowy (fi-FI, HeidiRUS)"
fr-CA | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CA Caroline)"
fr-CA | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CA HarmonieRUS)"
FR-CH | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CH, Guillaume)"
fr-FR | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, Julie, Apollo)"
fr-FR | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, HortenseRUS)"
fr-FR | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, Paul, Apollo)"
HE-IL| Mężczyzna| "Microsoft Server mowy Text na głos mowy (he-IL Asaf)"
w | Kobieta | "Microsoft Server mowy Text na głos mowy (hi-IN, Kalpana, Apollo)"
w | Kobieta | "Microsoft Server mowy Text na głos mowy (cześć IN, Kalpana)"
w | Mężczyzna | "Microsoft Server mowy Text na głos mowy (cześć IN, Hemant)"
hr-HR | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (hr-HR Matej)"
hu-HU | Mężczyzna | "Microsoft Server mowy Text na głos mowy (hu-HU, Szabolcs)"
id-ID | Mężczyzna | "Microsoft Server mowy Text na głos mowy (identyfikator ID, Andika)"
IT-IT | Mężczyzna | "Microsoft Server mowy Text na głos mowy (it-IT, Cosimo, Apollo)"
IT-IT | Kobieta | "Microsoft Server mowy Text na głos mowy (it-IT, LuciaRUS)"
ja-JP | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, Ayumi, Apollo)"
ja-JP | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, Ichiro, Apollo)"
ja-JP | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, HarukaRUS)"
ko-KR | Kobieta | "Microsoft Server mowy Text na głos mowy (ko-KR, HeamiRUS)"
ms-MY | Mężczyzna | "Głosu zamiany tekstu na mowę mowy serwera firmy Microsoft (ms Moje, Rizwan)"
nb-NO | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosu (nb — bez HuldaRUS)"
NL-NL | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (nl-NL, HannaRUS)"
pl-PL | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pl-PL, PaulinaRUS)"
pt-BR | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-BR, HeloisaRUS)"
pt-BR | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-BR, Daniel, Apollo)"
pt-PT | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-PT, HeliaRUS)"
RO RO | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ro-RO Andrei)"
ru-RU | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ru-RU, Irina, Apollo)"
ru-RU | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (Apollo Pavel, ru-RU)"
ru-RU | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ru-RU, EkaterinaRUS)"
sk-SK | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (sk-SK Filip)"
sl SI | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (sl-SI Lado)"
sv-SE | Kobieta | "Microsoft Server mowy Text na głos mowy (sv-SE, HedvigRUS)"
Ta w | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ta-IN, Valluvar)"
th TH | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (th-TH Pattara)"
tr-TR | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (tr-TR, SedaRUS)"
vi-VN | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosu (vi-VN)"
zh-CN | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-CN, HuihuiRUS)"
zh-CN | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-CN, Yaoyao, Apollo)"
zh-CN | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-CN, Kangkang, Apollo)"
zh-HK | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-HK Tracy, Apollo)"
zh-HK | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-HK TracyRUS)"
zh-HK | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-HK Danny, Apollo)"
zh-TW | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-TW, Yating, Apollo)"
zh-TW | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-TW HanHanRUS)"
zh-TW | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-TW, Zhiwei, Apollo)"

 \* AR-EG obsługuje nowoczesne standardowe arabski (MSA).

> [!NOTE]
> Należy pamiętać, że poprzednie nazwy usług **Microsoft Server speech Zamiana tekstu na mowę Voice (cs-cz, VIT)** i **microsoft Server Speech Zamiana tekstu na mowę Voice (EN-IE, Shaun)** będą przestarzałe po 3/31/2018, aby zoptymalizować interfejs API rozpoznawania mowy Bing możliwość. Zaktualizuj kod przy użyciu zaktualizowanych nazw.

## <a name="TrouNSupport"></a>Rozwiązywanie problemów i pomoc techniczna

Opublikuj wszystkie pytania i problemy na forum [usługi rozpoznawanie mowy Bing](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN. Dołącz pełne szczegóły, takie jak:

* Przykład pełnego ciągu żądania.
* Jeśli ma to zastosowanie, pełne dane wyjściowe żądania zakończonego niepowodzeniem, w tym identyfikatory dzienników.
* Procent żądań, które kończą się niepowodzeniem.
