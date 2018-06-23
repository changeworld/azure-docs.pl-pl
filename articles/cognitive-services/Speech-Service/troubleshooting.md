---
title: Mowy kognitywnych usługi SDK rozwiązywania problemów | Dokumentacja firmy Microsoft
description: Problemy z premia kognitywnych usług mowy zestawu SDK
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 16eaebcf9494ab57521068a9418ccf2ac7f5a8fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348984"
---
# <a name="troubleshooting-speech-services-sdk"></a>Rozwiązywanie problemów z usługami mowy zestawu SDK

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które można napotkać, korzystając z zestawu SDK mowy.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Błąd `WebSocket Upgrade failed with an authentication error (403).`

Może być nieprawidłowy punkt końcowy regionu lub usługi. Sprawdź identyfikator URI do upewnij się, że jest poprawny. Również w następnej sekcji, ponieważ może to być również problem ze klucza subskrypcji lub autoryzacji tokenu.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Błąd `HTTP 403 Forbidden` lub błędu `HTTP 401 Unauthorized`

Ten błąd jest często spowodowane przez problemy z uwierzytelnianiem. Żądania połączenia nie jest prawidłowym `Ocp-Apim-Subscription-Key` lub `Authorization` nagłówka są odrzucane stanu 401 lub 403.

* Jeśli używasz klucza subskrypcji do uwierzytelniania, może to być spowodowane:

    - klucz subskrypcji jest nieprawidłowy lub niedostępny
    - Przekroczono przydział użycia Twojej subskrypcji

* Jeśli token autoryzacji jest używana do uwierzytelniania, może to być spowodowane:

    - token autoryzacji jest nieprawidłowy
    - token autoryzacji wygasł

### <a name="validate-your-subscription-key"></a>Sprawdzanie poprawności klucza Twojej subskrypcji

Możesz sprawdzić, aby upewnić się, że masz klucz ważnej subskrypcji, uruchamiając jedno z poniższych poleceń.

> [!NOTE]
> Zastąp `YOUR_SUBSCRIPTION_KEY` i `YOUR_REGION` za pomocą własnego klucza subskrypcji i regionu skojarzonego odpowiednio.

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

### <a name="validate-an-authorization-token"></a>Sprawdzanie poprawności tokenu autoryzacji

Jeśli token autoryzacji używany do uwierzytelniania, uruchom jedno z poniższych poleceń, aby sprawdzić, czy token autoryzacji jest nadal ważny. Tokeny są ważne przez 10 minut.

> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio `YOUR_ACCESS_TOKEN` z tokenem autoryzacji zwrócony w poprzednim kroku i `YOUR_REGION` z poprawny region.

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

Ten błąd występuje zazwyczaj, gdy treść żądania zawiera nieprawidłowe dane audio. Tylko `WAV` format jest obsługiwany. Sprawdź również nagłówków żądania do upewnij się, że określono właściwe `Content-Type` i `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Błąd `HTTP 408 Request Timeout`

Błąd jest najprawdopodobniej nie audio dane są wysyłane do usługi. Ten błąd może być również spowodowane przez problemy z siecią.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` w odpowiedzi jest `InitialSilenceTimeout`

Dane audio jest zwykle Przyczyna przyczyną problemu. Na przykład:

* Istnieje długa odcinek wyciszenia na początku audio. Usługa spowoduje zatrzymanie uznania za kilka sekund i zwracać `InitialSilenceTimeout`.
* Dźwięk używa formatu nieobsługiwanego kodera-dekodera, co powoduje, że dane audio powinien być traktowany jako wyciszenia.

## <a name="next-steps"></a>Kolejne kroki

* [Informacje o wersji](releasenotes.md)

