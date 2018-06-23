---
title: Uwierzytelnianie usługi Microsoft mowy | Dokumentacja firmy Microsoft
description: Żądanie uwierzytelniania za pomocą interfejsu API mowy firmy Microsoft
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: e36168cf3ff938af44f1028c2d26fd475d60b148
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347424"
---
# <a name="authenticate-to-the-speech-api"></a>Uwierzytelnianie mowy interfejsu API

Usługa rozpoznawania mowy obsługuje uwierzytelnianie przy użyciu:

- Klucz subskrypcji.
- Token autoryzacji.

## <a name="use-a-subscription-key"></a>Użyj klawisza subskrypcji

Korzystać z usługi rozpoznawania mowy, należy zasubskrybować mowy interfejsu API, który jest częścią usługi kognitywnych (wcześniej Oxford projektu). Możesz uzyskać klucze bezpłatnej subskrypcji próbnej z [subskrypcji usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API mowy wybierz **uzyskać klucz interfejsu API** uzyskać klucza. Zwraca wartość klucza podstawowego i pomocniczego. Obydwu kluczy są powiązane z tego samego przydziału, dzięki czemu można użyć albo klucza.

Do długoterminowego użycia lub zwiększenia limitu przydziału, należy zarejestrować się w celu [konta Azure](https://azure.microsoft.com/free/).

Aby korzystać z interfejsu API REST mowy, musisz przekazać klucz subskrypcji w `Ocp-Apim-Subscription-Key` pole w nagłówku żądania.

Name (Nazwa)| Format| Opis
----|-------|------------
OCP-Apim subskrypcji — klawisz | ASCII | YOUR_SUBSCRIPTION_KEY

Poniżej przedstawiono przykład nagłówek żądania:

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
> Jeśli używasz [bibliotek klienckich](../GetStarted/GetStartedClientLibraries.md) w aplikacji, sprawdź, czy można uzyskać tokenu autoryzacji kluczem subskrypcji, zgodnie z opisem w poniższej sekcji. Biblioteki klienta za pomocą klawisza subskrypcji do pobrania tokenu autoryzacji, a następnie użyj tokenu na potrzeby uwierzytelniania.

## <a name="use-an-authorization-token"></a>Użyj tokenu autoryzacji

Alternatywnie można użyć tokenu autoryzacji uwierzytelniania jako dowód uwierzytelniania/autoryzacji. Aby uzyskać token, należy najpierw uzyskać klucz subskrypcji z interfejsu API mowy, zgodnie z opisem w [powyższej sekcji](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Pobierz token autoryzacji

Po klucz ważnej subskrypcji, wysłanie żądania POST do usługi tokenu kognitywnych usług. W odpowiedzi pojawi się token autoryzacji jako JSON Web Token (JWT).

> [!NOTE]
> Token ma wygaśnięcia 10 minut. Aby odnowić token, zobacz następującą sekcję.

Identyfikator URI usługi tokenu jest zlokalizowana tutaj:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Poniższy przykładowy kod przedstawia sposób uzyskać token dostępu. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji:

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

W przykładzie użyto curl w systemie Linux z bash. Jeśli nie jest dostępna na platformie, może być konieczne zainstalowanie curl. Przykład działa także w programów Cygwin na systemu Windows, Git Bash, zsh i innych powłoki.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

Jeśli nie możesz uzyskać upoważnienia tokenów z usługi tokenu, sprawdź, czy klucz Subskrypcja jest nadal ważny. Jeśli korzystasz z bezpłatnej wersji próbnej klucza, przejdź do [subskrypcji usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) strony, kliknij "Logowanie", aby zalogować się przy użyciu konta używanego do stosowania bezpłatnej wersji próbnej klucza i sprawdź, czy klucz subskrypcja wygasła lub przekracza limit przydziału.

### <a name="use-an-authorization-token-in-a-request"></a>Użyj tokenu autoryzacji w żądaniu

Zawsze wywołania interfejsu API mowy, musisz przekazać token autoryzacji w `Authorization` nagłówka. `Authorization` Nagłówek musi zawierać token dostępu JWT.

Poniższy przykład przedstawia sposób użycia tokenu autoryzacji po wywołaniu interfejsu API REST mowy.

> [!NOTE]
> Zastąp `YOUR_AUDIO_FILE` ze ścieżką do pliku nagrań audio. Zastąp `YOUR_ACCESS_TOKEN` z tokenem autoryzacji uzyskanego w poprzednim kroku [uzyskać token autoryzacji](#get-an-authorization-token).

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

Token autoryzacji wygasa po określonym czasie (obecnie 10 minut). Należy odnowić token autoryzacji przed jego wygaśnięciem.

Następujący kod jest przykładem implementacji w języku C# sposobu odnawiania tokenu autoryzacji:

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
