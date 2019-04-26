---
title: Uwierzytelnianie rozpoznawania mowy Bing | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Żądania uwierzytelniania do użycia interfejsu API rozpoznawania mowy Bing
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 11d6256fb63452b849a80abab181876d14b3b6a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515056"
---
# <a name="authenticate-to-the-speech-api"></a>Uwierzytelnianie interfejsu API mowy

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Rozpoznawanie mowy Bing obsługuje uwierzytelnianie przy użyciu:

- Klucz subskrypcji.
- Token autoryzacji.

## <a name="use-a-subscription-key"></a>Klucz subskrypcji

Aby korzystać z usługi rozpoznawania mowy, muszą najpierw zasubskrybować interfejsu API rozpoznawania mowy, który jest częścią usług Cognitive Services (wcześniej Project Oxford). Możesz uzyskać bezpłatną subskrypcję próbną kluczy z [subskrypcji usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API rozpoznawania mowy, wybierz opcję **Uzyskaj klucz interfejsu API** można pobrać klucza. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tego samego limitu przydziału, aby można było używać żadnego z nich.

Dla długoterminowego użytkowania lub zwiększenia limitu przydziału, należy zasubskrybować [konta platformy Azure](https://azure.microsoft.com/free/).

Aby korzystać z interfejsu API REST mowy, należy przekazać klucz subskrypcji w `Ocp-Apim-Subscription-Key` pole w nagłówku żądania.

Name (Nazwa)| Format| Opis
----|-------|------------
OCP-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Oto przykład nagłówek żądania:

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
> Jeśli używasz [biblioteki klienckie](../GetStarted/GetStartedClientLibraries.md) w aplikacji, sprawdź, czy można pobrać tokenu autoryzacji z kluczem subskrypcji, zgodnie z opisem w poniższej sekcji. Biblioteki klienckie Użyj klucz subskrypcji do uzyskania tokenu autoryzacji, a następnie użyj tokenu służącego do uwierzytelniania.

## <a name="use-an-authorization-token"></a>Użyj tokenu autoryzacji

Alternatywnie można użyć tokenu autoryzacji dla uwierzytelniania jako dowód uwierzytelniania/autoryzacji. Aby uzyskać token, należy najpierw uzyskać klucz subskrypcji z interfejsu API rozpoznawania mowy, zgodnie z opisem w [poprzedniej sekcji](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Pobierz token autoryzacji

Po umieszczeniu klucza ważnej subskrypcji, należy wysłać żądanie POST do tokenu usługi Cognitive Services. W odpowiedzi otrzymasz token autoryzacji jako JSON Web Token (JWT).

> [!NOTE]
> Token ma wygaśnięcia 10 minut. Aby odnowić token, zobacz następującą sekcję.

Identyfikator URI tokenu usługi znajduje się tutaj:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Poniższy przykładowy kod przedstawia sposób uzyskania tokenu dostępu. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji:

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

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

# <a name="curltabcurl"></a>[curl](#tab/curl)

W przykładzie użyto programu curl w systemie Linux przy użyciu programu bash. Jeśli nie jest dostępny na Twojej platformie, może być konieczne do zainstalowania programu curl. Przykład działa również na Cygwin na Windows, systemu Git Bash, zsh i innych powłoki.

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

Poniżej przedstawiono przykładowe żądanie POST:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Jeśli nie otrzymasz autoryzacji token z usługi tokenu, sprawdź, czy klucz subskrypcji jest nadal ważny. Jeśli używasz bezpłatnej wersji próbnej klucza, przejdź do strony [subskrypcji usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) stronie, kliknij "Zaloguj", aby zalogować się przy użyciu konta, którego użyto dla bezpłatnej wersji próbnej klucza i sprawdź, czy klucz subskrypcji wygasło lub przekracza limit przydziału.

### <a name="use-an-authorization-token-in-a-request"></a>Użyj tokenu autoryzacji w żądaniu

Każdorazowo wywołania interfejsu API rozpoznawania mowy, musisz przekazać token autoryzacji w `Authorization` nagłówka. `Authorization` Nagłówek musi zawierać token dostępu JWT.

Poniższy przykład przedstawia sposób użycia tokenu autoryzacji podczas wywoływania interfejsu API REST mowy.

> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio. Zastąp `YOUR_ACCESS_TOKEN` tokenem autoryzacji uzyskanego w poprzednim kroku [uzyskania tokenu autoryzacji](#get-an-authorization-token).

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

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

# <a name="curltabcurl"></a>[curl](#tab/curl)

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

### <a name="renew-an-authorization-token"></a>Odnów token autoryzacji

Token autoryzacji wygasa po określonym czasie (obecnie 10 minut). Musisz odnowić token autoryzacji, przed jego wygaśnięciem.

Poniższy kod jest przykładem implementacji w języku C#, jak do odnowienia tokenu autoryzacji:

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
