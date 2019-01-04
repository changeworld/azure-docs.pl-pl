---
title: Usługa Azure Functions HTTP wyzwalaczy i powiązań
description: Dowiedz się, jak używać protokołu HTTP, wyzwalaczy i powiązań w usłudze Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: usługi Azure functions, funkcje, zdarzeń przetwarzania, elementy webhook dynamicznych zasobów obliczeniowych, architektura bez serwera HTTP, API, REST
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 8d2bd74609447463b7ff857aa1037eaf5b6e3abb
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727007"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Usługa Azure Functions HTTP wyzwalaczy i powiązań

W tym artykule wyjaśniono, jak pracować z wyzwalaczami HTTP i powiązania danych wyjściowych w usłudze Azure Functions.

Wyzwalacz HTTP można dostosować tak, aby odpowiedzieć na [elementów webhook](https://en.wikipedia.org/wiki/Webhook).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania protokołu HTTP, znajdują się w [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) pakietu NuGet w wersji 1.x. Kod źródłowy dla pakietu znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania protokołu HTTP, znajdują się w [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) pakietu NuGet w wersji 3.x. Kod źródłowy dla pakietu znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz HTTP umożliwia wywołanie funkcji z żądania HTTP. Wyzwalacz HTTP służy do tworzenia interfejsów API bez użycia serwera i odpowiadać na elementy webhook.

Domyślnie wyzwalacz HTTP zwraca HTTP 200 OK o pustej treści w funkcjach 1.x lub HTTP 204 Brak zawartości z pustą treść w funkcjach 2.x. Aby zmodyfikować odpowiedzi, należy skonfigurować [powiązania danych wyjściowych HTTP](#output).

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , szuka `name` parametru w ciągu zapytania lub treści żądania HTTP. Należy zauważyć, że zwracana wartość jest używana dla powiązania danych wyjściowych, ale atrybut zwracana wartość nie jest wymagana.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie pokazano powiązanie wyzwalacza w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja wyszukuje `name` parametru w ciągu zapytania lub treści żądania HTTP.

Oto *function.json* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Oto C# kod skryptu, który tworzy powiązanie z `HttpRequestMessage`:

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
{
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

Możesz powiązać niestandardowy obiekt zamiast `HttpRequestMessage`. Ten obiekt jest tworzony z treści żądania, analizować w formacie JSON. Podobnie typem może być przekazywany do odpowiedzi HTTP, dane wyjściowe, powiązania i zwrócony jako treść odpowiedzi, wraz z kodem stanu 200.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(CustomObject req, ILogger log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public string name {get; set;}
}
```

### <a name="trigger---f-example"></a>Wyzwalacz - F# przykład

W poniższym przykładzie pokazano powiązanie wyzwalacza w *function.json* pliku i [ F# funkcja](functions-reference-fsharp.md) powiązania, który używa. Funkcja wyszukuje `name` parametru w ciągu zapytania lub treści żądania HTTP.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Oto F# kodu:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Potrzebujesz `project.json` plik, który używa NuGet, aby odwołać się do `FSharp.Interop.Dynamic` i `Dynamitey` zespołów, jak pokazano w poniższym przykładzie:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie pokazano powiązanie wyzwalacza w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja wyszukuje `name` parametru w ciągu zapytania lub treści żądania HTTP.

Oto *function.json* pliku:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

### <a name="trigger---python-example"></a>Wyzwalacz — przykładem w języku Python

W poniższym przykładzie pokazano powiązanie wyzwalacza w *function.json* pliku i [funkce Pythonu](functions-reference-python.md) powiązania, który używa. Funkcja wyszukuje `name` parametru w ciągu zapytania lub treści żądania HTTP.

Oto *function.json* pliku:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod języka Python:

```python
import logging
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

### <a name="trigger---java-example"></a>Wyzwalacz - przykładzie w języku Java

W poniższym przykładzie pokazano powiązanie wyzwalacza w *function.json* pliku i [funkcja Java](functions-reference-java.md) powiązania, który używa. Funkcja zwraca odpowiedź HTTP status kod 200 z treści żądania, które prefiksy wyzwalająca treści żądania za pomocą "Witaj," pozdrowienia.


Oto *function.json* pliku:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

Oto kodu Java:

```java
@FunctionName("hello")
public HttpResponseMessage<String> hello(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS), Optional<String> request,
                        final ExecutionContext context)
    {
        // default HTTP 200 response code
        return String.format("Hello, %s!", request);
    }
}
```

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) atrybutu.

Można ustawić autoryzację poziomu i dozwolone metody HTTP w Parametry Konstruktora atrybutu, a ma właściwości dla typu i trasy szablonu elementu webhook. Aby uzyskać więcej informacji o tych ustawieniach, zobacz [wyzwalacza — Konfiguracja](#trigger---configuration). Oto `HttpTrigger` atrybutu w podpisie metody:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequestMessage req)
{
    ...
}
 ```

Aby uzyskać kompletny przykład, zobacz [wyzwalacza — przykład w języku C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `HttpTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **type** | Nie dotyczy| Wymagana — musi być równa `httpTrigger`. |
| **direction** | Nie dotyczy| Wymagana — musi być równa `in`. |
| **Nazwa** | Nie dotyczy| Wymagana — nazwa zmiennej, używane w kodzie funkcji żądania lub treści żądania. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Określa, jakie klucze, musi być obecny na żądanie w celu wywołania funkcji. Poziom autoryzacji, może być jedną z następujących wartości: <ul><li><code>anonymous</code>&mdash;Klucz interfejsu API nie jest wymagana.</li><li><code>function</code>&mdash;Wymagany jest klucz interfejsu API specyficzne dla funkcji. Jeśli nie zostanie podana jest wartość domyślna.</li><li><code>admin</code>&mdash;Klucz główny jest wymagany.</li></ul> Aby uzyskać więcej informacji, zobacz sekcję [autoryzacji klucze](#authorization-keys). |
| **Metody** |**Metody** | Tablica metod HTTP, na które odpowiada funkcji. Jeśli nie zostanie określony, funkcja odpowiada na wszystkich metod HTTP. Zobacz [Dostosuj punkt końcowy http](#customize-the-http-endpoint). |
| **trasy** | **trasy** | Definiuje szablon trasy kontrolowanie, do której żądanie adresy URL reaguje funkcji. Jeśli nie zostanie podana wartość domyślna to `<functionname>`. Aby uzyskać więcej informacji, zobacz [Dostosuj punkt końcowy http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Obsługiwane tylko w przypadku środowisko uruchomieniowe 1.x wersji._<br/><br/>Konfiguruje wyzwalacza HTTP, która będzie działać jako [elementu webhook](https://en.wikipedia.org/wiki/Webhook) odbiornika dla określonego dostawcy. Nie należy ustawiać `methods` właściwość, jeśli ta właściwość jest ustawiona. Typ elementu webhook, może być jedną z następujących wartości:<ul><li><code>genericJson</code>&mdash;Ogólnego przeznaczenia punktu końcowego elementu webhook bez logiki dla określonego dostawcy. To ustawienie ogranicza żądania tylko te, które przy użyciu protokołu HTTP POST i za pomocą `application/json` typ zawartości.</li><li><code>github</code>&mdash;Funkcja odpowiada [elementy webhook GitHub](https://developer.github.com/webhooks/). Nie używaj _authLevel_ właściwości przy użyciu elementów webhook usługi GitHub. Aby uzyskać więcej informacji zobacz sekcję elementy webhook usługi GitHub, w dalszej części tego artykułu.</li><li><code>slack</code>&mdash;Funkcja odpowiada [Slack elementów webhook](https://api.slack.com/outgoing-webhooks). Nie używaj _authLevel_ właściwości przy użyciu elementów webhook Slack. Aby uzyskać więcej informacji zobacz sekcję Slack elementów webhook w dalszej części tego artykułu.</li></ul>|

## <a name="trigger---usage"></a>Wyzwalacz — użycie

Dla C# i F# funkcji, można zadeklarować rodzaj wyzwalacza danych wejściowych w celu być `HttpRequestMessage` lub typu niestandardowego. Jeśli wybierzesz `HttpRequestMessage`, uzyskujesz pełen dostęp do obiektu żądania. Dla typu niestandardowego środowiska uruchomieniowego próbuje przeanalizować treść żądania JSON, aby ustawić właściwości obiektu.

W przypadku funkcji JavaScript środowisko uruchomieniowe usługi Functions zapewnia treści żądania, a nie obiekt żądania. Aby uzyskać więcej informacji, zobacz [przykładowy wyzwalacz JavaScript](#trigger---javascript-example).

### <a name="customize-the-http-endpoint"></a>Dostosuj punkt końcowy HTTP

Domyślnie podczas tworzenia funkcji wyzwalacza HTTP, funkcja jest adresowalnych trasa formularza:

    http://<yourapp>.azurewebsites.net/api/<funcname>

Można dostosować tę trasę, za pomocą opcjonalnego `route` właściwości wyzwalacza HTTP dane wejściowe podane przez powiązanie. Na przykład następujące *function.json* plik definiuje `route` właściwości wyzwalacza HTTP:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Za pomocą tej konfiguracji, funkcja jest teraz mogą być adresowane za pomocą następujących tras zamiast oryginalnej trasy.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Dzięki temu kod funkcji obsługiwać dwa parametry w adresie _kategorii_ i _identyfikator_. Możesz użyć dowolnej [ograniczenia trasy interfejsu API sieci Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) z parametrami. Poniższy kod funkcji języka C# korzysta z obu parametrów.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id,
                                                ILogger log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Poniżej przedstawiono kod funkcji Node.js, który używa te same parametry trasy.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
}
```

Domyślnie wszystkie trasy funkcji mają prefiks *api*. Można również dostosować lub usunąć za pomocą prefiks `http.routePrefix` właściwości w swojej [host.json](functions-host-json.md) pliku. Poniższy przykład usuwa *api* prefiks trasy przy użyciu pustego ciągu do prefiksu w *host.json* pliku.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>Praca z tożsamości klienta

Jeśli aplikacja funkcji używa [uwierzytelnianie usługi App Service / autoryzacji](../app-service/overview-authentication-authorization.md), informacji o uwierzytelnionych klientów można wyświetlić w kodzie. Te informacje są dostępne jako [wprowadzony przez platformę nagłówki żądań](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Te informacje można również odczytywać powiązanie danych. Ta funkcja jest dostępna tylko na środowisko uruchomieniowe 2.x funkcji. Jest również obecnie tylko dostępne dla języków .NET.

W językach .NET, te informacje są dostępne jako [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal?view=netstandard-2.0). Elementu ClaimsPrincipal jest dostępny jako część kontekstu żądania, jak pokazano w poniższym przykładzie:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkResult();
}
```

Alternatywnie elementu ClaimsPrincipal po prostu może być uwzględniany jako dodatkowy parametr w sygnaturze funkcji:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}

```

### <a name="authorization-keys"></a>Klucze autoryzacji

Functions umożliwia utrudnić dostęp do funkcji punktów końcowych HTTP podczas programowania za pomocą klawiszy.  Standardowa wyzwalacza HTTP może wymagać klucza interfejsu API być obecne w żądaniu. 

> [!IMPORTANT]
> Gdy kluczy może pomóc zaciemniania punktów końcowych HTTP podczas tworzenia, nie są one przeznaczone jako sposobu zabezpieczenia wyzwalacza HTTP w środowisku produkcyjnym. Aby dowiedzieć się więcej, zobacz [bezpieczny punkt końcowy HTTP w środowisku produkcyjnym](#secure-an-http-endpoint-in-production).

> [!NOTE]
> W funkcje środowisko uruchomieniowe 1.x dostawców elementu webhook może autoryzować żądania na różne sposoby w zależności od dostawca obsługuje za pomocą klawiszy. To zagadnienie opisano w [elementów Webhook i klucze](#webhooks-and-keys). Środowisko uruchomieniowe 2.x wersji nie ma wbudowaną obsługę dostawców elementu webhook.

Istnieją dwa typy kluczy:

* **Klucze hosta**: Te klucze są współużytkowane przez wszystkie funkcje w obrębie aplikacji funkcji. Gdy jest używana jako klucz interfejsu API, te umożliwiają dostęp do dowolnej funkcji, w ramach aplikacji funkcji.
* **Klawisze funkcyjne**: Te klucze mają zastosowanie tylko do określonych funkcji, w których są zdefiniowane. Gdy jest używana jako klucz interfejsu API, te tylko zezwolić na dostęp do tej funkcji.

Każdy klucz nosi nazwę odniesienia i na poziomie funkcji i host ma domyślny klucz (o nazwie "domyślna"). Klawisze funkcyjne mają pierwszeństwo przed klucze hosta. Po zdefiniowaniu dwa klucze o takiej samej nazwie klucz funkcji zawsze jest używany.

Każda aplikacja funkcji ma również specjalny **klucz główny**. Ten klucz jest kluczem hosta o nazwie `_master`, który zapewnia dostęp administracyjny do interfejsów API środowiska uruchomieniowego. Nie można odwołać ten klucz. Po ustawieniu zezwolenia na poziomie `admin`, żądania muszą używać klucza głównego; inny klawisz, powoduje błąd autoryzacji.

> [!CAUTION]  
> Ze względu na podwyższonym poziomem uprawnień w aplikacji funkcji przyznane przy użyciu klucza głównego nie należy udostępniać tego klucza osobom trzecim lub rozpowszechnienie go w aplikacji klienta natywnego. Należy zachować ostrożność przy wyborze poziom dostępu administratora.

### <a name="obtaining-keys"></a>Uzyskiwanie kluczy

Klucze są przechowywane jako część aplikacji funkcji na platformie Azure i są szyfrowane w stanie spoczynku. Aby wyświetlić klucze, tworzenie nowych, lub Przywróć klucze do nowych wartości, przejdź do jednej z funkcji wyzwalanej przez HTTP na [witryny Azure portal](https://portal.azure.com) i wybierz **Zarządzaj**.

![Zarządzanie kluczami funkcji w portalu.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Nie ma żadnych obsługiwanych interfejsów API programowego uzyskiwania klawiszy funkcyjnych.

### <a name="api-key-authorization"></a>Autoryzacji klucza interfejsu API

Większość szablonów wyzwalacza HTTP wymaga klucza interfejsu API w żądaniu. Dlatego żądania HTTP zazwyczaj wygląda następującego adresu URL:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

Klucz mogły zostać uwzględnione w zmiennej ciągu zapytania o nazwie `code`, tak jak powyżej. Również mogły zostać uwzględnione w `x-functions-key` nagłówka HTTP. Wartość klucza może być dowolny klawisz funkcji zdefiniowanych dla funkcji lub dowolny klawisz hosta.

Możesz zezwolić na anonimowe żądania, które nie wymagają kluczy. Może również wymagać, że można użyć klucza głównego. Możesz zmienić domyślny poziom autoryzacji przy użyciu `authLevel` właściwość w powiązaniu JSON. Aby uzyskać więcej informacji, zobacz [wyzwalacza — Konfiguracja](#trigger---configuration).

> [!NOTE]
> Podczas uruchamiania funkcji lokalnie, autoryzacja jest wyłączony, bez względu na ustawienie poziomie określonej uwierzytelniania. Po opublikowaniu na platformie Azure, `authLevel` wymuszane jest ustawienie w wyzwalacza.



### <a name="secure-an-http-endpoint-in-production"></a>Bezpieczny punkt końcowy HTTP w środowisku produkcyjnym

Aby zabezpieczyć pełni funkcję punktów końcowych w środowisku produkcyjnym, należy rozważyć zaimplementowanie jednego z następujących opcji funkcji zabezpieczenia na poziomie aplikacji:

* Włącz uwierzytelnianie usługi App Service / autoryzacji dla aplikacji funkcji. Platforma usługi App Service pozwala używać do uwierzytelniania klientów usługi Azure Active Directory (AAD) i wielu dostawców tożsamości innych firm. Służy to do zaimplementowania reguł autoryzacji niestandardowej dla funkcji, a informacje o użytkownikach można pracować z kodu funkcji. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie i autoryzacja w usłudze Azure App Service](../app-service/overview-authentication-authorization.md) i [Praca z tożsamościami klientów](#working-with-client-identities).

* Użyj usługi Azure API Management (APIM), aby uwierzytelnić żądania. APIM oferuje różnorodne opcje zabezpieczeń interfejsu API dla żądań przychodzących. Aby dowiedzieć się więcej, zobacz [zasady uwierzytelniania usługi API Management](../api-management/api-management-authentication-policies.md). Za pomocą usługi APIM w miejscu można skonfigurować aplikację funkcji w celu umożliwienia akceptowania żądań tylko adres IP swojego wystąpienia usługi APIM. Aby dowiedzieć się więcej, zobacz [ograniczenia adresów IP](ip-addresses.md#ip-address-restrictions).

* Wdróż aplikację funkcji do usługi Azure App Service Environment (ASE). Środowisko ASE zawiera dedykowane Środowisko hostingu, w której chcesz uruchamiać swoje funkcje. Środowisko ASE można konfigurować pojedynczą bramą frontonu, która służy do uwierzytelniania wszystkich żądań przychodzących. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapory aplikacji sieci Web (WAF) dla środowiska App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Korzystając z jednej z następujących metod zabezpieczeń na poziomie aplikacji funkcji, należy skonfigurować uwierzytelnianie funkcji wyzwalanej przez HTTP poziom `anonymous`.

### <a name="webhooks"></a>Elementy webhook

> [!NOTE]
> Tryb elementu Webhook jest dostępna tylko dla wersji 1.x środowisko uruchomieniowe usługi Functions. Ta zmiana została wprowadzona w celu zwiększenia wydajności wyzwalaczy HTTP w wersji 2.x.

W wersji 1.x, szablony elementu webhook zapewniają dodatkowe sprawdzenie poprawności dla elementu webhook ładunków. W wersji 2.x, podstawowy wyzwalacza HTTP nadal działa i jest zalecane podejście do elementów webhook. 

#### <a name="github-webhooks"></a>Elementy webhook GitHub

Aby reagować na elementy webhook usługi GitHub, najpierw utworzyć funkcję z wyzwalaczem HTTP i ustaw **webHookType** właściwość `github`. Następnie skopiuj jej adres URL i klucz API w **Dodaj element webhook** strony repozytorium GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Elementy webhook Slack

Slack elementu webhook generuje token dla Ciebie zamiast pozwala określić, dlatego należy skonfigurować klucz właściwe dla funkcji przy użyciu tokenu z Slack. Zobacz [autoryzacji klucze](#authorization-keys).

### <a name="webhooks-and-keys"></a>Elementy Webhook i klucze

Autoryzacji elementu Webhook jest obsługiwany przez element webhook składnika odbiorcy, część wyzwalacza HTTP i mechanizm różni się w zależności od typu elementu webhook. Każdy mechanizm polegają na klucz. Domyślnie jest używany klucz funkcji o nazwie "domyślna". Aby użyć innego klucza, należy skonfigurować dostawcę elementu webhook, aby wysłać nazwę klucza z żądaniem w jednym z następujących sposobów:

* **Ciąg zapytania**: Dostawca przekazuje nazwę klucza w `clientid` zapytania parametr ciągu, takich jak `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
* **Nagłówek żądania**: Dostawca przekazuje nazwę klucza w `x-functions-clientid` nagłówka.

## <a name="trigger---limits"></a>Wyzwalacz — limity

Długość żądania HTTP jest ograniczona do 100 MB (w bajtach 104,857,600), a maksymalna długość adresu URL to 4 KB (4096 bajtów). Te limity są określone przez `httpRuntime` elementu w środowisku uruchomieniowym [pliku Web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config).

Jeśli funkcja, która korzysta z wyzwalacza HTTP nie zakończyła się w ciągu około 2,5 minut, limit czasu bramy i zwróci błąd HTTP 502. Funkcja będzie nadal działać, ale nie będzie można zwrócić odpowiedź HTTP. W przypadku długo działających funkcji zaleca się, postępuj zgodnie z wzorców asynchronicznych i zwrócenie lokalizacji, w którym można zbadać poleceniem ping stan żądania. Aby uzyskać informacje o ile można uruchomić funkcję, zobacz [skalowanie i hosting - plan zużycie](functions-scale.md#consumption-plan).

## <a name="trigger---hostjson-properties"></a>Wyzwalacz — właściwości host.json

[Host.json](functions-host-json.md) plik zawiera ustawienia, które kontrolują zachowanie wyzwalacza HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Dane wyjściowe

Użyj protokołu HTTP powiązania danych wyjściowych usługi na odpowiedź do nadawcy żądania HTTP. To powiązanie wymaga wyzwalacz HTTP i pozwala na dostosowanie odpowiedzi skojarzony z żądaniem wyzwalacza. Jeśli powiązanie danych wyjściowych HTTP nie zostanie podana, wyzwalacz HTTP zwraca HTTP 200 OK o pustej treści w funkcjach 1.x lub HTTP 204 Brak zawartości z pustą treść w funkcjach 2.x.

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku. Dla, bibliotek klas języka C# nie ma żadnych właściwości atrybutów, które odnoszą się do tych *function.json* właściwości.

|Właściwość  |Opis  |
|---------|---------|
| **type** |Musi być równa `http`. |
| **direction** | Musi być równa `out`. |
|**Nazwa** | Nazwa zmiennej użytą w kodzie funkcji w przypadku odpowiedzi, lub `$return` do Użyj wartości zwracanej. |

## <a name="output---usage"></a>Dane wyjściowe — użycie

Aby wysłać odpowiedź HTTP, należy użyć wzorców odpowiedzi standard języka. W języku C# lub skrypt języka C#, należy wprowadzić funkcję zwracany typ `HttpResponseMessage` lub `Task<HttpResponseMessage>`. W języku C# atrybut zwracana wartość nie jest wymagana.

Na przykład odpowiedzi, zobacz [przykładowy wyzwalacz](#trigger---example).

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
