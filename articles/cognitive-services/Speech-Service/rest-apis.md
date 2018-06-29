---
title: Usługa rozpoznawania mowy interfejsów API REST | Dokumentacja firmy Microsoft
description: Odwołanie do interfejsów API REST usługi mowy.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 53560fd4f8240c4446898f58992a9319e5177435
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085375"
---
# <a name="speech-service-rest-apis"></a>Usługa rozpoznawania mowy interfejsów API REST

Interfejsy API REST usługi ujednoliconego mowy są podobne do interfejsów API dostarczonych przez [API mowy](https://docs.microsoft.com/azure/cognitive-services/Speech) (wcześniej znane jako mowy usługi Bing). Punktów końcowych, które różnią się od punktów końcowych używanych przez usługę poprzedniej mowy.

## <a name="speech-to-text"></a>Zamiana mowy na tekst

W mowy API tekstu tylko punktów końcowych używanych różnią się od poprzedniej mowy usługi interfejsu API rozpoznawania mowy. W poniższej tabeli przedstawiono nowe punkty końcowe. Użyj, który odpowiada regionie Twojej subskrypcji.

[!include[](includes/endpoints-speech-to-text.md)]

Mowy API tekstu w przeciwnym razie jest podobny do [interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) poprzedniego interfejsu API mowy.

Mowy tekst REST API obsługuje tylko krótki zniesławiających. Żądań może zawierać maksymalnie 10 sekund dźwięku i ostatniej sekundy 14 ogólnej. Interfejs API REST zwraca tylko wyniki końcowe nie wyniki częściowe lub tymczasowe.

> [!NOTE]
> Jeśli dostosowano akustycznego modelu lub model języka lub wymowy należy użyć niestandardowego punktu końcowego.

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Nowy tekst na mowę interfejs API obsługuje 24 KHz wyjścia audio. `X-Microsoft-OutputFormat` Nagłówek teraz może zawierać następujące wartości.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

Usługa mowy zapewnia teraz dwie głosy 24 KHz:

Ustawienia regionalne | Język   | Płeć | Mapowanie nazw usługi
-------|------------|--------|------------
pl-PL  | US English | Kobieta | "Microsoft Server mowy tekstu na głos mowy (en US, Jessa24kRUS)" 
pl-PL  | US English | Mężczyzna   | "Microsoft Server mowy tekstu na głos mowy (en US, Guy24kRUS)"

Dostępne są następujące punkty końcowe REST do ujednoliconego mowy usługi tekst na mowę interfejsu API. Użyj punktu końcowego, który odpowiada regionie Twojej subskrypcji.

[!include[](includes/endpoints-text-to-speech.md)]

Zachowaj te różnice pamiętać jako odwoływać się [dokumentacja interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) poprzedniego interfejsu API mowy.

## <a name="authentication"></a>Authentication

Wysyłanie żądania do interfejsu API REST usługi mowy wymaga tokenu dostępu. Uzyskania tokenu zapewniając klucz subskrypcji do regionalnych usługi mowy `issueToken` punktu końcowego, co pokazano w poniższej tabeli. Użyj punktu końcowego, który odpowiada regionie Twojej subskrypcji.

[!include[](includes/endpoints-token-service.md)]

Każdy token dostępu jest ważny przez 10 minut. W dowolnym momencie można uzyskać nowy token — w tym, jeśli chcesz tylko przed każdym żądaniem interfejsu API REST mowy. Aby zminimalizować ruch sieciowy i czas oczekiwania, jednak zaleca się przy użyciu tego samego tokenu dziewięć minut.

Poniższe sekcje pokazują, jak uzyskać token i jak z niego korzystać w żądaniu.

### <a name="getting-a-token-http"></a>Pobieranie tokenu: HTTP

Poniżej przedstawiono przykładowe żądanie HTTP do uzyskania tokenu. Zastąp `YOUR_SUBSCRIPTION_KEY` kluczem subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie jest w regionu zachodnie stany USA, Zastąp `Host` nagłówka z nazwami hostów w Twoim regionie.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Treść odpowiedzi na to żądanie jest token dostępu w formacie Java tokenów sieci Web (JWT).

### <a name="getting-a-token-powershell"></a>Pobieranie tokenu: środowiska PowerShell

Poniższy skrypt programu Windows PowerShell ilustruje sposób uzyskać token dostępu. Zastąp `YOUR_SUBSCRIPTION_KEY` kluczem subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie jest w regionu zachodnie stany USA, należy odpowiednio zmień nazwę hosta danego identyfikatora URI.

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

### <a name="getting-a-token-curl"></a>Pobieranie tokenu: cURL

Zwinięcie jest dostępne w systemie Linux (i w podsystemie systemu Windows dla systemu Linux) Narzędzie wiersza polecenia. Poniższego polecenia cURL przedstawiono sposób uzyskiwania tokenu dostępu. Zastąp `YOUR_SUBSCRIPTION_KEY` kluczem subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie jest w regionu zachodnie stany USA, należy odpowiednio zmień nazwę hosta danego identyfikatora URI.

> [!NOTE]
> Polecenie jest wyświetlany w wielu wierszach dla czytelności, ale należy wprowadzić w jednym wierszu w wierszu polecenia powłoki.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Pobieranie tokenu: C#

C# klasy poniżej przedstawiono sposób uzyskiwania tokenu dostępu. Podczas tworzenia wystąpienia klasy, należy przekazać klucz subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie jest w regionu zachodnie stany USA, Zmień nazwę hosta `FetchTokenUri` odpowiednio.

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

### <a name="using-a-token"></a>Za pomocą tokenu

Aby użyć tokenu żądania interfejsu API REST, podaj go w `Authorization` nagłówka, po słowie `Bearer`. W tym miejscu na przykład jest przykładowy tekst REST mowy żądanie zawierające token. Zastępuje token rzeczywiste dla `YOUR_ACCESS_TOKEN` i używa poprawne nazwy hosta w `Host` nagłówka.

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

### <a name="renewing-authorization"></a>Odnawianie autoryzacji

Token autoryzacji wygasa po upływie 10 minut. Odnów autoryzację, uzyskując nowy token, przed jego wygaśnięciem — na przykład po dziewięć minut. 

Poniższy kod C# nie zastępuje dzięki wsuwanej konstrukcji klasy przedstawione wcześniej. `Authentication` Klasy automatycznie uzyskuje nowy token dostępu co dziewięć minut za pomocą czasomierza. Ta metoda gwarantuje, że prawidłowy token jest zawsze dostępna podczas działania programu.

> [!NOTE]
> Zamiast używać czasomierz, można przechowywać o po tokenu bieżącego uzyskano, a następnie poprosić o nowe tylko wtedy, gdy bieżący token jest bliski wygaśnięcia. Takie podejście pozwala uniknąć niepotrzebnego żąda nowe tokeny i może być bardziej odpowiednie dla programów, które rzadko żądania mowy.

Jak wcześniej, upewnij się, że `FetchTokenUri` wartość odpowiada regionie Twojej subskrypcji. Podczas tworzenia wystąpienia klasy, należy przekazać swój klucz subskrypcji.

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

- [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
- [Dostosowywanie akustycznego modeli](how-to-customize-acoustic-models.md)
- [Dostosowywanie modeli języka](how-to-customize-language-model.md)

