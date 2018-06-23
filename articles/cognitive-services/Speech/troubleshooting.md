---
title: Rozwiązywanie problemów z | Dokumentacja firmy Microsoft
description: Jak rozwiązać problemy za pomocą usługi mowy firmy Microsoft.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 04f3da19939d523d201d357b2b0293db1508431d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347408"
---
# <a name="troubleshooting"></a>Rozwiązywanie problemów

## <a name="error-http-403-forbidden"></a>Błąd `HTTP 403 Forbidden`

Podczas korzystania z funkcji rozpoznawania mowy interfejsu API, zwraca `HTTP 403 Forbidden` błędu.

### <a name="cause"></a>Przyczyna

Ten błąd jest często spowodowane przez problemy z uwierzytelnianiem. Żądania połączeń bez prawidłową `Ocp-Apim-Subscription-Key` lub `Authorization` nagłówka są odrzucane przez usługę z `HTTP 403 Forbidden` odpowiedzi.

Jeśli używasz klucza subskrypcji do uwierzytelniania, przyczyną może być

- klucz subskrypcji jest nieprawidłowy lub niedostępny
- Przekroczono przydział użycia klucza subskrypcji
- `Ocp-Apim-Subscription-Key` pola nie jest określona w nagłówku żądania podczas wywołania interfejsu API REST

Jeśli token autoryzacji jest używana do uwierzytelniania, że może spowodować błąd.

- `Authorization` Brak nagłówka w żądaniu przy użyciu REST
- określony w nagłówku autoryzacji token autoryzacji jest nieprawidłowy
- token autoryzacji wygasł. Token dostępu ma upływie 10 minut

Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [uwierzytelniania](How-to/how-to-authentication.md) strony.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

#### <a name="verify-that-your-subscription-key-is-valid"></a>Sprawdź, czy klucz subskrypcji jest nieprawidłowy

Można uruchomić następujące polecenie w celu weryfikacji. Należy pamiętać, aby zastąpić *YOUR_SUBSCRIPTION_KEY* z kluczem subskrypcji. Jeśli klucz subskrypcji jest prawidłowy, zostanie wyświetlony w odpowiedzi tokenu autoryzacji jako JSON Web Token (JWT). W przeciwnym razie wystąpi błąd w odpowiedzi.

> [!NOTE]
> Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

W przykładzie użyto curl w systemie Linux z bash. Jeśli nie jest dostępna na platformie, może być konieczne Zainstaluj narzędzie curl. Przykład powinny działać również w programów Cygwin na systemu Windows, Git Bash, zsh i innych powłoki.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Upewnij się, że używasz tego samego klucza subskrypcji w aplikacji lub w żądaniu REST jak używany powyżej.

#### <a name="verify-the-authorization-token"></a>Sprawdź token autoryzacji

Ten krok jest potrzebne tylko wtedy, jeśli token autoryzacji jest używany do uwierzytelniania. Uruchom następujące polecenie, aby sprawdzić, czy token autoryzacji jest nadal ważny. Polecenie wysyła żądanie POST do usługi i oczekuje na komunikat odpowiedzi z usługi. Jeśli nadal będziesz mieć HTTP `403 Forbidden` błąd, Sprawdź dostęp token jest poprawnie ustawiona i nie wygasł.

> [!IMPORTANT]
> Token ma upływie 10 minut.
> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio i `YOUR_ACCESS_TOKEN` tokenem autoryzacji zwrócony w poprzednim kroku.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Błąd `HTTP 400 Bad Request`

Z tego powodu jest zwykle, że treść żądania zawiera nieprawidłowe dane audio. Aktualnie obsługiwany jest tylko plik WAV.

## <a name="error-http-408-request-timeout"></a>Błąd `HTTP 408 Request Timeout`

Błąd jest prawdopodobnie ponieważ żadne dane nie audio jest wysyłane do usługi, a usługa zwraca błąd po limitu czasu. Dla interfejsu API REST danych należy umieścić w treści żądania.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` w odpowiedzi jest `InitialSilenceTimeout`

Dane audio jest zwykle Przyczyna przyczyną problemu. Na przykład:

- dźwięk ma wyciszenia długi czas na początku. Usługa zostanie zatrzymana uznania po liczby sekund i zwraca `InitialSilenceTimeout`.
- dźwięk używa formatu nieobsługiwanego kodera-dekodera, co sprawia, że dane audio traktowane jako wyciszenia.
