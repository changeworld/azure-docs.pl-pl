---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 9cad860b8808dd2682995768c282d8376ab5d9be
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58671735"
---
## <a name="authentication"></a>Authentication

Każde żądanie wymaga nagłówka autoryzacji. W poniższej tabeli zestawiono, które nagłówki są obsługiwane dla każdej usługi:

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

[!INCLUDE [](./cognitive-services-speech-service-endpoints-token-service.md)]

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

Treść odpowiedzi zawiera token dostępu w formacie tokenu Web JSON (JWT).

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

#### <a name="python-sample"></a>Przykład środowiska Python

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

### <a name="how-to-use-an-access-token"></a>Sposób użycia tokenu dostępu

Token dostępu powinny być przesyłane do usługi jako `Authorization: Bearer <TOKEN>` nagłówka. Każdy token dostępu jest ważny przez 10 minut. Możesz uzyskać nowy token w dowolnym momencie, aby zminimalizować ruch sieciowy i opóźnienia, firma Microsoft zaleca jednak przy użyciu tego samego tokenu na dziewięć minut.

Oto przykładowe żądanie HTTP, zamiany tekstu na mowę interfejsu API REST:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
