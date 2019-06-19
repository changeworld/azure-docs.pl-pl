---
title: Tekst na mowę interfejsu API usługi Microsoft Speech | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Użyj zamiany tekstu na mowę interfejsu API, aby podać konwersji tekstu na mowę w czasie rzeczywistym w wielu różnych głosów i języków
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: priyar
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a046bec5d81d828d88716d31c84e9cbcdcea1a08
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515423"
---
# <a name="bing-text-to-speech-api"></a>Tekst do API rozpoznawania mowy Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Wprowadzenie

Za pomocą usługi Bing zamiany tekstu na mowę interfejsu API aplikacja może wysyłać żądania HTTP do serwera chmurowego, gdzie tekst jest natychmiast przekształcony na mowę brzmiącą człowieka i zwracane w postaci pliku audio. Ten interfejs API może służyć w wielu różnych kontekstach zapewnienie konwersji tekstu na mowę w czasie rzeczywistym w wielu różnych głosów i języków.

## <a name="VoiceSynReq"></a>Żądanie syntezy głosu

### <a name="Subscription"></a>Token autoryzacji

Każde żądanie syntezy głosu wymaga tokenu dostępu tokenu Web JSON (JWT). Rozszerzenie JWT token dostępu jest przekazywane w nagłówku żądania mowy. Token ma czas wygaśnięcia 10 minut. Aby uzyskać informacji o subskrypcji i uzyskania kluczy interfejsu API, które są używane do pobierania tokenów dostępu w usłudze prawidłowy token JWT, zobacz [subskrypcja usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Klucz interfejsu API jest przekazywany do usługi tokenu. Na przykład:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Informacje nagłówka wymagane dla tokenu dostępu jest w następujący sposób.

Name (Nazwa)| Format | Opis
----|----|----
OCP-Apim-Subscription-Key | ASCII | Klucz subskrypcji

Zwraca wartość usługi tokenu JWT token dostępu jako `text/plain`. Następnie tokenu JWT jest przekazywany jako `Base64 access_token` na punkt końcowy rozpoznawania mowy jako nagłówek autoryzacji prefiksem ciągu `Bearer`. Na przykład:

`Authorization: Bearer [Base64 access_token]`

Klienci muszą używać następujący punkt końcowy dostępu do zamiany tekstu na mowę usługi:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Dopóki nie posiadasz token dostępu z kluczem subskrypcji zgodnie z wcześniejszym opisem, generuje ten link `403 Forbidden` błąd odpowiedzi.

### <a name="Http"></a>Nagłówki HTTP

W poniższej tabeli przedstawiono nagłówki HTTP, które są używane na potrzeby syntezy polecenia głosowe.

nagłówek |Wartość |Komentarze
----|----|----
Content-Type | Aplikacja/ssml + xml | Typ zawartości danych wejściowych.
X-Microsoft-OutputFormat | **1.** ssml-16khz-16bit-mono-tts <br> **2.** pierwotne-16 khz-16-bitowych-mono-pcm <br>**3.** audio-16 khz-16 KB/s-mono-siren <br> **4.** riff-16 khz-16 KB/s-mono-siren <br> **5.** riff-16 khz-16-bitowych-mono-pcm <br> **6.** audio-16 khz-128kbitrate-mono-mp3 <br> **7.** audio-16 khz-64kbitrate-mono-mp3 <br> **8.** audio-16 khz-32kbitrate-mono-mp3 | Format danych wyjściowych audio.
X-Search-AppId | Identyfikator GUID (tylko szesnastkowy, nie kresek) | Identyfikator, który unikatowo identyfikuje aplikację klienta. Może to być identyfikator magazynu dla aplikacji. Jeśli nie jest dostępny, identyfikator może być generowany dla aplikacji użytkownika.
X-Search-ClientID | Identyfikator GUID (tylko szesnastkowy, nie kresek) | Identyfikator, który jednoznacznie identyfikuje wystąpienie aplikacji dla każdej instalacji.
User-Agent | Nazwa aplikacji | Nazwa aplikacji jest wymagany i musi zawierać mniej niż 255 znaków.
Authorization | Token autoryzacji |  Zobacz <a href="#Subscription">token autoryzacji</a> sekcji.

### <a name="InputParam"></a>Parametry wejściowe

Żądania do usługi Bing tekst na mowę interfejsu API są wykonywane, przy użyciu wywołania HTTP POST. Nagłówki są określone w poprzedniej sekcji. Treść zawiera dane wejściowe mowy syntezy Markup Language (SSML), który reprezentuje tekst, który ma zostać przekształcony. Opis znaczniki używane do sterowania aspektami mowy, takie jak język i płeć osoby mówiącej, zobacz [specyfikacji W3C SSML](https://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Maksymalny rozmiar danych wejściowych SSML, która jest obsługiwana jest 1024 znaków, łącznie ze wszystkimi tagami.

###  <a name="SampleVoiceOR"></a>Przykład: głosowych danych wyjściowych żądań

Przykładem żądania wyjścia głos jest następująca:

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

## <a name="VoiceOutResponse"></a>Głos dane wyjściowe odpowiedzi

Mowy na tekst Bing interfejsu API używa metody POST protokołu HTTP do odesłania audio do klienta. Odpowiedź interfejsu API zawiera strumień audio i kodera-dekodera i dopasowuje format zażądał danych wyjściowych. Dźwięk zwrócony dla danego żądania nie może przekraczać 15 sekund.

### <a name="SuccessfulRecResponse"></a>Przykład: syntezy pomyślne odpowiedzi

Poniższy kod jest przykładem odpowiedź w formacie JSON na żądanie syntezy pomyślne głosu. Komentarze i formatowanie kodu są przeznaczone do celów tego przykładu, tylko i zostały pominięte w właściwą odpowiedź.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Przykład: syntezy awarii

Poniższy przykład kodu pokazuje odpowiedź w formacie JSON na niepowodzenie zapytania syntezy głosu:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Odpowiedzi na błędy

Błąd | Opis
----|----
Nieprawidłowe żądanie HTTP/400 | Wymagany parametr jest Brak, jest puste lub wartość null lub wartość przekazana do każdego wymaganego lub opcjonalnego parametru jest nieprawidłowa. Jedną z przyczyn dotyczących uzyskiwania odpowiedzi "nieprawidłowy" przekazuje wartość ciągu, której długość przekracza dozwoloną długość. Krótki opis parametru problematyczne jest dołączony.
Odpowiedź HTTP/401 Brak autoryzacji | Żądanie nie jest autoryzowany.
HTTP/413 RequestEntityTooLarge  | SSML danych wejściowych jest większy niż co jest obsługiwane.
HTTP/502 BadGateway | Istnieje problem związany z siecią lub problem po stronie serwera.

Przykładem odpowiedź o błędzie jest następujący:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Zmiana wyjściowego głosowych za pośrednictwem SSML

Interfejs API zamiany tekstu na mowę firmy Microsoft obsługuje SSML 1.0, zgodnie z definicją w formacie W3C [mowy syntezy Markup Language (SSML) w wersji 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). W tej sekcji przedstawiono przykłady zmianę niektórych właściwości głosu wygenerowane dane wyjściowe, takie jak wypowiedzi szybkości Wymowa itp. przy użyciu tagów SSML.

1. Dodawanie podziału

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

6. Rozkład prosody zmiany

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> Uwaga danych musi być 8k lub 16 wav k, w następującym formacie: **Kod CRC** (CRC-32): 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ 0xFFFFFFFF; **Flagi formatu audio**: 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ 0xFFFFFFFF; **Liczba próbek**: 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ 0x7FFFFFFF; **Rozmiar dane binarne ciała**: 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ 0x7FFFFFFF; **Dane binarne ciała**: n bajtów.

## <a name="SampleApp"></a>Przykładowa aplikacja

Aby uzyskać szczegóły implementacji, zobacz [Visual C# .NET zamiany tekstu na mowę przykładowej aplikacji](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Obsługiwane ustawienia regionalne i strony czcionki głosowe

W poniższej tabeli przedstawiono niektóre z obsługiwanych ustawień regionalnych i czcionki głosowe powiązane.

Ustawienia regionalne | Płeć | Mapowanie nazwy usługi
---------|--------|------------
ar np * | Kobieta | "Microsoft Server mowy tekstu na głos mowy (ar np Hoda)"
ar-SA | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ar-SA Naayf)"
bg-BG | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (bg-BG Ivanowi)"
ES urzędu certyfikacji | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (Kanada ES, HerenaRUS)"
cs-CZ | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (cs-CZ, Jakub)"
Akcelerator deweloperski w wersji DK | Kobieta | "Microsoft Server mowy Text na głos mowy (da-DK HelleRUS)"
de-AT | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-AT, Michael)"
de-CH | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-CH, Karsten)"
de-DE. | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, Hedda)"
de-DE. | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, HeddaRUS)"
de-DE. | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-DE, Stefan, Apollo)"
el-GR | Mężczyzna | "Microsoft Server mowy Text na głos mowy (el-GR Stefanos)"
EN-AU | Kobieta | "Microsoft Server mowy Text na głos mowy (en-AU Catherine)"
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

 \* ar np obsługuje nowoczesnych standardowa arabski (MSA).

> [!NOTE]
> Należy pamiętać, że poprzedniej nazwy usług **Microsoft Server mowy tekstu na głos mowy (cs-CZ, Vit)** i **Microsoft Server mowy tekstu na głos mowy (en-IE, Shaun)** staną się przestarzałe po 3/31 stycznia 2018 r. w kolejność, aby zoptymalizować funkcje API rozpoznawania mowy Bing. Zaktualizuj swój kod przy użyciu zaktualizowanych nazw.

## <a name="TrouNSupport"></a>Rozwiązywanie problemów i pomoc techniczna

Opublikuj wszystkie pytania i problemy z [usługa rozpoznawania mowy Bing](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN forum. Zawierać szczegółowe informacje, takie jak:

* Na przykład ciąg pełnego żądania.
* Jeśli to konieczne, pełne dane wyjściowe żądania nie powiodło się, w tym rejestrowanie identyfikatorów.
* Procent żądań, które kończą się niepowodzeniem.
