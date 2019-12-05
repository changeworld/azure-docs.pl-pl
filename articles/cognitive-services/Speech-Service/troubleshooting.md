---
title: Rozwiązywanie problemów z zestawem Speech SDK — Speech Service
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z zestawu Speech SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815417"
---
# <a name="troubleshoot-the-speech-sdk"></a>Rozwiązywanie problemów z zestawem Speech SDK

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z zestawu Speech SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Błąd: Uaktualnienie WebSocket nie powiodło się z powodu błędu uwierzytelniania (403)

Konieczne może być nieprawidłowy punkt końcowy dla Twojego regionu lub usługi. Sprawdź identyfikator URI, aby upewnić się, że jest on poprawny.

Ponadto może być problem z Twoim klucz subskrypcji lub autoryzacji tokenu. Aby uzyskać więcej informacji zobacz następną sekcję.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Błąd: HTTP 403 Dostęp zabroniony lub HTTP 401 Brak autoryzacji

Ten błąd jest często spowodowane przez problemy z uwierzytelnianiem. Żądania połączenia bez prawidłowego `Ocp-Apim-Subscription-Key` lub `Authorization` nagłówka zostały odrzucone ze stanem 403 lub 401.

* Klucz subskrypcji używany do uwierzytelniania, może zostać wyświetlony błąd, ponieważ:

    - klucz subskrypcji lub jest nieprawidłowy
    - Przekroczono limit przydziału użycia Twojej subskrypcji

* Token autoryzacji używany do uwierzytelniania, może zostać wyświetlony błąd, ponieważ:

    - token autoryzacji jest nieprawidłowy
    - wygasł token autoryzacji

### <a name="validate-your-subscription-key"></a>Zweryfikuj swój klucz subskrypcji

Aby sprawdzić, mają klucz ważnej subskrypcji, uruchamiając jeden z następujących poleceń.

> [!NOTE]
> Zastąp `YOUR_SUBSCRIPTION_KEY` i `YOUR_REGION` własny klucz subskrypcji i regionu skojarzonego.

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

Jeśli wprowadzono prawidłowy klucz subskrypcji, polecenie zwróci Token autoryzacji, w przeciwnym razie zwracany jest błąd.

### <a name="validate-an-authorization-token"></a>Zweryfikuj token autoryzacji

Jeśli token autoryzacji jest używany do uwierzytelniania, uruchom jedno z poniższych poleceń, aby sprawdzić, czy token autoryzacji jest nadal ważny. Tokeny są ważne przez 10 minut.

> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio. Zastąp `YOUR_ACCESS_TOKEN` tokenem autoryzacji są zwracane w poprzednim kroku. Zastąp `YOUR_REGION` z poprawny region.

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

Jeśli wprowadzono prawidłowy token autoryzacji, polecenie zwróci transkrypcję pliku audio, w przeciwnym razie zwracany jest błąd.

---

## <a name="error-http-400-bad-request"></a>Błąd: HTTP 400 Niewłaściwe żądanie

Ten błąd występuje zazwyczaj, gdy treść żądania zawiera nieprawidłowe dane audio. Jest obsługiwany tylko format WAV. Sprawdź, czy nagłówki żądania, aby upewnić się, można określić odpowiednie wartości dla `Content-Type` i `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Błąd: Limit czasu z 408 żądania HTTP

Najbardziej prawdopodobną błąd występuje, ponieważ nie dane audio są wysyłane do usługi. Ten błąd może być także spowodowany przez problemy z siecią.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" w odpowiedzi jest "InitialSilenceTimeout"

Ten problem jest zazwyczaj spowodowane danych audio. Może zostać wyświetlony ten błąd, ponieważ:

* Istnieje długa odcinek wyciszenia na początku audio. W takiej sytuacji usługa zatrzymuje uznanie za kilka sekund i zwraca `InitialSilenceTimeout`.

* Audio w formacie nieobsługiwanego kodera-dekodera, co powoduje, że dane audio powinien być traktowany jako wyciszenia.

## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z informacjami o wersji](releasenotes.md)
