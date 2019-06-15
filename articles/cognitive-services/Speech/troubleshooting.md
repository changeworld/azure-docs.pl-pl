---
title: Rozwiązywanie problemów z rozpoznawania mowy Bing | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Jak rozwiązywać problemy, korzystając z funkcji rozpoznawania mowy Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e70e7b79be7dd4ea55c56898eaf8007d25732366
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60513987"
---
# <a name="troubleshooting-bing-speech"></a>Rozwiązywanie problemów z rozpoznawania mowy Bing

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Błąd `HTTP 403 Forbidden`

Korzystając z interfejsu API rozpoznawania mowy, zwraca `HTTP 403 Forbidden` błędu.

### <a name="cause"></a>Przyczyna

Ten błąd jest często spowodowane przez problemy z uwierzytelnianiem. Żądania połączeń bez prawidłowe `Ocp-Apim-Subscription-Key` lub `Authorization` nagłówka są odrzucane przez usługę z `HTTP 403 Forbidden` odpowiedzi.

Jeśli używasz klucz subskrypcji dla uwierzytelniania, przyczyną może być

- klucz subskrypcji lub jest nieprawidłowy
- zostanie przekroczony limit przydziału użycia klucza subskrypcji
- `Ocp-Apim-Subscription-Key` pole nie jest określona w nagłówku żądania podczas wywołania interfejsu API REST

Jeśli używasz token autoryzacji do uwierzytelniania, przyczyny może spowodować błąd.

- `Authorization` Brak nagłówka w żądaniu podczas przy użyciu usługi REST
- określony w nagłówku autoryzacji token autoryzacji jest nieprawidłowy
- token autoryzacji wygasł. Token dostępu ma wygaśnięcia 10 minut

Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [uwierzytelniania](How-to/how-to-authentication.md) strony.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

#### <a name="verify-that-your-subscription-key-is-valid"></a>Sprawdź, czy klucz subskrypcji jest nieprawidłowy

Można uruchomić następujące polecenie w celu weryfikacji. Należy pamiętać, aby zastąpić *YOUR_SUBSCRIPTION_KEY* z kluczem subskrypcji. Jeśli klucz subskrypcji jest prawidłowy, pojawi się w odpowiedzi na token autoryzacji jako JSON Web Token (JWT). W przeciwnym razie wystąpi błąd w odpowiedzi.

> [!NOTE]
> Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="curltabcurl"></a>[curl](#tab/curl)

W przykładzie użyto programu curl w systemie Linux przy użyciu programu bash. Jeśli nie jest dostępny na Twojej platformie, może być konieczne do zainstalowania programu curl. Przykład powinny również działać w Cygwin na Windows, systemu Git Bash, zsh i innych powłoki.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Upewnij się, że używasz tego samego klucza subskrypcji w swojej aplikacji lub żądania REST jak używany powyżej.

#### <a name="verify-the-authorization-token"></a>Weryfikuj token autoryzacji

Ten krok jest wymagane tylko, jeśli token autoryzacji jest używany do uwierzytelniania. Uruchom następujące polecenie, aby sprawdzić, czy token autoryzacji jest nadal ważny. Polecenie wysyła żądanie POST do usługi i oczekuje, że komunikat odpowiedzi z usługi. Jeśli nadal otrzymujesz HTTP `403 Forbidden` błąd, Sprawdź dostęp token jest poprawnie ustawiony i nie wygasł.

> [!IMPORTANT]
> Token ma wygaśnięcia 10 minut.
> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio i `YOUR_ACCESS_TOKEN` tokenem autoryzacji są zwracane w poprzednim kroku.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Błąd `HTTP 400 Bad Request`

Z tego powodu jest zazwyczaj, że treść żądania zawiera nieprawidłowe dane audio. Aktualnie obsługiwany jest tylko plik WAV.

## <a name="error-http-408-request-timeout"></a>Błąd `HTTP 408 Request Timeout`

Błąd jest prawdopodobnie ponieważ że żadne dane audio są wysyłane do usługi, a usługa zwraca ten błąd, po upłynięciu limitu czasu. Interfejs API REST dane audio należy umieścić w treści żądania.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` w odpowiedzi jest `InitialSilenceTimeout`

Dane audio jest zazwyczaj przyczyną problemu. Na przykład:

- audio ma godzina długa wyciszenia na początku. Usługa zostanie zatrzymana rozpoznawanie po pewnej liczby sekund i zwraca `InitialSilenceTimeout`.
- audio w formacie nieobsługiwanego kodera-dekodera, co sprawia, że dane audio być traktowane jako wyciszenia.
