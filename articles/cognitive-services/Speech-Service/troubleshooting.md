---
title: Rozwiązywanie problemów z sdk mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z SDK mowy.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815417"
---
# <a name="troubleshoot-the-speech-sdk"></a>Rozwiązywanie problemów z zestawem Speech SDK

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z SDK mowy.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Błąd: uaktualnienie websocket nie powiodło się z błędem uwierzytelniania (403)

Może mieć niewłaściwy punkt końcowy dla regionu lub usługi. Sprawdź identyfikator URI, aby upewnić się, że jest poprawny.

Ponadto może wystąpić problem z kluczem subskrypcji lub tokenem autoryzacji. Aby uzyskać więcej informacji, zobacz następną sekcję.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Błąd: HTTP 403 Zabronione lub HTTP 401 Nieautoryzowane

Ten błąd często jest spowodowany przez problemy z uwierzytelnianiem. Żądania połączenia bez `Ocp-Apim-Subscription-Key` `Authorization` prawidłowego lub nagłówka są odrzucane o stanie 403 lub 401.

* Jeśli używasz klucza subskrypcji do uwierzytelniania, może zostać wyświetlony błąd, ponieważ:

    - Brakuje klucza subskrypcji lub brakuje go
    - Przekroczono limit użycia subskrypcji

* Jeśli używasz tokenu autoryzacji do uwierzytelniania, może zostać wyświetlony błąd, ponieważ:

    - Token autoryzacji jest nieprawidłowy
    - Token autoryzacji wygasł

### <a name="validate-your-subscription-key"></a>Sprawdzanie poprawności klucza subskrypcji

Możesz sprawdzić, czy masz prawidłowy klucz subskrypcji, uruchamiając jedno z następujących poleceń.

> [!NOTE]
> Zamień `YOUR_SUBSCRIPTION_KEY` i `YOUR_REGION` z własnym kluczem subskrypcji i skojarzonym regionem.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Jeśli wprowadzono prawidłowy klucz subskrypcji, polecenie zwraca token autoryzacji, w przeciwnym razie zwracany jest błąd.

### <a name="validate-an-authorization-token"></a>Sprawdzanie poprawności tokenu autoryzacji

Jeśli używasz tokenu autoryzacji do uwierzytelniania, uruchom jedno z następujących poleceń, aby sprawdzić, czy token autoryzacji jest nadal prawidłowy. Tokeny są ważne przez 10 minut.

> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ścieżką do wstępnie zarejestrowanego pliku audio. Zamień `YOUR_ACCESS_TOKEN` token autoryzacji zwrócony w poprzednim kroku. Wymień `YOUR_REGION` na odpowiedni region.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Jeśli wprowadzono prawidłowy token autoryzacji, polecenie zwraca transkrypcję pliku audio, w przeciwnym razie zwracany jest błąd.

---

## <a name="error-http-400-bad-request"></a>Błąd: HTTP 400 Złe żądanie

Ten błąd zwykle występuje, gdy treść żądania zawiera nieprawidłowe dane audio. Obsługiwany jest tylko format WAV. Ponadto sprawdź nagłówki żądania, aby upewnić się, `Content-Type` że `Content-Length`określono odpowiednie wartości dla i .

## <a name="error-http-408-request-timeout"></a>Błąd: Limit czasu żądania HTTP 408

Błąd najprawdopodobniej występuje, ponieważ żadne dane audio nie są wysyłane do usługi. Ten błąd może być również spowodowany problemami z siecią.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" w odpowiedzi jest "InitialSilenceTimeout"

Ten problem jest zwykle spowodowany przez dane audio. Ten błąd może zostać wyświetlony, ponieważ:

* Na początku dźwięku panuje długa cisza. W takim przypadku usługa zatrzymuje rozpoznawanie po `InitialSilenceTimeout`kilku sekundach i zwraca .

* Dźwięk używa nieobsługitowanego formatu kodeka, co powoduje, że dane audio są traktowane jako cisza.

## <a name="next-steps"></a>Następne kroki

* [Przejrzyj informacje o wersji](releasenotes.md)
