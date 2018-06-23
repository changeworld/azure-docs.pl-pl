---
title: Tekst na mowę interfejsu API usługi Microsoft mowy | Dokumentacja firmy Microsoft
description: Użyj tekst na mowę interfejsu API, aby zapewnić tekst na mowę konwersji w czasie rzeczywistym w różnych głosy i języki
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 03/16/2017
ms.author: priyar
ms.openlocfilehash: 4b633cefa37c11511a8171d5a7f61b03dfaa4466
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347629"
---
# <a name="bing-text-to-speech-api"></a>Bing tekst na mowę interfejsu API

## <a name="Introduction"></a>Wprowadzenie

Z usługi Bing tekst na mowę interfejsu API aplikacji mogą wysyłać żądania HTTP do chmury, gdy tekst jest natychmiastowe syntezatora do personelu pomiarowe mowy i serwer zwracane w postaci pliku audio. Ten interfejs API umożliwia w wielu różnych kontekstach Podaj tekst na mowę konwersji w czasie rzeczywistym w wielu różnych głosów i języków.

## <a name="VoiceSynReq"></a>Żądanie syntezy głosu

### <a name="Subscription"></a>Token autoryzacji

Każde żądanie syntezy głosu wymaga tokenu dostępu tokenu Web JSON (JWT). Token dostępu JWT jest przekazywana w nagłówku żądania mowy. Token ma czasu wygaśnięcia 10 minut. Aby uzyskać informacji o subskrypcji i uzyskania kluczy interfejsu API, które są używane do pobierania prawidłowych tokenów dostępu JWT, zobacz [kognitywnych subskrypcji usługi](https://azure.microsoft.com/try/cognitive-services/).

Klucz interfejsu API jest przekazywany do usługi tokenu. Na przykład:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Informacje nagłówka wymagane dla tokenu dostępu ma następującą składnię.

Name (Nazwa)| Format | Opis
----|----|----
OCP-Apim subskrypcji — klawisz | ASCII | Twój klucz subskrypcji

Usługa tokenu zwraca token dostępu JWT jako `text/plain`. Następnie tokenu JWT jest przekazywany jako `Base64 access_token` do punktu końcowego mowy jako nagłówek autoryzacji poprzedzona ciągiem `Bearer`. Na przykład:

`Authorization: Bearer [Base64 access_token]`

Klienci muszą używać następujący punkt końcowy w celu uzyskania dostępu do zamiany tekstu na mowę usługi:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Dopóki kluczem subskrypcji zostały nabyte tokenu dostępu, zgodnie z wcześniejszym opisem, generuje ten link `403 Forbidden` błąd odpowiedzi.

### <a name="Http"></a>Nagłówki HTTP

W poniższej tabeli przedstawiono nagłówków HTTP, które są używane dla żądań syntezy głosu.

Nagłówek |Wartość |Komentarze
----|----|----
Content-Type | Aplikacja/ssml + xml | Typ danych wejściowych zawartości.
X-Microsoft-OutputFormat | **1.** ssml-16 khz-16-bitowych-mono-tts <br> **2.** raw-16 khz-16-bitowych-mono-pcm <br>**3.** audio-16 khz-16 KB/s-mono-siren <br> **4.** riff-16 khz-16 KB/s-mono-siren <br> **5.** riff-16 khz-16-bitowych-mono-pcm <br> **6.** audio-16 khz-128kbitrate-mono-mp3 <br> **7.** audio-16 khz-64kbitrate-mono-mp3 <br> **8.** audio-16 khz-32kbitrate-mono-mp3 | Format audio danych wyjściowych.
AppId-X-wyszukiwania | Identyfikator GUID (tylko hex, nie łączniki) | Identyfikator, który unikatowo identyfikuje aplikację klienta. Może to być identyfikator magazynu dla aplikacji. Jeśli jedno nie jest dostępna, identyfikator może być generowany dla aplikacji użytkownika.
ClientID-X-wyszukiwania | Identyfikator GUID (tylko hex, nie łączniki) | Identyfikator, który unikatowo identyfikuje wystąpienia aplikacji dla każdej instalacji.
Agent użytkownika | Nazwa aplikacji | Nazwa aplikacji jest wymagana i musi być krótsza niż 255 znaków.
Autoryzacja | Token autoryzacji |  Zobacz <a href="#Subscription">token autoryzacji</a> sekcji.

### <a name="InputParam"></a>Parametry wejściowe

Żądania do usługi Bing tekst na mowę interfejsu API są wykonywane przy użyciu wywołania metody POST protokołu HTTP. Nagłówki są określone w poprzedniej sekcji. Treść zawiera reprezentuje tekst, który ma być syntezatora danych wejściowych mowy syntezy Markup Language (SSML). Opis znaczników kontrolować aspektów mowy, takich jak języka i płci prelegenta, zobacz [specyfikacji W3C SSML](http://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Maksymalny rozmiar dane wejściowe SSML, która jest obsługiwana jest 1024 znaków, łącznie ze wszystkimi tagami.

###  <a name="SampleVoiceOR"></a>Przykład: głosu danych wyjściowych żądań

Przykładem żądania wyjścia głos jest następujący:

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

Bing tekst na mowę interfejsu API używa protokołu HTTP POST do wysyłania audio do klienta. Odpowiedzi interfejsu API zawiera strumieniem audio i koder-dekoder i jest on zgodny format zażądał danych wyjściowych. Audio zwrócony dla danego żądania nie może przekraczać 15 sekund.

### <a name="SuccessfulRecResponse"></a>Przykład: pomyślnie syntezy odpowiedzi

Następujący kod jest przykładem odpowiedź w formacie JSON na żądanie syntezy głosu powiodło się. Komentarze i formatowanie kodu są przeznaczone tylko w tym przykładzie i zostały pominięte z rzeczywistego odpowiedzi.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Przykład: syntezy awarii

Poniższy przykład kodu pokazuje JSON odpowiedzi na błąd kwerendy syntezy głosu:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Błąd odpowiedzi

Błąd | Opis
----|----
Nieprawidłowe żądanie HTTP/400 | Wymagany parametr jest Brak, pusty lub wartość null lub wartość przekazywana do każdego wymaganego lub opcjonalnego parametru jest nieprawidłowa. Jedną z przyczyn uzyskiwania odpowiedzi "nieprawidłowe" jest przekazanie wartość ciągu, której długość przekracza dozwoloną długość. Znajduje się krótki opis parametru powodować problemy.
HTTP/401 nieautoryzowane | Żądanie nie jest autoryzowane.
HTTP/413 RequestEntityTooLarge  | Dane wejściowe SSML jest większy niż co jest obsługiwane.
HTTP/502 BadGateway | Istnieje problem związany z siecią lub problem po stronie serwera.

Przykład odpowiedź o błędzie jest następujący:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Zmiana danych wyjściowych głosu za pośrednictwem SSML

Interfejs API zamiany tekstu na mowę Microsoft obsługuje SSML 1.0, zgodnie z definicją w W3C [mowy syntezy Markup Language (SSML) w wersji 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). W tej sekcji przedstawiono przykłady zmiany niektórych właściwości dane wyjściowe wygenerowane głosu podobne mówiąc szybkości wymowy itp. przy użyciu tagów SSML.

1. Dodawanie podziału

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
  ```

2. Wymowy szybkość zmian

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

3. Wymowy

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
  ```

4. Zmień woluminu

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

5. Zmiana wysokości

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

6. Rozkład prosody zmiany

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> Uwaga danych musi być 8k lub 16 wav k usterki w następującym formacie: **kod CRC** (CRC 32): 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ 0xFFFFFFFF; **Flagi format audio**: 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ 0xFFFFFFFF; **Liczba próbek**: 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ 0x7FFFFFFF; **Rozmiaru binarne treści**: 4 bajty (DWORD) z prawidłowym zakresem 0x00000000 ~ 0x7FFFFFFF; **Binarne treści**: n bajtów.

## <a name="SampleApp"></a>Przykładowa aplikacja

Aby uzyskać szczegóły implementacji, zobacz [Visual C# .NET tekst na mowę przykładowej aplikacji](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Czcionki głosu i obsługiwanych ustawień regionalnych

W poniższej tabeli przedstawiono niektóre powiązane głosu czcionek i obsługiwanych ustawień regionalnych.

Ustawienia regionalne | Płeć | Mapowanie nazw usługi
---------|--------|------------
ar np * | Kobieta | "Microsoft Server mowy tekstu na głos mowy (ar np Hoda)"
ar-SA | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (ar-SA, Naayf)"
bg-BG | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (bg-BG Ivanowi)"
ES urzędu certyfikacji | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (Kanada ES, HerenaRUS)"
cs-CZ | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (cs-CZ, Jakub)"
Ciemny da | Kobieta | "Microsoft Server mowy tekstu na głos mowy (da k HelleRUS)"
Niemcy AT | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (de-AT Michael)"
Niemcy CH | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (de-CH Karsten)"
de-DE | Kobieta | "Microsoft Server mowy tekstu na głos mowy (de-DE, Hedda)"
de-DE | Kobieta | "Microsoft Server mowy tekstu na głos mowy (de-DE, HeddaRUS)"
de-DE | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (de-DE, Stefan, Apollo)"
el-GR | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (el-GR Stefanos)"
EN-AU | Kobieta | "Microsoft Server mowy tekstu na głos mowy (en-AU Catherine)"
EN-AU | Kobieta | "Microsoft Server mowy tekstu na głos mowy (en-AU HayleyRUS)"
EN-CA | Kobieta | "Microsoft Server mowy tekstu na głos mowy (en-CA Anna)"
EN-CA | Kobieta | "Microsoft Server mowy tekstu na głos mowy (en-CA HeatherRUS)"
pl pl. | Kobieta | "Microsoft Server mowy tekstu na głos mowy (en-GB, Susan, Apollo)"
pl pl. | Kobieta | "Microsoft Server mowy tekstu na głos mowy (pl pl. HazelRUS)"
pl pl. | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (en-GB, George, Apollo)"
EN-IE | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (en-IE Piotr)"
EN w | Kobieta | "Microsoft Server mowy tekstu na głos mowy (en-IN, Heera, Apollo)"
EN w | Kobieta | "Microsoft Server mowy tekstu na głos mowy (en-IN PriyaRUS)"
EN w | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (en-IN, Ravi, Apollo)"
pl-PL | Kobieta | "Microsoft Server mowy tekstu na głos mowy (en US, ZiraRUS)"
pl-PL | Kobieta | "Microsoft Server mowy tekstu na głos mowy (en US, JessaRUS)"
pl-PL | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (en US, BenjaminRUS)"
es-ES | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (es-ES, Laura, Apollo)"
es-ES | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (es-ES, HelenaRUS)"
es-ES | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (es-ES, urządzenia Pablo, Apollo)"
es-MX | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (es-MX, HildaRUS)"
es-MX | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (es-MX, Raul, Apollo)"
fi-FI | Kobieta | "Microsoft Server mowy tekstu na głos mowy (fi-FI, HeidiRUS)"
fr-CA | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (fr-CA Caroline)"
fr-CA | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (fr-CA HarmonieRUS)"
FR CH | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (fr-CH Guillaume)"
fr-FR | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (fr-FR, Julie, Apollo)"
fr-FR | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (fr-FR, HortenseRUS)"
fr-FR | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (fr-FR, Pawła, Apollo)"
HE-IL| Mężczyzna| "Microsoft Server mowy tekstu na głos mowy (he-IL Asaf)"
w | Kobieta | "Microsoft Server mowy tekstu na głos mowy (hi-IN, Kalpana, Apollo)"
w | Kobieta | "Microsoft Server mowy tekstu na głos mowy (hi-IN, Kalpana)"
w | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (hi-IN, Hemant)"
hr HR | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (hr-HR Matej)"
hu-HU | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (hu-HU, Szabolcs)"
id-ID | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (identyfikator ID, Andika)"
IT-IT | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (it-IT, Cosimo, Apollo)"
ja-JP | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (ja-JP, Ayumi, Apollo)"
ja-JP | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (ja-JP, Ichiro, Apollo)"
ja-JP | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (ja-JP, HarukaRUS)"
ja-JP | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (ja-JP, LuciaRUS)"
ja-JP | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (ja-JP, EkaterinaRUS)"
ko-KR | Kobieta | "Microsoft Server mowy tekstu na głos mowy (ko-KR, HeamiRUS)"
ms-MY | Mężczyzna | "Głos tekst na mowę mowy serwera firmy Microsoft (ms Moje, Rizwan)"
nb-NO | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (nb-NO HuldaRUS)"
NL-NL | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (nl-NL, HannaRUS)"
pl-PL | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (pl-PL, PaulinaRUS)"
pt-BR | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (pt-BR, HeloisaRUS)"
pt-BR | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (pt-BR, Danielowi, Apollo)"
pt-PT | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (pt-PT HeliaRUS)"
RO RO | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (ro-RO Andrei)"
ru-RU | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (ru-RU, Irina, Apollo)"
ru-RU | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (ru-RU, Pavel, Apollo)"
sk-SK | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (sk-SK Filip)"
RO SL | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (sl-SI Lado)"
sv-SE | Kobieta | "Microsoft Server mowy tekstu na głos mowy (sv-SE, HedvigRUS)"
Ta w | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (ta-IN, Valluvar)"
TH-ty percentyl | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (th-TH Pattara)"
tr-TR | Kobieta | "Microsoft Server mowy tekst na mowę głosowe (tr-TR, SedaRUS)"
vi-VN | Mężczyzna | "Microsoft Server mowy tekst na mowę głosowe (vi-VN)"
zh-CN | Kobieta | "Microsoft Server mowy tekstu na głos mowy (zh-CN, HuihuiRUS)"
zh-CN | Kobieta | "Microsoft Server mowy tekstu na głos mowy (zh-CN, Yaoyao, Apollo)"
zh-CN | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (zh-CN, Kangkang, Apollo)"
zh-HK | Kobieta | "Microsoft Server mowy tekstu na głos mowy (zh-HK, Tracy, Apollo)"
zh-HK | Kobieta | "Microsoft Server mowy tekstu na głos mowy (zh-HK, TracyRUS)"
zh-HK | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (zh-HK, Danny, Apollo)"
zh-TW | Kobieta | "Microsoft Server mowy tekstu na głos mowy (zh-TW, Yating, Apollo)"
zh-TW | Kobieta | "Microsoft Server mowy tekstu na głos mowy (zh-TW, HanHanRUS)"
zh-TW | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (zh-TW, Zhiwei, Apollo)"
 * ar np obsługuje nowoczesnych standardowe arabski (MSA).

> [!NOTE]
> Należy pamiętać, że poprzednie nazwy usługi **Microsoft Server mowy tekstu na głos mowy (cs-CZ, Vit)** i **Microsoft Server mowy tekstu na głos mowy (en-IE Shaun)** zostaną wycofane po 3/31/2018 w Aby zoptymalizować funkcji API mowy usługi Bing. Zaktualizuj swój kod z nazwami zaktualizowane.

## <a name="TrouNSupport"></a>Rozwiązywanie problemów i pomoc techniczna

Wszystkie pytania i opisy problemów do [usługi mowy usługi Bing](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN forum. Zawierają szczegółowe informacje, takie jak:

* Przykładem ciągu pełne żądanie.
* Jeśli to konieczne, pełne dane wyjściowe nieudanych żądań, która obejmuje dziennika identyfikatorów.
* Procent żądań, które kończą się niepowodzeniem.
