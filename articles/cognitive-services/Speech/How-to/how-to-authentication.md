---
title: Uwierzytelnianie w rozpoznawanie mowy Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Żądaj uwierzytelniania, aby używać interfejs API rozpoznawania mowy Bing
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d1e708ff29293b87935d0d191ba44ad4a11917a0
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965653"
---
# <a name="authenticate-to-the-speech-api"></a>Uwierzytelnianie w Speech API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Rozpoznawanie mowy Bing obsługuje uwierzytelnianie przy użyciu:

- Klucz subskrypcji.
- Token autoryzacji.

## <a name="use-a-subscription-key"></a>Korzystanie z klucza subskrypcji

Aby korzystać z usługi mowy, musisz najpierw subskrybować Speech API, które są częścią Cognitive Services (wcześniej Project Oxford). Klucze subskrypcji bezpłatnej wersji próbnej możesz uzyskać ze strony [subskrypcji Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) . Po wybraniu Speech API wybierz pozycję **Pobierz klucz interfejsu API** , aby uzyskać klucz. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tym samym limitem przydziału, więc można użyć obu kluczy.

Aby uzyskać długoterminowe użycie lub zwiększony limit przydziału, Utwórz [konto platformy Azure](https://azure.microsoft.com/free/).

Aby użyć interfejsu API REST usługi Speech, należy przekazać klucz `Ocp-Apim-Subscription-Key` subskrypcji do pola w nagłówku żądania.

Name| Format| Opis
----|-------|------------
OCP-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Poniżej znajduje się przykład nagłówka żądania:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> Jeśli używasz [bibliotek klienckich](../GetStarted/GetStartedClientLibraries.md) w aplikacji, sprawdź, czy możesz uzyskać Token autoryzacji z kluczem subskrypcji, zgodnie z opisem w poniższej sekcji. Biblioteki klienckie używają klucza subskrypcji w celu uzyskania tokenu autoryzacji, a następnie użycia tokenu do uwierzytelniania.

## <a name="use-an-authorization-token"></a>Użyj tokenu autoryzacji

Alternatywnie można użyć tokenu autoryzacji do uwierzytelniania jako potwierdzenie uwierzytelniania/autoryzacji. Aby uzyskać ten token, należy najpierw uzyskać klucz subskrypcji z Speech API, zgodnie z opisem w [poprzedniej sekcji](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Pobieranie tokenu autoryzacji

Po prawidłowym kluczu subskrypcji Wyślij żądanie POST do usługi tokenów Cognitive Services. W odpowiedzi otrzymujesz Token autoryzacji jako token sieci Web JSON (JWT).

> [!NOTE]
> Ważność tokenu wynosi 10 minut. Aby odnowić token, zapoznaj się z następującą sekcją.

Identyfikator URI usługi tokenu znajduje się tutaj:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Poniższy przykład kodu pokazuje, jak uzyskać token dostępu. Zastąp `YOUR_SUBSCRIPTION_KEY` własnym kluczem subskrypcji:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="curltabcurl"></a>[odsłon](#tab/curl)

W przykładzie zastosowano zwinięcie w systemie Linux z bash. Jeśli nie jest on dostępny na twojej platformie, może być konieczne zainstalowanie zawieszania. Przykład działa również na Cygwin w systemie Windows, Git bash, zsh i innych powłokach.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

Oto przykładowe żądanie POST:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Jeśli nie możesz uzyskać tokenu autoryzacji z usługi tokenów, sprawdź, czy klucz subskrypcji jest nadal ważny. Jeśli używasz bezpłatnego klucza wersji próbnej, przejdź do strony [subskrypcji Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) , kliknij przycisk Zaloguj się, aby zalogować się przy użyciu konta użytego do zastosowania bezpłatnego klucza wersji próbnej, i sprawdź, czy klucz subskrypcji wygasł lub przekracza limit przydziału.

### <a name="use-an-authorization-token-in-a-request"></a>Używanie tokenu autoryzacji w żądaniu

Za każdym razem, gdy wywołasz Speech API, musisz przekazać Token autoryzacji w `Authorization` nagłówku. `Authorization` Nagłówek musi zawierać token dostępu JWT.

Poniższy przykład pokazuje, jak używać tokenu autoryzacji podczas wywoływania interfejsu API REST mowy.

> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio. Zamień `YOUR_ACCESS_TOKEN` na token autoryzacji uzyskany w poprzednim kroku [Pobierz token autoryzacji](#get-an-authorization-token).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="curltabcurl"></a>[odsłon](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
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

---

### <a name="renew-an-authorization-token"></a>Odnów Token autoryzacji

Token autoryzacji wygasa po upływie określonego czasu (obecnie 10 minut). Token autoryzacji należy odnowić przed jego wygaśnięciem.

Poniższy kod stanowi przykład implementacji C# odnowienia tokenu autoryzacji:

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```
