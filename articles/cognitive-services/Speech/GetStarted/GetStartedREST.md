---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft przy użyciu REST | Dokumentacja firmy Microsoft
description: Umożliwia dostęp do interfejsu API rozpoznawania mowy w kognitywnych usług firmy Microsoft, aby przekonwertować na tekst rozmowy audio REST.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 53785cdfd75c23910802f2be20e6305817b3b097
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347464"
---
# <a name="get-started-with-speech-recognition-by-using-the-rest-api"></a>Wprowadzenie do rozpoznawania mowy przy użyciu interfejsu API REST

Oparte na chmurze mowy usługi aplikacje można tworzyć przy użyciu interfejsu API REST można przekonwertować na tekst rozmowy audio.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie API mowy i uzyskiwanie klucza bezpłatnej subskrypcji próbnej

Interfejs API mowy jest częścią usługi kognitywnych (wcześniej Oxford projektu). Możesz uzyskać klucze bezpłatnej subskrypcji próbnej z [subskrypcji usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API mowy wybierz **uzyskać klucz interfejsu API** uzyskać klucza. Zwraca wartość klucza podstawowego i pomocniczego. Obydwu kluczy są powiązane z tego samego przydziału, dzięki czemu można użyć albo klucza.

> [!IMPORTANT]
>* Pobierz klucz subskrypcji. Aby korzystać z interfejsu API REST, musi mieć [klucza subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
>* Użyj klucza subskrypcji. W poniższych przykładach REST należy zastąpić YOUR_SUBSCRIPTION_KEY klucz subskrypcji. 
>
>* Zapoznaj się [uwierzytelniania](../how-to/how-to-authentication.md) strony dotyczące sposobu uzyskania klucza subskrypcji.

### <a name="prerecorded-audio-file"></a>Plik nagrań audio

W tym przykładzie używamy nagrania audio pliku ilustrujący sposób użycia interfejsu API REST. Zapisz plik dźwiękowy, informujący o tym krótką frazę. Na przykład "Co to jest pogodzie, takich jak dzisiaj?" lub "Odnaleźć zabawne filmów, aby obejrzeć". Rozpoznawanie mowy interfejs API obsługuje również dane wejściowe mikrofonu zewnętrznego.

> [!NOTE]
> Przykład wymaga tego audio jest rejestrowany jako pliku WAV z **PCM pojedynczy kanał (mono), 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Żądanie rozpoznawania kompilacji, a następnie wyślij ją z usługą rozpoznawania mowy

Następnym krokiem rozpoznawania mowy jest wysłanie żądania POST do punktów końcowych HTTP mowy z właściwego żądania nagłówek i treść.

### <a name="service-uri"></a>Identyfikator URI usługi

Usługa rozpoznawania mowy identyfikatora URI jest zdefiniowana na podstawie [tryby rozpoznawania](../concepts.md#recognition-modes) i [języków rozpoznawania](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` Określa tryb rozpoznawania i musi mieć jedną z następujących wartości: `interactive`, `conversation`, lub `dictation`. Jest to ścieżka wymaganego zasobu w identyfikatorze URI. Aby uzyskać więcej informacji, zobacz [tryby rozpoznawania](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` jest to wymagany parametr w ciągu zapytania. Definiuje języka docelowego do konwersji audio: na przykład `en-US` angielski (Stany Zjednoczone). Aby uzyskać więcej informacji, zobacz [języków rozpoznawania](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` jest opcjonalny parametr ciągu zapytania. Dozwolone wartości są `simple` i `detailed`. Domyślnie usługa zwraca wyniki w `simple` format. Aby uzyskać więcej informacji, zobacz [format danych wyjściowych](../concepts.md#output-format).

W poniższej tabeli wymieniono przykładowe identyfikatory URI usługi.

| Tryb rozpoznawania  | Język | Format danych wyjściowych | Identyfikator URI usługi |
|---|---|---|---|
| `interactive` | pt-BR | Domyślne | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | pl-PL | Szczegółowy |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Proste | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> Identyfikator URI usługi jest wymagane tylko wtedy, gdy aplikacja używa interfejsów API REST do wywołania tej usługi rozpoznawania mowy. Jeśli używany jest jeden z [bibliotek klienckich](GetStartedClientLibraries.md), zwykle nie trzeba znać URI, który jest używany. Biblioteki klienta może używać innej usługi identyfikatory URI, które są stosowane tylko dla określonego klienta biblioteki. Aby uzyskać więcej informacji zobacz Biblioteka klienta wybranych przez użytkownika.

### <a name="request-headers"></a>Nagłówki żądań

W nagłówku żądania musi być ustawione następujące pola:

- `Ocp-Apim-Subscription-Key`: Za każdym razem, wywołania tej usługi, należy podać klucz subskrypcji w `Ocp-Apim-Subscription-Key` nagłówka. Usługa rozpoznawania mowy obsługuje również operacji związanych z autoryzacją tokeny zamiast klucze subskrypcji. Aby uzyskać więcej informacji, zobacz [uwierzytelniania](../How-to/how-to-authentication.md).
- `Content-type`: `Content-type` Opisuje format i kodera-dekodera audio strumienia. Obecnie tylko pliku WAV i kodowanie PCM Mono 16000 jest obsługiwane. Wartość Content-type dla tego formatu jest `audio/wav; codec=audio/pcm; samplerate=16000`.

`Transfer-Encoding` Pole jest opcjonalne. Jeśli to pole jest ustawiona `chunked`, dźwięk można dzielenia na małe fragmenty. Aby uzyskać więcej informacji, zobacz [transferu pakietowego](../How-to/how-to-chunked-transfer.md).

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

Poniższy przykład przedstawia sposób wysłania żądania rozpoznawania mowy do punkty końcowe REST mowy. Używa `interactive` trybu rozpoznawania.

> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji.

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

W przykładzie użyto curl w systemie Linux z bash. Jeśli nie jest dostępna na platformie, może być konieczne zainstalowanie curl. Przykład działa także w programów Cygwin na systemu Windows, Git Bash, zsh i innych powłoki.

> [!NOTE]
> Zachowaj `@` przed nazwą pliku audio podczas zamieniania `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio, ponieważ oznacza to, że wartość `--data-binary` jest nazwą pliku, a nie dane.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

## <a name="process-the-speech-recognition-response"></a>Proces rozpoznawania mowy odpowiedzi

Po przetworzeniu żądania, usługa mowy zwraca wyniki w odpowiedzi na formacie JSON.

> [!NOTE]
> Jeśli poprzedni kod zwraca błąd, zobacz [Rozwiązywanie problemów](../troubleshooting.md) zlokalizować możliwe przyczyny.

Poniższy fragment kodu przedstawia przykładowy sposób uzyskać odpowiedzi ze strumienia.

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/Powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

W tym przykładzie curl bezpośrednio zwraca komunikat odpowiedzi w ciągu. Jeśli chcesz wyświetlić w formacie JSON, można użyć dodatkowych narzędzi, na przykład jq.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

- Obsługuje on strumieniem audio tylko do 15 sekund.
- Podczas rozpoznawania nie obsługuje pośrednich wyników. Użytkownicy otrzymują tylko wynik końcowy rozpoznawania.

Aby usunąć te ograniczenia, należy użyć mowy [bibliotek klienckich](GetStartedClientLibraries.md). Lub może współpracować bezpośrednio z [protokół WebSocket mowy](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Co dalej

- Aby zobaczyć sposób użycia interfejsu API REST w języku C#, Java, itp., zobacz te [przykładowe aplikacje](../samples.md).
- Aby zlokalizować i napraw błędy, zobacz [Rozwiązywanie problemów](../troubleshooting.md).
- Aby korzystać z bardziej zaawansowanych funkcji, zobacz jak rozpocząć pracę przy użyciu mowy [bibliotek klienckich](GetStartedClientLibraries.md).

### <a name="license"></a>Licencja

Wszystkie kognitywnych usług zestawy SDK i przykłady, jest udzielana z licencją MIT. Aby uzyskać więcej informacji, zobacz [licencji](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
