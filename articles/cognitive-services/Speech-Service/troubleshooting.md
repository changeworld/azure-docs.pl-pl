---
title: Mowy usług cognitive Services SDK rozwiązywania problemów
description: Problemy z rozwiązywania problemów Cognitive Services — zestaw SDK rozpoznawania mowy
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: ff8aba562cfd2d6d54c708ee7fdc4c6ca7185f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284126"
---
# <a name="troubleshooting-speech-services-sdk"></a>Rozwiązywanie problemów z usług przetwarzania mowy zestawu SDK

Ten artykuł zawiera informacje ułatwiające rozwiązanie problemów, które można napotkać podczas korzystania z zestawu SDK rozpoznawania mowy.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Błąd `WebSocket Upgrade failed with an authentication error (403).`

Może być nieprawidłowy punkt końcowy dla Twojego regionu lub usługi. Dokładnie Sprawdź identyfikator URI, aby upewnić się, że jest on poprawny. Również, jak również może to być problem z Twoim klucz subskrypcji lub autoryzacji, zobacz następną sekcję, tokenu.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Błąd `HTTP 403 Forbidden` lub błąd `HTTP 401 Unauthorized`

Ten błąd jest często spowodowane przez problemy z uwierzytelnianiem. Żądania połączenia bez prawidłowego `Ocp-Apim-Subscription-Key` lub `Authorization` nagłówka są odrzucane stanu 401 lub 403.

* Jeśli używasz klucz subskrypcji dla uwierzytelniania, może to być spowodowane:

    - klucz subskrypcji lub jest nieprawidłowy
    - Przekroczono limit przydziału użycia Twojej subskrypcji

* Jeśli używasz token autoryzacji do uwierzytelniania, może to być spowodowane:

    - token autoryzacji jest nieprawidłowy
    - wygasł token autoryzacji

### <a name="validate-your-subscription-key"></a>Zweryfikuj swój klucz subskrypcji

Możesz sprawdzić, aby upewnić się, że masz klucz ważnej subskrypcji, uruchamiając jeden z poniższych poleceń.

> [!NOTE]
> Zastąp `YOUR_SUBSCRIPTION_KEY` i `YOUR_REGION` przy użyciu własnych klucz subskrypcji i regionu skojarzonego odpowiednio.

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

### <a name="validate-an-authorization-token"></a>Zweryfikuj token autoryzacji

Jeśli token autoryzacji jest używany do uwierzytelniania, uruchom jedno z poniższych poleceń, aby sprawdzić, czy token autoryzacji jest nadal ważny. Tokeny są ważne przez 10 minut.

> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ze ścieżką do pliku audio nagrań `YOUR_ACCESS_TOKEN` tokenem autoryzacji, zwracane w poprzednim kroku i `YOUR_REGION` z poprawny region.

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
    
    # Read audio into byte array
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")
    
    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes
    
    # Show the result
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

---

## <a name="error-http-400-bad-request"></a>Błąd `HTTP 400 Bad Request`

Ten błąd występuje zazwyczaj, gdy treść żądania zawiera nieprawidłowe dane audio. Tylko `WAV` format jest obsługiwany. Również zapoznać się z nagłówków żądań, aby upewnić się, określasz odpowiednią `Content-Type` i `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Błąd `HTTP 408 Request Timeout`

Błąd jest najbardziej prawdopodobne, ponieważ nie dane audio są wysyłane do usługi. Ten błąd może być także spowodowane przez problemy z siecią.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` w odpowiedzi jest `InitialSilenceTimeout`

Dane audio jest zazwyczaj przyczyną problemu. Na przykład:

* Istnieje długa odcinek wyciszenia na początku audio. Usługa zostanie zatrzymanie rozpoznawanie po kilku sekundach i powrót `InitialSilenceTimeout`.
* Audio w formacie nieobsługiwanego kodera-dekodera, co powoduje, że dane audio powinien być traktowany jako wyciszenia.

## <a name="next-steps"></a>Kolejne kroki

* [Informacje o wersji](releasenotes.md)

