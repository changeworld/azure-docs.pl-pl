---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania rozpoznawanie mowy Bing przy użyciu usługi REST | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Użyj usługi REST, aby uzyskać dostęp do interfejsu API rozpoznawania mowy w Microsoft Cognitive Services, aby przekonwertować dźwięk mówiony na tekst.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e962a12c6c27737f95e78e80036e51bac41147d5
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965783"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Szybki start: Korzystanie z interfejsu API REST rozpoznawania rozpoznawanie mowy Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Korzystając z usługi rozpoznawanie mowy Bing opartej na chmurze, można opracowywać aplikacje za pomocą interfejsu API REST, aby przekonwertować dźwięk mówiony na tekst.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Zasubskrybuj Speech API i uzyskaj bezpłatny klucz subskrypcji wersji próbnej

Speech API jest częścią Cognitive Services (wcześniej Project Oxford). Klucze subskrypcji bezpłatnej wersji próbnej możesz uzyskać ze strony [subskrypcji Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) . Po wybraniu Speech API wybierz pozycję **Pobierz klucz interfejsu API** , aby uzyskać klucz. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tym samym limitem przydziału, więc można użyć obu kluczy.

> [!IMPORTANT]
>* Pobierz klucz subskrypcji. Aby można było uzyskać dostęp do interfejsu API REST, należy dysponować [kluczem subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
>* Użyj klucza subskrypcji. W poniższych przykładach REST Zastąp YOUR_SUBSCRIPTION_KEY własnym kluczem subskrypcji.
>
>* Zapoznaj się z tematem jak uzyskać klucz subskrypcji, na stronie [uwierzytelnianie](../how-to/how-to-authentication.md) .

### <a name="prerecorded-audio-file"></a>Zarejestrowano plik audio

W tym przykładzie używamy zapisanego pliku audio do zilustrowania sposobu korzystania z interfejsu API REST. Nagrywaj plik dźwiękowy samodzielnie mówiący o krótkiej frazie. Na przykład powiedzmy "co to jest pogoda, jak dzisiaj?" lub "Znajdź zabawne filmy do obejrzenia". Interfejs API rozpoznawania mowy obsługuje również wejście zewnętrznego mikrofonu.

> [!NOTE]
> Przykład wymaga, aby dźwięk był rejestrowany jako plik WAV z **pojedynczym kanałem PCM (mono), 16 kHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Utwórz żądanie rozpoznawania i wyślij je do usługi rozpoznawania mowy

Następnym krokiem rozpoznawania mowy jest wysłanie żądania POST do punktów końcowych HTTP mowy przy użyciu odpowiedniego nagłówka i treści żądania.

### <a name="service-uri"></a>Identyfikator URI usługi

Identyfikator URI usługi rozpoznawania mowy jest definiowany na podstawie [trybów rozpoznawania](../concepts.md#recognition-modes) i [języków rozpoznawania](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>`Określa tryb rozpoznawania i musi mieć jedną z następujących wartości: `interactive`, `conversation`, lub `dictation`. Jest to wymagana ścieżka zasobu w identyfikatorze URI. Aby uzyskać więcej informacji, zobacz [tryby rozpoznawania](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>`jest wymaganym parametrem w ciągu zapytania. Definiuje język docelowy konwersji audio: na przykład `en-US` w przypadku języka angielskiego (Stany Zjednoczone). Aby uzyskać więcej informacji, zobacz [Języki rozpoznawania](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>`jest opcjonalnym parametrem w ciągu zapytania. Dozwolone wartości to `simple` i `detailed`. Domyślnie usługa zwraca wyniki w `simple` formacie. Aby uzyskać więcej informacji, zobacz [Format danych wyjściowych](../concepts.md#output-format).

Niektóre przykłady identyfikatorów URI usługi są wymienione w poniższej tabeli.

| Tryb rozpoznawania  | Język | Format wyjściowy | Identyfikator URI usługi |
|---|---|---|---|
| `interactive` | pt-BR | Domyślny | https:\//Speech.platform.Bing.com/Speech/Recognition/Interactive/cognitiveservices/V1?Language=pt-br |
| `conversation` | en-US | Szczegółowy | https:\//speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Proste | https:\//Speech.platform.Bing.com/Speech/Recognition/Dictation/cognitiveservices/V1?language=FR-fr&format=Simple |

> [!NOTE]
> Identyfikator URI usługi jest wymagany tylko wtedy, gdy aplikacja używa interfejsów API REST do wywoływania usługi rozpoznawania mowy. Jeśli używasz jednej z [bibliotek klienta](GetStartedClientLibraries.md), zazwyczaj nie musisz wiedzieć, który identyfikator URI jest używany. Biblioteki klienckie mogą używać różnych identyfikatorów URI usługi, które mają zastosowanie tylko do określonej biblioteki klienta. Aby uzyskać więcej informacji, zapoznaj się z dowolnie wybraną biblioteką klienta.

### <a name="request-headers"></a>Nagłówki żądań

W nagłówku żądania muszą być ustawione następujące pola:

- `Ocp-Apim-Subscription-Key`: Za każdym razem, gdy wywoływana jest usługa, należy przekazać klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka. Usługa Speech obsługuje również przekazywanie tokenów autoryzacji zamiast kluczy subskrypcji. Aby uzyskać więcej informacji, zobacz [Authentication](../How-to/how-to-authentication.md) (Uwierzytelnianie).
- `Content-type`: W `Content-type` polu opisano format i dekodery strumienia audio. Obecnie obsługiwane jest tylko kodowanie plików WAV i PCM mono 16000. Wartość typu zawartości dla tego formatu to `audio/wav; codec=audio/pcm; samplerate=16000`.

Pole `Transfer-Encoding` jest opcjonalne. Jeśli ustawisz to pole na `chunked`, możesz Chop dźwięk do małych fragmentów. Aby uzyskać więcej informacji, zobacz temat [przenoszenie fragmentaryczne](../How-to/how-to-chunked-transfer.md).

Poniżej znajduje się przykładowy nagłówek żądania:

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

Poniższy przykład pokazuje, jak wysłać żądanie rozpoznawania mowy do punktów końcowych REST mowy. Używa `interactive` trybu rozpoznawania.

> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio. Zamień `YOUR_SUBSCRIPTION_KEY` na własny klucz subskrypcji.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="curltabcurl"></a>[odsłon](#tab/curl)

W przykładzie zastosowano zwinięcie w systemie Linux z bash. Jeśli nie jest on dostępny na twojej platformie, może być konieczne zainstalowanie zawieszania. Przykład działa również na Cygwin w systemie Windows, Git bash, zsh i innych powłokach.

> [!NOTE]
> Należy zachować nazwę pliku audio `YOUR_AUDIO_FILE` `--data-binary` przed zastąpieniem ścieżką do zarejestrowanego pliku audio, ponieważ oznacza to, że wartość jest nazwą pliku zamiast danymi. `@`

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

## <a name="process-the-speech-recognition-response"></a>Przetwarzanie odpowiedzi rozpoznawania mowy

Po przetworzeniu żądania usługa mowy zwraca wyniki w odpowiedzi w formacie JSON.

> [!NOTE]
> Jeśli poprzedni kod zwróci błąd, zobacz [Rozwiązywanie problemów](../troubleshooting.md) , aby zlokalizować możliwą przyczynę.

Poniższy fragment kodu przedstawia przykład sposobu odczytywania odpowiedzi ze strumienia.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[odsłon](#tab/curl)

W tym przykładzie zwinięcie bezpośrednio zwraca komunikat odpowiedzi w ciągu. Jeśli chcesz wyświetlić go w formacie JSON, możesz użyć dodatkowych narzędzi, na przykład JQ.

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

Poniższy przykład jest odpowiedzią JSON:

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

Interfejs API REST ma pewne ograniczenia:

- Obsługuje strumień audio tylko do 15 sekund.
- Nie obsługuje pośrednich wyników podczas rozpoznawania. Użytkownicy otrzymują tylko końcowy wynik rozpoznawania.

Aby usunąć te ograniczenia, użyj [bibliotek klienckich](GetStartedClientLibraries.md)mowy. Można też bezpośrednio korzystać z [protokołu WebSocket mowy](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Co dalej

- Aby dowiedzieć się, jak korzystać z interfejsu C#API REST w programie Java itp., zobacz te [przykładowe aplikacje](../samples.md).
- Aby zlokalizować i naprawić błędy, zobacz [Rozwiązywanie problemów](../troubleshooting.md).
- Aby skorzystać z bardziej zaawansowanych funkcji, zobacz jak rozpocząć pracę przy użyciu [bibliotek klienckich](GetStartedClientLibraries.md)mowy.

### <a name="license"></a>Licencja

Wszystkie Cognitive Services zestawy SDK i przykłady są licencjonowane z licencją MIT. Aby uzyskać więcej informacji, zobacz [licencja](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
