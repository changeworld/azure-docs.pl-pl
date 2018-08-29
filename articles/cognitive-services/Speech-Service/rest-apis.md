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
ms.openlocfilehash: 64dce26303c0e700da54d371af5cb275b1613d70
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122107"
---
# <a name="speech-service-rest-apis"></a>Usługa rozpoznawania mowy interfejsów API REST

Interfejsy API REST, ujednolicone usługi mowy są podobne do interfejsów API dostarczonych przez [interfejsu API rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/Speech) (wcześniej znane jako usługa rozpoznawania mowy Bing). Punktów końcowych, które różnią się od punktów końcowych używanych przez usługę rozpoznawania mowy w poprzednim.

## <a name="speech-to-text"></a>Zamiana mowy na tekst

W funkcji rozpoznawania mowy do interfejsu API tłumaczenia tekstu tylko punktów końcowych używanych różnią się od poprzedniej Speech service interfejs API rozpoznawania mowy. W poniższej tabeli przedstawiono nowe punkty końcowe. Użyj jednego, który odpowiada Twoim regionie subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

Zamiana mowy na interfejs API tłumaczenia tekstu w przeciwnym razie jest podobny do [interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) poprzedniego interfejsu API rozpoznawania mowy.

Zamiana mowy na tekst REST API obsługuje tylko krótkie wypowiedzi. Żądania może zawierać maksymalnie 10 sekund audio i ostatnie 14 sekundy ogólnej. Interfejs API REST zwraca tylko wyników końcowych nie przejściowym lub częściowe wyniki.

> [!NOTE]
> Jeśli dostosowany model akustyczny lub model języka lub wymowa, należy użyć niestandardowego punktu końcowego.

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Nowy tekst na mowę interfejs API obsługuje 24 KHz wyjściowego audio. `X-Microsoft-OutputFormat` Nagłówek może teraz zawierać następujące wartości.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

Usługa rozpoznawania mowy udostępnia teraz dwa głosy 24 KHz:

Ustawienia regionalne | Język   | Płeć | Mapowanie nazwy usługi
-------|------------|--------|------------
pl-PL  | US English | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, Jessa24kRUS)" 
pl-PL  | US English | Mężczyzna   | "Microsoft Server mowy Text na głos mowy (en US, Guy24kRUS)"

Dostępne są następujące punkty końcowe REST dla ujednolicone usługi tekstu na mowę interfejsu API. Użycie punktu końcowego, który odpowiada Twoim regionie subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

Zachowanie tych różnic, pamiętając, jak możesz odwołać się do [dokumentację interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) poprzedniego interfejsu API rozpoznawania mowy.

## <a name="authentication"></a>Authentication

Wysyła żądanie do interfejsu API REST usługi mowy wymaga tokenu dostępu. Uzyskania tokenu, podając swój klucz subskrypcji z usługą mowy regionalnych `issueToken` punktu końcowego, co pokazano w poniższej tabeli. Użycie punktu końcowego, który odpowiada Twoim regionie subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Każdy token dostępu jest ważny przez 10 minut. W dowolnym momencie można uzyskać nowy token — w tym, jeśli chcesz, tuż przed wywołaniem każdego żądania interfejsu API REST mowy. Aby zminimalizować ruch sieciowy i czas oczekiwania, jednak zalecamy używanie tego samego tokenu na dziewięć minut.

W poniższych sekcjach opisano, jak uzyskać token i jak z niej korzystać w żądaniu.

### <a name="getting-a-token-http"></a>Uzyskanie tokenu: HTTP

Poniżej przedstawiono przykładowe żądanie HTTP, do uzyskania tokenu. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie jest w regionie zachodnie stany USA, należy zastąpić `Host` nagłówka z nazwą hosta w Twoim regionie.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Treść odpowiedzi do tego żądania jest token dostępu w formacie Java tokenu sieci Web (JWT).

### <a name="getting-a-token-powershell"></a>Uzyskanie tokenu: PowerShell

Poniższy skrypt programu Windows PowerShell ilustruje sposób uzyskania tokenu dostępu. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie jest dostępne w regionie zachodnie stany USA, należy odpowiednio zmienić nazwę hosta danego identyfikatora URI.

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

### <a name="getting-a-token-curl"></a>Uzyskanie tokenu: cURL

cURL to narzędzie wiersza polecenia dostępne w systemie Linux (i podsystem Windows dla systemu Linux). Poniższe polecenie cURL ilustruje sposób uzyskania tokenu dostępu. Zastąp `YOUR_SUBSCRIPTION_KEY` z kluczem subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie jest dostępne w regionie zachodnie stany USA, należy odpowiednio zmienić nazwę hosta danego identyfikatora URI.

> [!NOTE]
> Polecenie jest wyświetlane w wielu wierszach, aby zwiększyć czytelność, ale powinny być wprowadzane w jednym wierszu w wierszu polecenia powłoki.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Uzyskanie tokenu: C#

Klasa C# poniżej pokazano, jak można uzyskać tokenu dostępu. Podczas tworzenia wystąpienia klasy, należy przekazać swój klucz subskrypcji usługi mowy. Jeśli Twoja subskrypcja nie jest w regionie zachodnie stany USA, Zmień nazwę hosta `FetchTokenUri` odpowiednio.

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

Aby użyć tokenu żądania interfejsu API REST, podaj je w `Authorization` nagłówka, po słowie `Bearer`. Na przykład Oto przykład tekstu do żądania REST mowy, zawierający tokenu. Podstaw rzeczywiste token dla `YOUR_ACCESS_TOKEN` i używa poprawne nazwy hosta w `Host` nagłówka.

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

Token autoryzacji wygasa po upływie 10 minut. Odnów autoryzację dzięki uzyskaniu nowego tokenu, przed jego wygaśnięciem — na przykład po dziewięciu minut. 

Poniższy kod C# jest zamiennikiem dla klasy przedstawiony wcześniej. `Authentication` Klasy automatycznie uzyskuje nowy token dostępu co dziewięć minut za pomocą czasomierza. Takie podejście zapewnia, że prawidłowy token jest zawsze dostępny podczas działania programu.

> [!NOTE]
> Zamiast używać czasomierza, można przechowywać sygnaturę czasową, gdy bieżący token zostały pobrane, a następnie poprosić o nowe tylko wtedy, gdy bieżący token jest bliski wygaśnięcia. Ta metoda pozwala uniknąć niepotrzebnego żądanie nowych tokenów i może być bardziej odpowiednie dla programów, które rzadko żądaniami funkcji rozpoznawania mowy.

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

