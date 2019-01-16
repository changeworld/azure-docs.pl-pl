---
title: Speech Services — interfejsy API REST — usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsów API REST mowy na tekst i zamiany tekstu na mowę. W tym artykule dowiesz się o opcjach autoryzacji, opcje zapytania, jak struktury żądania i odpowiedzi.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b7f5d4683f0042b95399b86cd4f53c93518c3c56
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330679"
---
# <a name="speech-service-rest-apis"></a>Interfejsy API REST usługi mowy

Jako alternatywę dla [zestaw SDK rozpoznawania mowy](speech-sdk.md), usługa mowy umożliwia konwertowanie mowy na tekst i zamiany tekstu na mowę przy użyciu zestawu interfejsów API REST. Każdy punkt końcowy, dostępny jest skojarzone z regionem. Aplikacja wymaga klucza subskrypcji dla punktu końcowego, który ma być używany.

Przed rozpoczęciem korzystania z interfejsów API REST, należy zrozumieć:
* Żądania zamiany mowy na tekst, przy użyciu interfejsu API REST może zawierać tylko 10 sekund nagrania audio.
* Interfejs API REST mowy na tekst zwraca tylko wyniki końcowe. Wyniki częściowe nie są dostarczane.
* Zamiany tekstu na mowę interfejsu API REST wymaga nagłówka autoryzacji. Oznacza to, trzeba wykonać wymiany tokenu dostępu do usługi. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie).

## <a name="authentication"></a>Authentication

Każde żądanie albo mowy na tekst i zamiany tekstu na mowę interfejsu API REST wymaga nagłówka autoryzacji. W poniższej tabeli zestawiono, które nagłówki są obsługiwane dla każdej usługi:

| Nagłówki obsługiwane autoryzacji | Zamiany mowy na tekst | Zamiana tekstu na mowę |
|------------------------|----------------|----------------|
| OCP-Apim-Subscription-Key | Yes | Nie |
| Autoryzacja: Elementu nośnego | Yes | Yes |

Korzystając z `Ocp-Apim-Subscription-Key` nagłówka, tylko, musisz podać klucz subskrypcji. Na przykład:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Korzystając z `Authorization: Bearer` nagłówka jest wymagana, aby zgłosić wniosek o `issueToken` punktu końcowego. W tym żądaniu możesz wymienić klucz subskrypcji dla tokenu dostępu, który jest ważny przez 10 minut. W kolejnych sekcjach dowiesz się, jak uzyskać token, używają tokenu i token odświeżania.

### <a name="how-to-get-an-access-token"></a>Jak uzyskać token dostępu

Aby uzyskać token dostępu, musisz zgłosić wniosek o `issueToken` punktu końcowego za pomocą `Ocp-Apim-Subscription-Key` i klucz subskrypcji.

Obsługiwane są następujące regiony i punktów końcowych:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Aby utworzyć żądanie tokenu dostępu za pomocą tych przykładów.

#### <a name="http-sample"></a>Przykładowe HTTP

W tym przykładzie jest proste żądania HTTP w celu pobrania tokenu. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji usługa rozpoznawania mowy. Jeśli Twoja subskrypcja nie znajduje się w regionie zachodnie stany USA, należy zastąpić `Host` nagłówek o nazwie hosta w Twoim regionie.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Treść odpowiedzi zawiera token dostępu w formacie Java tokenu sieci Web (JWT).

#### <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell

W tym przykładzie jest to prosty skrypt programu PowerShell w celu uzyskania tokenu dostępu. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji usługa rozpoznawania mowy. Upewnij się, że używasz właściwego punktu końcowego dla regionu, który pasuje do Twojej subskrypcji. W tym przykładzie jest aktualnie skonfigurowana do regionu zachodnie stany USA.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>przykład programu cURL

cURL to narzędzie wiersza polecenia dostępne w systemie Linux (i podsystem Windows dla systemu Linux). To polecenie cURL ilustruje sposób uzyskania tokenu dostępu. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji usługa rozpoznawania mowy. Upewnij się, że używasz właściwego punktu końcowego dla regionu, który pasuje do Twojej subskrypcji. W tym przykładzie jest aktualnie skonfigurowana do regionu zachodnie stany USA.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Przykład w języku C#

To C# klasy ilustruje sposób uzyskania tokenu dostępu. Przekaż swój klucz subskrypcji usługa rozpoznawania mowy, podczas tworzenia wystąpienia klasy. Jeśli Twoja subskrypcja nie znajduje się w regionie zachodnie stany USA, zmień wartość `FetchTokenUri` do dopasowania regionie dla Twojej subskrypcji.

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="how-to-use-an-access-token"></a>Sposób użycia tokenu dostępu

Token dostępu powinny być przesyłane do usługi jako `Authorization: Bearer <TOKEN>` nagłówka. Każdy token dostępu jest ważny przez 10 minut. Możesz uzyskać nowy token w dowolnym momencie, aby zminimalizować ruch sieciowy i opóźnienia, firma Microsoft zaleca jednak przy użyciu tego samego tokenu na dziewięć minut.

Oto przykładowe żądanie HTTP, zamiany tekstu na mowę interfejsu API REST:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="how-to-renew-an-access-token-using-c"></a>Jak odnowić, dostęp do tokenu przy użyciuC#

To C# kod jest zamiennikiem dla klasy przedstawiony wcześniej. `Authentication` Klasa automatycznie otrzymuje nowy token dostępu co dziewięć minut za pomocą czasomierza. Takie podejście zapewnia, że prawidłowy token jest zawsze dostępny podczas działania programu.

> [!NOTE]
> Zamiast używać czasomierza, można przechowywać sygnaturę czasową po ostatni token został uzyskany. Następnie możesz zażądać nowego tylko wtedy, gdy jest bliski wygaśnięcia. Ta metoda pozwala uniknąć niepotrzebnego żądanie nowych tokenów i może być bardziej odpowiednie dla programów, które rzadko żądaniami funkcji rozpoznawania mowy.

Tak jak poprzednio, upewnij się, że `FetchTokenUri` wartość odpowiada regionie Twojej subskrypcji. Podczas tworzenia wystąpienia klasy, należy przekazać swój klucz subskrypcji.

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="speech-to-text-api"></a>Interfejs API zamiany mowy na tekst

Interfejs API REST mowy na tekst obsługuje tylko krótkie wypowiedzi. Żądania może zawierać maksymalnie 10 sekund audio z łączny czas trwania 14 sekund. Interfejs API REST zwraca tylko wyniki końcowe, wyniki nie częściowego lub przejściowym.

Jeśli wysyłanie dłużej audio jest wymagana dla aplikacji, należy rozważyć użycie [zestaw SDK rozpoznawania mowy](speech-sdk.md) lub [batch transkrypcji](batch-transcription.md).

### <a name="regions-and-endpoints"></a>Regiony i punktów końcowych

Te regiony są obsługiwane w przypadku przekształcania mowy na tekst przy użyciu interfejsu API REST. Upewnij się, wybierz pozycję punkt końcowy, który odpowiada Twoim regionie subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>Parametry zapytania

Te parametry mogą być zawarte w ciągu zapytania żądania REST.

| Parametr | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| `language` | Określa język mówiony, który jest rozpoznawany. Zobacz [obsługiwane języki](language-support.md#speech-to-text). | Wymagane |
| `format` | Określa format wyniku. Akceptowane wartości to `simple` i `detailed`. Proste wyniki obejmują `RecognitionStatus`, `DisplayText`, `Offset`, i `Duration`. Precyzyjne reakcje obejmują wiele wyników z wartościami zaufania i cztery różne reprezentacje. Ustawieniem domyślnym jest `simple`. | Optional (Opcjonalność) |
| `profanity` | Określa sposób obsługi wulgaryzmów w wyniki rozpoznawania. Akceptowane wartości to `masked`, gwiazdek, która zastępuje wulgaryzmów `removed`, który Usuń wszystkie wulgaryzmów z wyników, lub `raw`, w tym wulgaryzmów w wyniku. Ustawieniem domyślnym jest `masked`. | Optional (Opcjonalność) |

### <a name="request-headers"></a>Nagłówki żądań

Ta tabela zawiera wymagane i opcjonalne nagłówki dla żądania zamiany mowy na tekst.

|Nagłówek| Opis | Wymagane / opcjonalne |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klucz subskrypcji usługa rozpoznawania mowy. | Albo tego pliku nagłówkowego lub `Authorization` jest wymagana. |
| `Authorization` | Token autoryzacji poprzedzone wyrazem `Bearer`. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Albo tego pliku nagłówkowego lub `Ocp-Apim-Subscription-Key` jest wymagana. |
| `Content-type` | W tym artykule opisano format i kodera-dekodera audio podanych danych. Akceptowane wartości to `audio/wav; codecs=audio/pcm; samplerate=16000` i `audio/ogg; codecs=opus`. | Wymagane |
| `Transfer-Encoding` | Określa, czy fragmentaryczne dane audio są wysyłane, zamiast pojedynczego pliku. Ten nagłówek należy używać tylko, jeśli dane audio. | Optional (Opcjonalność) |
| `Expect` | Jeśli używasz fragmentaryczne transferu, Wyślij `Expect: 100-continue`. Usługa rozpoznawania mowy potwierdza żądanie początkowe i czeka na dodatkowe dane.| Wymagany, jeśli wysyłanie danych audio podzielonego. |
| `Accept` | Jeśli podano, musi on być `application/json`. Usługa rozpoznawania mowy udostępnia wyniki w formacie JSON. Niektóre środowiska żądania sieci Web Podaj wartość domyślną niezgodne, jeśli nie zostanie określony, dzięki czemu jest dobrym rozwiązaniem jest zawsze zawierać `Accept`. | Opcjonalne, ale zalecane. |

### <a name="audio-formats"></a>Formaty audio

Dźwięku w treści HTTP `POST` żądania. Musi być w jednym z formatów w tej tabeli:

| Format | Koder-dekoder | Szybkość transmisji bitów | Częstotliwość próbkowania |
|--------|-------|---------|-------------|
| WAV | MODUŁU PCM | 16-bitowych | 16 kHz, narzędzie mono |
| OGG | DZIELE | 16-bitowych | 16 kHz, narzędzie mono |

>[!NOTE]
>Powyższe formaty są obsługiwane za pośrednictwem interfejsu API REST i WebSocket usługi mowy. [Zestaw SDK rozpoznawania mowy](speech-sdk.md) aktualnie obsługuje tylko WAV Formatuj przy użyciu kodera-dekodera PCM.

### <a name="sample-request"></a>Przykładowe żądanie

Jest to typowy żądania HTTP. Poniższy przykład obejmuje nazwę hosta i wymagane nagłówki. Należy zauważyć, że usługa oczekuje również dane audio, która nie znajduje się w tym przykładzie. Jak wspomniano wcześniej, segmentu jest zalecane, jednak nie jest wymagane.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi wskazuje sukces lub typowych błędów.

| Kod stanu HTTP | Opis | Możliwa przyczyna |
|------------------|-------------|-----------------|
| 100 | Kontynuuj | Wstępne żądanie zostało zaakceptowane. Czy kontynuować wysyłanie pozostałe dane. (Używany przy użyciu transferu pakietowego). |
| 200 | OK | Żądanie powiodło się; treść odpowiedzi jest obiekt JSON. |
| 400 | Nieprawidłowe żądanie | Kod języka podany lub nie jest obsługiwany język; Nieprawidłowy plik audio. |
| 401 | Brak autoryzacji | Klucz subskrypcji lub autoryzacji token jest nieprawidłowy w określonym regionie lub nieprawidłowy punkt końcowy. |
| 403 | Zabroniony | Brak klucz subskrypcji lub autoryzacji tokenu. |

### <a name="chunked-transfer"></a>Fragmentaryczne transferu

Transferu pakietowego (`Transfer-Encoding: chunked`) może pomóc zmniejszyć opóźnienie rozpoznawania, ponieważ zezwala ona na usługi mowy rozpoczęcie przetwarzania plików audio, gdy są przesyłane. Interfejs API REST nie zapewnia tymczasowe lub częściowe wyniki. Ta opcja jest przeznaczona wyłącznie do zwiększyć szybkość reakcji.

Ten przykładowy kod przedstawia sposób wysłania audio we fragmentach. Tylko pierwszy fragment może zawierać nagłówek pliku audio. `request` Obiekt HTTPWebRequest podłączonego do odpowiedniego punktu końcowego REST. `audioFile` jest to ścieżka do pliku audio na dysku.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
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

### <a name="response-parameters"></a>Parametrów odpowiedzi

Wyniki są dostarczane w formacie JSON. `simple` Formatu zawiera następujące pola najwyższego poziomu.

| Parametr | Opis  |
|-----------|--------------|
|`RecognitionStatus`|Stan, takich jak `Success` rozpoznawania się pomyślnie. Zobacz następną tabelę.|
|`DisplayText`|Rozpoznany tekst po wielkość liter, znaki interpunkcyjne, tekst odwrotny normalizacji (Konwersja tekstu mówionego na krótszą formularzy, takich jak 200, "200" lub "odzyskiwania po awarii. Smith""lekarzem Smith"), a wulgaryzmów maskowania. Istnieje tylko w przypadku powodzenia.|
|`Offset`|Czas (w jednostkach 100-nanosekundowych), jaką rozpoznawanym mowy rozpoczyna się w strumienia audio.|
|`Duration`|Czas trwania (w jednostkach 100-nanosekundowych) rozpoznawaną mowy w strumienia audio.|

`RecognitionStatus` Pole może zawierać następujące wartości:

| Stan | Opis |
|--------|-------------|
| `Success` | Rozpoznawanie zakończyło się pomyślnie i `DisplayText` pole jest obecny. |
| `NoMatch` | Wykryto mowy strumienia audio, ale bez wyrazów z języka docelowego zostały dopasowane. Zwykle oznacza, że rozpoznawanie jest inny język niż te, które użytkownik mówiącego Prezydenta. |
| `InitialSilenceTimeout` | Początek strumienia audio zawiera tylko wyciszenia, a usługa upłynął limit czasu oczekiwania podczas rozpoznawania mowy. |
| `BabbleTimeout` | Początek strumienia audio zawiera tylko hałasu i usługi, upłynął limit czasu oczekiwania podczas rozpoznawania mowy. |
| `Error` | Usługa rozpoznawania napotkał błąd wewnętrzny i nie może kontynuować działania. Spróbuj ponownie, jeśli to możliwe. |

> [!NOTE]
> Jeśli audio składa się tylko z wulgaryzmów i `profanity` parametr zapytania ma wartość `remove`, usługa nie zwróciła wynik mowy.

`detailed` Format obejmuje te same dane co `simple` formacie wraz z `NBest`, listę alternatywnych interpretacji ten sam wynik rozpoznawania mowy. Te wyniki są oceniane od najbardziej prawdopodobne najmniej prawdopodobnie pierwszy wpis jest taki sam jak wynik rozpoznawania głównego.  Korzystając z `detailed` formacie `DisplayText` jest dostarczana jako `Display` dla każdego wyniku `NBest` listy.

Każdy obiekt w `NBest` lista zawiera:

| Parametr | Opis |
|-----------|-------------|
| `Confidence` | Współczynnik ufności wpisu od 0,0 (nie zaufania) 1.0 (pełne zaufanie) |
| `Lexical` | Leksykalne formularza rozpoznany tekst: rzeczywistymi słowami został rozpoznany. |
| `ITN` | Znormalizowane tekstu odwrotność ("") forma kanoniczna rozpoznany, za pomocą telefonu liczb, liczby, skróty ("lekarzem smith" do "odzyskiwania po awarii smith") i inne zastosowane przekształcenia. |
| `MaskedITN` | Formularz podczas przy użyciu maskowania wulgaryzmów stosowane, jeśli jest to wymagane. |
| `Display` | Formularz wyświetlania rozpoznany tekst, znaki interpunkcyjne i dodaje wielkie litery. Ten parametr jest taka sama jak `DisplayText` pod warunkiem, gdy skonfigurowano format `simple`. |

### <a name="sample-responses"></a>Przykład odpowiedzi

Jest to typowa odpowiedź dla `simple` rozpoznawania.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Jest to typowa odpowiedź dla `detailed` rozpoznawania.

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="text-to-speech-api"></a>Interfejs API zamiany tekstu na mowę

Zamiany tekstu na mowę interfejsu API REST obsługuje neuronowych i standard głosów zamiany tekstu na mowę, z których każdy obsługuje określonego języka i dialektu, identyfikowane za pomocą ustawień regionalnych.

* Aby uzyskać pełną listę głosów, zobacz [języki](language-support.md#text-to-speech).
* Aby uzyskać informacje o dostępności regionalnej, zobacz [regionów](regions.md#text-to-speech).

### <a name="request-headers"></a>Nagłówki żądań

Ta tabela zawiera wymagane i opcjonalne nagłówki dla żądania zamiany mowy na tekst.

| Nagłówek | Opis | Wymagane / opcjonalne |
|--------|-------------|---------------------|
| `Authorization` | Token autoryzacji poprzedzone wyrazem `Bearer`. Aby uzyskać więcej informacji, zobacz [uwierzytelniania](#authentication). | Wymagane |
| `Content-Type` | Określa typ zawartości dla podanego tekstu. Akceptowane wartości: `application/ssml+xml`. | Wymagane |
| `X-Microsoft-OutputFormat` | Określa format danych wyjściowych audio. Aby uzyskać pełną listę akceptowanych wartości, zobacz [danych wyjściowych audio](#audio-outputs). | Wymagane |
| `User-Agent` | Nazwa aplikacji. Musi być krótsza niż 255 znaków. | Wymagane |

### <a name="audio-outputs"></a>Dane wyjściowe audio

Jest to lista obsługiwanych formatów audio, które są wysyłane do wszystkich żądań jako `X-Microsoft-OutputFormat` nagłówka. Każdy łączy w sobie szybkość transmisji bitów i typ kodowania. Usługa rozpoznawania mowy obsługuje 24 KHz i 16 KHz audio danych wyjściowych.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-mulaw` | `riff-16khz-16bit-mono-pcm` |
| `audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3` |
| `audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm` |
| `riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3` |
| `audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3` |

> [!NOTE]
> Jeśli wybrany głosu i format danych wyjściowych inne szybkości transmisji bitów, audio jest próbkowany zgodnie z potrzebami. Jednak nie obsługują głosów 24khz `audio-16khz-16kbps-mono-siren` i `riff-16khz-16kbps-mono-siren` formaty danych wyjściowych.

### <a name="request-body"></a>Treść żądania

Treść każdego `POST` żądanie jest wysyłane jako [mowy syntezy Markup Language (SSML)](speech-synthesis-markup.md). SSML pozwala wybrać, głos i język syntezatora mowy zwracane przez usługę zamiany tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [języki](language-support.md#text-to-speech).

> [!NOTE]
> Jeśli używasz niestandardowych voice, treści żądania mogą być wysyłane jako zwykły tekst (ASCII lub UTF-8).

### <a name="sample-request"></a>Przykładowe żądanie

To żądanie HTTP używa SSML w celu określenia połączeń głosowych i języka. Treść nie może przekraczać 1000 znaków.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi wskazuje sukces lub typowych błędów.

| Kod stanu HTTP | Opis | Możliwa przyczyna |
|------------------|-------------|-----------------|
| 200 | OK | Żądanie powiodło się; treść odpowiedzi jest plik audio. |
| 400 | Nieprawidłowe żądanie | Wymagany parametr jest Brak, pusta lub równa null. Lub wartość przekazana do każdego wymaganego lub opcjonalnego parametru jest nieprawidłowa. Typowym problemem jest nagłówkiem, który jest za długi. |
| 401 | Brak autoryzacji | Żądanie nie jest autoryzowany. Zaznacz, aby upewnić się, że klucz subskrypcji lub token jest prawidłowy i w poprawny region. |
| 413 | Jednostka żądania jest zbyt duża | Dane wejściowe SSML jest dłuższa niż 1024 znaki. |
| 429 | Zbyt wiele żądań | Przekroczono limit przydziału lub liczbę żądań dozwoloną przez subskrypcję. |
| 502 | Zła brama | Problem z siecią lub po stronie serwera. Może również oznaczać nieprawidłowy nagłówek. |

W przypadku stanu HTTP `200 OK`, treść odpowiedzi zawiera plik audio w formacie żądanej. Ten plik można odtwarzać, jak ma przesyłane, zapisany do buforu lub zapisany w pliku.

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
