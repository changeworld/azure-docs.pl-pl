---
title: Rozwiązywanie problemów rozpoznawanie mowy Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Jak rozwiązywać problemy podczas korzystania z rozpoznawanie mowy Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f222d9d4cf6c56dea0832938dcb132cf711491bc
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934057"
---
# <a name="troubleshooting-bing-speech"></a>rozpoznawanie mowy Bing rozwiązywania problemów

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Porn`HTTP 403 Forbidden`

W przypadku korzystania z interfejsu API rozpoznawania mowy zwracany `HTTP 403 Forbidden` jest błąd.

### <a name="cause"></a>Przyczyna

Ten błąd jest często spowodowany przez problemy z uwierzytelnianiem. Żądania połączenia bez prawidłowego `Ocp-Apim-Subscription-Key` lub `Authorization` nagłówka są odrzucane `HTTP 403 Forbidden` przez usługę z odpowiedzią.

Jeśli używasz klucza subskrypcji do uwierzytelniania, przyczyna może być

- Brak klucza subskrypcji lub jest on nieprawidłowy
- Przekroczono limit przydziału użycia klucza subskrypcji
- `Ocp-Apim-Subscription-Key` pole nie jest ustawione w nagłówku żądania, gdy wywoływany jest interfejs API REST

Jeśli używasz tokenu autoryzacji do uwierzytelniania, następujące przyczyny mogą spowodować wystąpienie błędu.

- w żądaniu brakuje nagłówka w czasie korzystania z usługi REST `Authorization`
- Token autoryzacji określony w nagłówku autoryzacji jest nieprawidłowy
- Token autoryzacji wygasł. Token dostępu ma ważność 10 minut

Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz stronę [uwierzytelnianie](How-to/how-to-authentication.md) .

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

#### <a name="verify-that-your-subscription-key-is-valid"></a>Sprawdź, czy klucz subskrypcji jest prawidłowy

Aby przeprowadzić weryfikację, można uruchomić następujące polecenie. Pamiętaj, aby zastąpić *YOUR_SUBSCRIPTION_KEY* własnym kluczem subskrypcji. Jeśli klucz subskrypcji jest prawidłowy, otrzymujesz odpowiedź tokenu autoryzacji jako tokenu sieci Web JSON (JWT). W przeciwnym razie pojawi się błąd w odpowiedzi.

> [!NOTE]
> Zamień `YOUR_SUBSCRIPTION_KEY` na własny klucz subskrypcji.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[odsłon](#tab/curl)

W przykładzie zastosowano zwinięcie w systemie Linux z bash. Jeśli nie jest on dostępny na twojej platformie, może być konieczne zainstalowanie zawieszania. Przykład powinien zadziałał także na Cygwin w systemie Windows, Git bash, zsh i innych powłokach.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Upewnij się, że używasz tego samego klucza subskrypcji w aplikacji lub w żądaniu REST używanym powyżej.

#### <a name="verify-the-authorization-token"></a>Weryfikowanie tokenu autoryzacji

Ten krok jest wymagany tylko wtedy, gdy do uwierzytelniania jest używany Token autoryzacji. Uruchom następujące polecenie, aby sprawdzić, czy token autoryzacji jest nadal ważny. Polecenie wysyła żądanie POST do usługi i oczekuje komunikatu odpowiedzi z usługi. Jeśli nadal pojawia się błąd `403 Forbidden` http, sprawdź, czy token dostępu został poprawnie ustawiony i nie wygasł.

> [!IMPORTANT]
> Token ma ważność 10 minut.
> [!NOTE]
> Zamień `YOUR_AUDIO_FILE` na ścieżkę do zarejestrowanego pliku audio i `YOUR_ACCESS_TOKEN` z tokenem autoryzacji zwróconym w poprzednim kroku.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[odsłon](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Porn`HTTP 400 Bad Request`

Przyczyną tego jest zwykle, że treść żądania zawiera nieprawidłowe dane audio. Obecnie obsługujemy tylko plik WAV.

## <a name="error-http-408-request-timeout"></a>Porn`HTTP 408 Request Timeout`

Błąd jest najprawdopodobniej dlatego, że żadne dane audio nie są wysyłane do usługi, a usługa zwraca ten błąd po przekroczeniu limitu czasu. W przypadku interfejsu API REST dane audio należy umieścić w treści żądania.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` W odpowiedzi jest`InitialSilenceTimeout`

Dane audio są zwykle przyczyną problemu. Na przykład

- dźwięk ma długi czas ciszy na początku. Usługa przestanie być rozpoznawana po upływie pewnej liczby `InitialSilenceTimeout`sekund i zwraca wartość.
- dźwięk używa nieobsługiwanego formatu kodek, co sprawia, że dane audio są traktowane jako wyciszenie.
