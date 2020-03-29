---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: dc5e251fee00ee22edb2261c1abd8404714834ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78669310"
---
## <a name="authentication"></a>Uwierzytelnianie

Każde żądanie wymaga nagłówka autoryzacji. W tej tabeli przedstawiono, które nagłówki są obsługiwane dla każdej usługi:

| Obsługiwane nagłówki autoryzacji | Zamiana mowy na tekst | Zamiana tekstu na mowę |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Tak | Nie |
| Autoryzacja: Okaziciela | Tak | Tak |

Podczas korzystania `Ocp-Apim-Subscription-Key` z nagłówka, musisz tylko podać klucz subskrypcji. Przykład:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Podczas korzystania `Authorization: Bearer` z nagłówka, musisz złożyć żądanie `issueToken` do punktu końcowego. W tym żądaniu można wymienić klucz subskrypcji token dostępu, który jest prawidłowy przez 10 minut. W następnych kilku sekcjach dowiesz się, jak uzyskać token i użyć tokenu.

### <a name="how-to-get-an-access-token"></a>Jak uzyskać token dostępu

Aby uzyskać token dostępu, musisz złożyć żądanie `issueToken` do punktu `Ocp-Apim-Subscription-Key` końcowego przy użyciu klucza subskrypcji i.

Punkt `issueToken` końcowy ma ten format:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Zamień `<REGION_IDENTIFIER>` identyfikator odpowiadający regionowi subskrypcji z tej tabeli:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Użyj tych przykładów, aby utworzyć żądanie tokenu dostępu.

#### <a name="http-sample"></a>Przykład HTTP

W tym przykładzie jest proste żądanie HTTP, aby uzyskać token. Zamień `YOUR_SUBSCRIPTION_KEY` klucz subskrypcji usługi mowy. Jeśli subskrypcji nie ma w regionie Zachodnie `Host` stany USA, zastąp nagłówek nazwą hosta w regionie.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Treść odpowiedzi zawiera token dostępu w formacie JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell

W tym przykładzie jest prosty skrypt programu PowerShell, aby uzyskać token dostępu. Zamień `YOUR_SUBSCRIPTION_KEY` klucz subskrypcji usługi mowy. Upewnij się, że używasz poprawnego punktu końcowego dla regionu, który pasuje do subskrypcji. Ten przykład jest obecnie ustawiony na zachodnie stany USA.

```powershell
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

#### <a name="curl-sample"></a>Próbka cURL

cURL to narzędzie wiersza polecenia dostępne w systemie Linux (i w podsystemie Windows dla systemu Linux). To polecenie cURL ilustruje sposób uzyskiwania tokenu dostępu. Zamień `YOUR_SUBSCRIPTION_KEY` klucz subskrypcji usługi mowy. Upewnij się, że używasz poprawnego punktu końcowego dla regionu, który pasuje do subskrypcji. Ten przykład jest obecnie ustawiony na zachodnie stany USA.

```console
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Przykład w języku C#

Ta klasa Języka C# ilustruje sposób uzyskiwania tokenu dostępu. Przekaż klucz subskrypcji usługi mowy podczas tworzenia wystąpienia klasy. Jeśli subskrypcji nie ma w regionie Zachodnie stany `FetchTokenUri` USA, zmień wartość, aby dopasować go do regionu subskrypcji.

```csharp
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

#### <a name="python-sample"></a>Przykład pythona

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Jak używać tokenu dostępu

Token dostępu powinny być wysyłane do `Authorization: Bearer <TOKEN>` usługi jako nagłówek. Każdy token dostępu jest ważny przez 10 minut. Możesz uzyskać nowy token w dowolnym momencie, jednak, aby zminimalizować ruch sieciowy i opóźnienia, zaleca się przy użyciu tego samego tokenu przez dziewięć minut.

Oto przykładowe żądanie HTTP do interfejsu API REST od zamiany tekstu na mowę:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
