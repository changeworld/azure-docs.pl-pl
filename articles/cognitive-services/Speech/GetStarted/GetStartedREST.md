---
title: Wprowadzenie do interfejsu API rozpoznawania mowy Bing za pomocą interfejsu REST | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Dostęp do interfejsu API rozpoznawania mowy w usługach Microsoft Cognitive Services do Konwertuj dźwięk mówiony na tekst przy użyciu architektury REST.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ead4026ecec4878c69bc21a9ebc989eaf3d69a13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515133"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Szybki start: Używanie rozpoznawania mowy Bing interfejsu API REST

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Za pomocą opartej na chmurze usługa rozpoznawania mowy Bing aplikacje można tworzyć przy użyciu interfejsu API REST do Konwertuj dźwięk mówiony na tekst.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie do interfejsu API mowy i Uzyskaj klucz subskrypcji wersji próbnej

Interfejs API mowy jest częścią usług Cognitive Services (wcześniej Project Oxford). Możesz uzyskać bezpłatną subskrypcję próbną kluczy z [subskrypcji usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API rozpoznawania mowy, wybierz opcję **Uzyskaj klucz interfejsu API** można pobrać klucza. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tego samego limitu przydziału, aby można było używać żadnego z nich.

> [!IMPORTANT]
>* Pobierz klucz subskrypcji. Zanim można uzyskać dostęp do interfejsu API REST, konieczne jest posiadanie [klucz subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
>* Użyj klucza subskrypcji. W następujących przykładach REST należy zastąpić YOUR_SUBSCRIPTION_KEY klucz subskrypcji.
>
>* Zapoznaj się [uwierzytelniania](../how-to/how-to-authentication.md) stron dotyczące sposobu uzyskania klucza subskrypcji.

### <a name="prerecorded-audio-file"></a>Plik dźwiękowy nagrań

W tym przykładzie używamy zarejestrowany plik dźwiękowy do ilustrują sposób korzystania z interfejsu API REST. Zapisz plik audio, informujący o tym, krótkich fraz. Załóżmy na przykład, "Co to jest pogody, takich jak dzisiaj?" lub "Znajdź zabawnych filmy, aby obejrzeć". Interfejs API rozpoznawania mowy obsługuje również dane wejściowe mikrofonu zewnętrznego.

> [!NOTE]
> Przykład wymaga tego audio jest rejestrowany jako plik WAV o **PCM pojedynczy kanał (mono) 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Żądanie rozpoznawania kompilacji oraz wysyłać je do usługi rozpoznawania mowy

Następnym krokiem rozpoznawania mowy jest wysłanie żądania POST do punktów końcowych HTTP mowy z żądania prawidłowego nagłówka i treści.

### <a name="service-uri"></a>Identyfikator URI usługi

Usługę rozpoznawania mowy, identyfikator URI jest zdefiniowana na podstawie [tryby rozpoznawania](../concepts.md#recognition-modes) i [języków rozpoznawania](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` Określa tryb rozpoznawanie i musi mieć jedną z następujących wartości: `interactive`, `conversation`, lub `dictation`. Jest to ścieżka wymaganego zasobu w identyfikatorze URI. Aby uzyskać więcej informacji, zobacz [tryby rozpoznawania](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` jest wymaganym parametrem ciągu zapytania. Definiuje język docelowy do konwersji audio: na przykład `en-US` dla języka angielskiego (Stany Zjednoczone). Aby uzyskać więcej informacji, zobacz [języków rozpoznawania](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` to opcjonalny parametr ciągu zapytania. Jego dozwolone wartości to `simple` i `detailed`. Domyślnie usługa zwraca wyniki w `simple` formatu. Aby uzyskać więcej informacji, zobacz [format danych wyjściowych](../concepts.md#output-format).

Przykłady usługi identyfikatory URI są wymienione w poniższej tabeli.

| Tryb rozpoznawania  | Język | Format wyjściowy | Identyfikator URI usługi |
|---|---|---|---|
| `interactive` | pt-BR | Domyślne | https:\//speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | Szczegółowy | https:\//speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Proste | https:\//speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> Identyfikator URI usługi jest wymagane tylko wtedy, gdy aplikacja używa interfejsów API REST, aby wywołać usługę rozpoznawania mowy. Jeśli używasz jednego z [biblioteki klienckie](GetStartedClientLibraries.md), zwykle nie trzeba wiedzieć, których identyfikator URI jest używany. Biblioteki klienckie może używać innej usługi identyfikatory URI, które mają zastosowanie tylko w przypadku biblioteki klienta właściwy. Aby uzyskać więcej informacji zobacz Biblioteka klienta wybranych przez użytkownika.

### <a name="request-headers"></a>Nagłówki żądań

Następujące pola musi być ustawiona w nagłówku żądania:

- `Ocp-Apim-Subscription-Key`: Za każdym razem, należy wywołać usługę, należy przekazać swój klucz subskrypcji w `Ocp-Apim-Subscription-Key` nagłówka. Usługa rozpoznawania mowy obsługuje również operacji związanych z autoryzacją tokeny zamiast kluczy subskrypcji. Aby uzyskać więcej informacji, zobacz [Authentication](../How-to/how-to-authentication.md) (Uwierzytelnianie).
- `Content-type`: `Content-type` Opisuje format i kodera-dekodera audio strumienia. Obecnie tylko plik WAV i PCM Mono 16000 kodowanie jest obsługiwane. Wartość Content-type ten format jest `audio/wav; codec=audio/pcm; samplerate=16000`.

Pole `Transfer-Encoding` jest opcjonalne. Jeśli to pole jest ustawiona na `chunked`, audio mogą być skalowane na małe fragmenty. Aby uzyskać więcej informacji, zobacz [transferu pakietowego](../How-to/how-to-chunked-transfer.md).

Poniżej przedstawiono przykładowy nagłówek żądania:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Wyślij żądanie do usługi

Poniższy przykład pokazuje, jak wysyłać żądań rozpoznawania mowy punkty końcowe REST mowy. Używa ona `interactive` trybu rozpoznawania.

> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

W przykładzie użyto programu curl w systemie Linux przy użyciu programu bash. Jeśli nie jest dostępny na Twojej platformie, może być konieczne do zainstalowania programu curl. Przykład działa również na Cygwin na Windows, systemu Git Bash, zsh i innych powłoki.

> [!NOTE]
> Zachowaj `@` przed nazwą pliku audio podczas zastępowania `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio, ponieważ oznacza to, że wartość `--data-binary` jest nazwą pliku, a nie dane.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

---

## <a name="process-the-speech-recognition-response"></a>Przetworzenie odpowiedzi rozpoznawania mowy

Po przetworzeniu żądania, usługa rozpoznawania mowy zwraca wyniki do odpowiedzi w formacie JSON.

> [!NOTE]
> Jeśli poprzedni kod zwraca błąd, zobacz [Rozwiązywanie problemów](../troubleshooting.md) do lokalizowania możliwa przyczyna.

Poniższy fragment kodu przedstawia przykład jak odpowiedzi może być odczytany ze strumienia.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

W tym przykładzie curl bezpośrednio zwraca komunikat odpowiedzi w ciągu. Jeśli chcesz było wyświetlane w formacie JSON, można użyć dodatkowych narzędzi, na przykład jq.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

Poniższy przykład jest odpowiedź w formacie JSON:

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Ograniczenia

Interfejs API REST ma następujące ograniczenia:

- Strumień audio obsługuje tylko do 15 sekund.
- Nie obsługuje on wyniki pośrednie podczas rozpoznawania. Użytkownicy będą otrzymywać tylko wynik końcowy rozpoznawania.

Aby usunąć te ograniczenia, użyj mowy [biblioteki klienckie](GetStartedClientLibraries.md). Lub może współpracować bezpośrednio z [protokołu WebSocket mowy](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Co dalej

- Aby zobaczyć, jak używać interfejsu API REST w języku C#, Java, itp., zobacz te [przykładowe aplikacje](../samples.md).
- Aby znaleźć i naprawić błędy, zobacz [Rozwiązywanie problemów](../troubleshooting.md).
- Aby korzystać z bardziej zaawansowanych funkcji, zobacz, jak rozpocząć pracę przy użyciu funkcji rozpoznawania mowy [biblioteki klienckie](GetStartedClientLibraries.md).

### <a name="license"></a>Licencja

Wszystkie Cognitive Services zestawy SDK i przykłady są licencjonowane z licencją MIT. Aby uzyskać więcej informacji, zobacz [licencji](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
