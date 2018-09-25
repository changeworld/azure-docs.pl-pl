---
title: Usługa rozpoznawania mowy interfejsów API REST
description: Dokumentacja interfejsów API REST usługi mowy.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 6758cd658daf75beeea93bf9c719508cd271c8be
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032431"
---
# <a name="speech-service-rest-apis"></a>Usługa rozpoznawania mowy interfejsów API REST

Interfejsy API REST usługi Azure Cognitive Services unified mowy usługi są podobne do interfejsów API dostarczonych przez [modułu Speech API Bing](https://docs.microsoft.com/azure/cognitive-services/Speech). Punktów końcowych, które różnią się od punktów końcowych używanych przez usługę rozpoznawania mowy Bing. Regionalne punkty końcowe są dostępne i trzeba użyć klucza subskrypcji, która odnosi się do punktu końcowego, którego używasz.

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Punktów końcowych rozpoznawania mowy, interfejsu API REST usługi tekstowe są wyświetlane w poniższej tabeli. Użyj jednego, który odpowiada Twoim regionie subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

> [!NOTE]
> Jeśli dostosowany model akustyczny lub model języka lub wymowa, należy użyć niestandardowego punktu końcowego.

Ten interfejs API obsługuje tylko krótkie wypowiedzi. Żądania może zawierać maksymalnie 10 sekund audio i ostatnie 14 sekundy ogólnej. Interfejs API REST zwraca wyniki tylko końcowej, nie przejściowym lub częściowe wyniki. Usługa rozpoznawania mowy ma również [batch transkrypcji](batch-transcription.md) interfejsu API, które można wykonać transkrypcji audio dłużej.

### <a name="query-parameters"></a>Parametry zapytania

Mogą zostać dołączone następujące parametry ciągu zapytania żądania REST.

|Nazwa parametru|Wymagane/opcjonalne|Znaczenie|
|-|-|-|
|`language`|Wymagane|Identyfikator doceniona języka. Zobacz [obsługiwane języki](supported-languages.md#speech-to-text).|
|`format`|Optional (Opcjonalność)<br>Wartość domyślna: `simple`|Format wyników `simple` lub `detailed`. Proste wyniki obejmują `RecognitionStatus`, `DisplayText`, `Offset`i czas trwania. Szczegółowe wyniki będą zawierać wielu kandydatów z wartościami zaufania i cztery różne reprezentacje.|
|`profanity`|Optional (Opcjonalność)<br>Wartość domyślna: `masked`|Jak obsługiwać wulgaryzmów w wyniki rozpoznawania. Może być `masked` (zastępuje wulgaryzmów gwiazdkami), `removed` (spowoduje to usunięcie wszystkich wulgaryzmów), lub `raw` (w tym wulgaryzmów).

### <a name="request-headers"></a>Nagłówki żądań

Następujące pola są wysyłane w nagłówku żądania HTTP.

|Nagłówek|Znaczenie|
|------|-------|
|`Ocp-Apim-Subscription-Key`|Klucz subskrypcji usługi mowy. Albo tego pliku nagłówkowego lub `Authorization` musi zostać podana.|
|`Authorization`|Token autoryzacji poprzedzone wyrazem `Bearer`. Albo tego pliku nagłówkowego lub `Ocp-Apim-Subscription-Key` musi zostać podana. Zobacz [uwierzytelniania](#authentication).|
|`Content-type`|W tym artykule opisano format i kodera-dekodera audio danych. Obecnie ta wartość musi być `audio/wav; codec=audio/pcm; samplerate=16000`.|
|`Transfer-Encoding`|Opcjonalny. Jeśli podana, musi być `chunked` umożliwia danych audio w celu ich wysłania w wielu małych fragmentów zamiast pojedynczego pliku.|
|`Expect`|Jeśli używasz fragmentaryczne transferu, Wyślij `Expect: 100-continue`. Usługa rozpoznawania mowy potwierdza żądanie początkowe i czeka na dodatkowe dane.|
|`Accept`|Opcjonalny. Jeśli podano, musi zawierać `application/json`, jak usługa mowy udostępnia wyniki w formacie JSON. (Niektóre struktury żądania sieci Web Podaj wartość domyślną niezgodne, jeśli nie zostanie określony, dzięki czemu jest dobrym rozwiązaniem jest zawsze zawierać `Accept`.)|

### <a name="audio-format"></a>Audio format

Dźwięku w treści HTTP `PUT` żądania. Należy go w formacie WAV PCM pojedynczy kanał (mono) na 16 KHz 16-bitowych.

### <a name="chunked-transfer"></a>Fragmentaryczne transferu

Transferu pakietowego (`Transfer-Encoding: chunked`) może pomóc zmniejszyć opóźnienie rozpoznawania, ponieważ zezwala ona na usługi mowy rozpoczęcie przetwarzania plików audio, gdy są przesyłane. Interfejs API REST nie zapewnia tymczasowe lub częściowe wyniki. Ta opcja jest przeznaczona wyłącznie do zwiększyć szybkość reakcji.

Poniższy kod ilustruje sposób wysyłania audio we fragmentach. `request` Obiekt HTTPWebRequest podłączonego do odpowiedniego punktu końcowego REST. `audioFile` jest to ścieżka do pliku audio na dysku.

```csharp
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

### <a name="example-request"></a>Przykładowe żądanie

Poniżej przedstawiono typowe żądania.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status"></a>Stan HTTP

Stanu HTTP odpowiedzi wskazuje sukces lub typowe warunki błędów.

Kod HTTP|Znaczenie|Możliwa przyczyna
-|-|-|
100|Kontynuuj|Wstępne żądanie zostało zaakceptowane. Czy kontynuować wysyłanie pozostałe dane. (Używany przy użyciu transferu pakietowego).
200|OK|Żądanie powiodło się; treść odpowiedzi jest obiekt JSON.
400|Nieprawidłowe żądanie|Kod języka podany lub nie jest obsługiwany język; Nieprawidłowy plik audio.
401|Brak autoryzacji|Klucz subskrypcji lub autoryzacji token jest nieprawidłowy w określonym regionie lub nieprawidłowy punkt końcowy.
403|Zabroniony|Brak klucz subskrypcji lub autoryzacji tokenu.

### <a name="json-response"></a>Odpowiedź w formacie JSON

Wyniki są zwracane w formacie JSON. `simple` Format obejmuje tylko następujące pola najwyższego poziomu.

|Nazwa pola|Zawartość|
|-|-|
|`RecognitionStatus`|Stan, takich jak `Success` rozpoznawania się pomyślnie. Zobacz następną tabelę.|
|`DisplayText`|Rozpoznany tekst po wielkość liter, znaki interpunkcyjne, tekst odwrotny normalizacji (Konwersja tekstu mówionego na krótszą formularzy, takich jak 200, "200" lub "odzyskiwania po awarii. Smith""lekarzem Smith"), a wulgaryzmów maskowania. Istnieje tylko w przypadku powodzenia.|
|`Offset`|Czas (w jednostkach 100-nanosekundowych), jaką rozpoznawanym mowy rozpoczyna się w strumienia audio.|
|`Duration`|Czas trwania (w jednostkach 100-nanosekundowych) rozpoznawaną mowy w strumienia audio.|

`RecognitionStatus` Pole może zawierać następujących wartości.

|Wartość stanu|Opis
|-|-|
| `Success` | Rozpoznawanie zakończyło się pomyślnie, a pole Wyświetlany_tekst jest obecny. |
| `NoMatch` | Wykryto mowy strumienia audio, ale bez wyrazów z języka docelowego zostały dopasowane. Zwykle oznacza, że rozpoznawanie jest inny język niż te, które użytkownik mówiącego Prezydenta. |
| `InitialSilenceTimeout` | Początek strumienia audio zawiera tylko wyciszenia, a usługa upłynął limit czasu oczekiwania podczas rozpoznawania mowy. |
| `BabbleTimeout` | Początek strumienia audio zawiera tylko hałasu i usługi, upłynął limit czasu oczekiwania podczas rozpoznawania mowy. |
| `Error` | Usługa rozpoznawania napotkał błąd wewnętrzny i nie może kontynuować działania. Spróbuj ponownie, jeśli to możliwe. |

> [!NOTE]
> Jeśli audio składa się tylko z wulgaryzmów i `profanity` parametr zapytania ma wartość `remove`, usługa nie zwróciła wynik mowy. 


`detailed` Format obejmuje te same pola jako `simple` formacie wraz z `NBest` pola. `NBest` Pola znajduje się lista alternatywnych interpretacji tych samych mowy, randze spośród wszystkich dokumentów z największym prawdopodobieństwem najmniej prawdopodobne. Pierwszy wpis jest taki sam jak wynik rozpoznawania głównego. Każdy wpis zawiera następujące pola:

|Nazwa pola|Zawartość|
|-|-|
|`Confidence`|Współczynnik ufności wpisu od 0,0 (nie zaufania) 1.0 (pełne zaufanie)
|`Lexical`|Leksykalne formularza rozpoznany tekst: rzeczywistymi słowami został rozpoznany.
|`ITN`|Znormalizowane tekstu odwrotność ("") forma kanoniczna rozpoznany, za pomocą telefonu liczb, liczby, skróty ("lekarzem smith" do "odzyskiwania po awarii smith") i inne zastosowane przekształcenia.
|`MaskedITN`| Formularz podczas przy użyciu maskowania wulgaryzmów stosowane, jeśli jest to wymagane.
|`Display`| Formularz wyświetlania rozpoznany tekst, znaki interpunkcyjne i dodaje wielkie litery. Taki sam jak `DisplayText` w wyniku najwyższego poziomu.

### <a name="sample-responses"></a>Przykład odpowiedzi

Poniżej przedstawiono typowe odpowiedzi dla `simple` rozpoznawania.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Poniżej przedstawiono typowe odpowiedzi dla `detailed` rozpoznawania.

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

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Dostępne są następujące punkty końcowe REST dla usługi mowy tekst na mowę interfejsu API. Użycie punktu końcowego, który odpowiada Twoim regionie subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

> [!NOTE]
> Jeśli utworzono niestandardową czcionkę głosową, należy użyć skojarzone niestandardowego punktu końcowego.

Usługa rozpoznawania mowy obsługuje wyjściowego audio 24 KHz, oprócz danych wyjściowych 16 Khz, obsługiwane przez rozpoznawania mowy Bing. Cztery formaty danych wyjściowych 24 KHz są dostępne do użycia w `X-Microsoft-OutputFormat` nagłówka HTTP, jak są dwa głosy 24 KHz, `Jessa24kRUS` i `Guy24kRUS`.

Ustawienia regionalne | Język   | Płeć | Mapowanie nazwy usługi
-------|------------|--------|------------
pl-PL  | US English | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, Jessa24kRUS)" 
pl-PL  | US English | Mężczyzna   | "Microsoft Server mowy Text na głos mowy (en US, Guy24kRUS)"

Pełną listę dostępnych głosów jest dostępna w [obsługiwane języki](supported-languages.md#text-to-speech).

### <a name="request-headers"></a>Nagłówki żądań

Następujące pola są wysyłane w nagłówku żądania HTTP.

|Nagłówek|Znaczenie|
|------|-------|
|`Authorization`|Token autoryzacji poprzedzone wyrazem `Bearer`. Wymagany. Zobacz [uwierzytelniania](#authentication).|
|`Content-Type`|Typ zawartości danych wejściowych: `application/ssml+xml`.|
|`X-Microsoft-OutputFormat`|Format danych wyjściowych audio. Zobacz następną tabelę.|
|`User-Agent`|Nazwa aplikacji. Wymagane; musi zawierać mniej niż 255 znaków.|

Formaty danych wyjściowych audio dostępne (`X-Microsoft-OutputFormat`) na szybkość transmisji bitów i kodowania.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `raw-8khz-8bit-mono-mulaw`
`riff-8khz-8bit-mono-mulaw`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

> [!NOTE]
> Jeśli wybrany głosu i format danych wyjściowych inne szybkości transmisji bitów, audio jest próbkowany zgodnie z potrzebami. Jednak nie obsługują głosów 24khz `audio-16khz-16kbps-mono-siren` i `riff-16khz-16kbps-mono-siren` formaty danych wyjściowych. 

### <a name="request-body"></a>Treść żądania

Tekst, który ma zostać przekonwertowany na mowę w celu jest wysyłany jako treść HTTP `POST` żądania albo jako zwykły tekst (ASCII lub UTF-8) lub [język znaczników synteza mowy](speech-synthesis-markup.md) formatu (SSML) (UTF-8). Żądania w postaci zwykłego tekstu za pomocą usługi głosowe w domyślnego i języka. Wyślij SSML używać różnych głosu.

### <a name="sample-request"></a>Przykładowe żądanie

Następujące żądanie HTTP używa treści SSML wyboru mowy. Treść musi być dłuższy niż 1000 znaków.

```xml
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

### <a name="http-response"></a>Odpowiedź HTTP

Stanu HTTP odpowiedzi wskazuje sukces lub typowe warunki błędów.

Kod HTTP|Znaczenie|Możliwa przyczyna
-|-|-|
200|OK|Żądanie powiodło się; treść odpowiedzi jest plik audio.
400 |Nieprawidłowe żądanie |Wymagany parametr jest Brak, pusta lub równa null. Lub wartość przekazana do każdego wymaganego lub opcjonalnego parametru jest nieprawidłowa. Typowym problemem jest nagłówkiem, który jest za długi.
401|Brak autoryzacji |Żądanie nie jest autoryzowany. Zaznacz, aby upewnić się, że klucz subskrypcji lub token jest prawidłowy i w poprawny region.
413|Jednostka żądania jest zbyt duża|Dane wejściowe SSML jest dłuższa niż 1024 znaki.
|502|Zła brama    | Problem z siecią lub po stronie serwera. Może również oznaczać nieprawidłowy nagłówek.

W przypadku stanu HTTP `200 OK`, treść odpowiedzi zawiera plik audio w formacie żądanej. Mogą być odtwarzane tego pliku, ponieważ przeniesione lub zapisany do buforu lub nowszej odtwarzania lub inne użycie w pliku.

## <a name="authentication"></a>Authentication

Wysyła żądanie do interfejsu API REST usługi mowy wymaga klucza subskrypcji albo tokenu dostępu. Ogólnie rzecz biorąc najłatwiej jest bezpośrednio wysyłać klucz subskrypcji. Usługa rozpoznawania mowy następnie uzyskuje token dostępu dla Ciebie. Aby zminimalizować czas odpowiedzi, możesz chcieć użyć tokenu dostępu.

Do uzyskania tokenu, przedstawia swój klucz subskrypcji z usługą mowy regionalnych `issueToken` punktu końcowego, jak pokazano w poniższej tabeli. Użycie punktu końcowego, który odpowiada Twoim regionie subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Każdy token dostępu jest ważny przez 10 minut. W dowolnym momencie można uzyskać nowy token. Jeśli chcesz można uzyskać tokenu po prostu przed każdym żądaniem Speech REST API. Aby zminimalizować ruch sieciowy i opóźnienia, zaleca się przy użyciu tego samego tokenu na dziewięć minut.

W poniższych sekcjach opisano, jak uzyskać token i jak z niej korzystać w żądaniu.

### <a name="get-a-token-http"></a>Pobierz token: HTTP

Poniższy przykład przedstawia przykładowe żądanie HTTP, do uzyskania tokenu. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie znajduje się w regionie zachodnie stany USA, należy zastąpić `Host` nagłówek o nazwie hosta w Twoim regionie.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Treść odpowiedzi do tego żądania jest token dostępu w formacie Java tokenu sieci Web (JWT).

### <a name="get-a-token-powershell"></a>Pobierz token: PowerShell

Poniższy skrypt programu Windows PowerShell ilustruje sposób uzyskania tokenu dostępu. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie znajduje się w regionie zachodnie stany USA, należy odpowiednio zmienić nazwę hosta dla danego identyfikatora URI.

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

### <a name="get-a-token-curl"></a>Pobierz token: cURL

cURL to narzędzie wiersza polecenia dostępne w systemie Linux (i podsystem Windows dla systemu Linux). Następujące polecenie cURL ilustruje sposób uzyskania tokenu dostępu. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie znajduje się w regionie zachodnie stany USA, należy odpowiednio zmienić nazwę hosta dla danego identyfikatora URI.

> [!NOTE]
> Polecenie jest wyświetlane w wielu wierszach, aby zwiększyć czytelność, ale wprowadź go w jednym wierszu w wierszu polecenia powłoki.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="get-a-token-c"></a>Pobierz token: C#

Następujące klasy C# ilustruje sposób uzyskania tokenu dostępu. Podczas tworzenia wystąpienia klasy, należy przekazać swój klucz subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie znajduje się w regionie zachodnie stany USA, Zmień nazwę hosta `FetchTokenUri` odpowiednio.

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

### <a name="use-a-token"></a>Użyj tokenu

Aby użyć tokenu żądania interfejsu API REST, podaj je w `Authorization` nagłówka, po słowie `Bearer`. Oto przykład tekstu do żądania REST mowy, który zawiera token. Podstaw rzeczywiste token dla `YOUR_ACCESS_TOKEN`. Użyj poprawną nazwę hosta w `Host` nagłówka.

```xml
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

### <a name="renew-authorization"></a>Odnów autoryzację

Token autoryzacji wygasa po upływie 10 minut. Odnów autoryzację dzięki uzyskaniu nowego tokenu, przed jego wygaśnięciem. Na przykład można uzyskać nowy token po dziewięciu minut.

Poniższy kod C# jest zamiennikiem dla klasy przedstawiony wcześniej. `Authentication` Klasy automatycznie uzyskuje nowy token dostępu co dziewięć minut za pomocą czasomierza. Takie podejście zapewnia, że prawidłowy token jest zawsze dostępny podczas działania programu.

> [!NOTE]
> Zamiast używać czasomierza, można przechowywać sygnaturę czasową po ostatni token został uzyskany. Następnie możesz zażądać nowego tylko wtedy, gdy jest bliski wygaśnięcia. Ta metoda pozwala uniknąć niepotrzebnego żądanie nowych tokenów i może być bardziej odpowiednie dla programów, które rzadko żądaniami funkcji rozpoznawania mowy.

Tak jak poprzednio, upewnij się, że `FetchTokenUri` wartość odpowiada regionie Twojej subskrypcji. Podczas tworzenia wystąpienia klasy, należy przekazać swój klucz subskrypcji.

```cs
/*
    * This class demonstrates how to maintain a valid access token.
    */
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

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md)
- [Dostosowywanie modeli językowych](how-to-customize-language-model.md)

