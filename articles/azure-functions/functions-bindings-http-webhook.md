---
title: Azure powiązania HTTP funkcje i elementu webhook
description: Zrozumienie, jak używać protokołu HTTP i elementu webhook wyzwalaczy i powiązań w usługi Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: funkcje platformy Azure, funkcje, zdarzenia przetwarzania elementów webhook, dynamicznych zasobów obliczeniowych, API niekorzystającą architektury, protokołu HTTP, REST
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: tdykstra
ms.openlocfilehash: c85af21a40a1b110ae2088fdf6fc8077ec919039
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724629"
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Azure powiązania HTTP funkcje i elementu webhook

W tym artykule opisano sposób pracy z powiązania HTTP usługi Azure Functions. Azure wyzwalaczy HTTP obsługuje funkcje i powiązania danych wyjściowych.

Wyzwalacz HTTP można dostosować, aby odpowiadać na [elementów webhook](https://en.wikipedia.org/wiki/Webhook). Wyzwalacz webhook akceptuje tylko ładunek JSON i sprawdza poprawność kodu JSON. Dostępne są specjalne wersje wyzwalacza elementu webhook ułatwiające obsługi elementów webhook niektórych producentów, takie jak GitHub i Slack.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania HTTP znajdują się w [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) pakietu NuGet, wersja 1.x. Kod źródłowy dla pakietu jest w [azure-zadań webjob sdk rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania HTTP znajdują się w [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) pakietu NuGet w wersji 3.x. Kod źródłowy dla pakietu jest w [azure-zadań webjob sdk rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz protokołu HTTP umożliwia wywołanie funkcji z żądania HTTP. Wyzwalacz protokołu HTTP umożliwia tworzenie niekorzystającą interfejsów API i reagowanie na elementów webhook. 

Domyślnie wyzwalacza HTTP zwraca HTTP 200 OK o pustej treści w funkcjach 1.x lub HTTP 204 nr zawartości o pustej treści w funkcjach 2.x. Aby zmodyfikować odpowiedzi, należy skonfigurować [powiązania wyjściowego HTTP](#http-output-binding).

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) która szuka `name` parametru w ciągu zapytania lub w treści żądania HTTP. Należy zauważyć, że wartość zwracana jest używane do wiązania danych wyjściowych, ale atrybut wartości zwracanej nie jest wymagane.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

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

W poniższym przykładzie przedstawiono powiązanie wyzwalacza w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub w treści żądania HTTP.

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

Oto C# kodu skryptu, który jest powiązany z `HttpRequestMessage`:

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

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

Można powiązać z niestandardowego obiektu zamiast `HttpRequestMessage`. Ten obiekt jest tworzony z treści żądania jako JSON. Podobnie typu mogą być przekazywane do odpowiedzi HTTP, dane wyjściowe powiązania i zwrócony jako treść odpowiedzi, wraz z kodem stanu 200.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
```

### <a name="trigger---f-example"></a>Wyzwalacz — przykład F #

W poniższym przykładzie przedstawiono powiązanie wyzwalacza w *function.json* pliku i [F # funkcja](functions-reference-fsharp.md) używającą powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub w treści żądania HTTP.

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

Oto kod F #:

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

Należy `project.json` pliku, który używa NuGet, aby odwołać `FSharp.Interop.Dynamic` i `Dynamitey` zestawy, jak pokazano w poniższym przykładzie:

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

W poniższym przykładzie przedstawiono powiązanie wyzwalacza w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub w treści żądania HTTP.

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

Oto kod JavaScript:

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
     
## <a name="trigger---webhook-example"></a>Wyzwalacz — przykład elementu webhook

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#webhook---c-example)
* [Skryptu C# (csx)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Element Webhook — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) wysyłają w odpowiedzi na żądanie ogólnego JSON 200 protokołu HTTP.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Element Webhook — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono wyzwalacz elementu webhook powiązanie w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja rejestruje komentarze problem GitHub.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "github",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Oto kod skryptu C#:

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### <a name="webhook---f-example"></a>Element Webhook — przykład F #

W poniższym przykładzie przedstawiono wyzwalacz elementu webhook powiązanie w *function.json* pliku i [F # funkcja](functions-reference-fsharp.md) używającą powiązania. Funkcja rejestruje komentarze problem GitHub.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "github",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Oto kod F #:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

### <a name="webhook---javascript-example"></a>Element Webhook — przykład JavaScript

W poniższym przykładzie przedstawiono wyzwalacz elementu webhook powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja rejestruje komentarze problem GitHub.

W tym miejscu jest powiązanie danych *function.json* pliku:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "github",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) atrybutu.

Można ustawić autoryzacja poziomu i dopuszczalnych metod HTTP w parametrach konstruktora atrybut, a nie ma właściwości dla elementu webhook szablonu typu i trasy. Aby uzyskać więcej informacji o tych ustawieniach, zobacz [wyzwalacza - konfiguracji](#trigger---configuration). Oto `HttpTrigger` atrybutu w podpisie metody:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

Pełny przykład, zobacz [wyzwalacza — przykład C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `HttpTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **type** | Nie dotyczy| Wymagana — musi być ustawiona `httpTrigger`. |
| **direction** | Nie dotyczy| Wymagana — musi być ustawiona `in`. |
| **Nazwa** | Nie dotyczy| Wymagana — nazwa zmiennej używane w kodzie funkcji żądania lub treści żądania. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Określa, jakie klucze, jeśli taki występuje, musi być obecny na żądanie, aby można było wywołać funkcję. Poziom dostępu może być jedną z następujących wartości: <ul><li><code>anonymous</code>&mdash;Brak klucza interfejsu API jest wymagana.</li><li><code>function</code>&mdash;Wymagany jest klucz interfejsu API właściwe dla funkcji. Jeśli nie zostanie podana jest wartość domyślna.</li><li><code>admin</code>&mdash;Klucz główny jest wymagany.</li></ul> Aby uzyskać więcej informacji, zobacz sekcję [klucze autoryzacji](#authorization-keys). |
| **Metody** |**Metody** | Tablica metod HTTP, na które odpowiada funkcji. Jeśli nie zostanie określony, funkcja odpowiada na wszystkich metod HTTP. Zobacz [dostosować punkt końcowy http](#customize-the-http-endpoint). |
| **Trasy** | **Trasy** | Określa szablon trasy, kontrolowanie, do której żądanie odpowiada funkcji adresów URL. Jeśli nie zostanie podana wartość domyślna to `<functionname>`. Aby uzyskać więcej informacji, zobacz [dostosować punkt końcowy http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** |Konfiguruje wyzwalacz protokołu HTTP do działania jako [webhook](https://en.wikipedia.org/wiki/Webhook) odbiornika dla podanego dostawcy. Nie należy ustawiać `methods` właściwości, jeśli ta właściwość jest ustawiona. Typ elementu webhook może być jedną z następujących wartości:<ul><li><code>genericJson</code>&mdash;Punkt końcowy elementu webhook ogólnego przeznaczenia bez logiki dla określonego dostawcy. To ustawienie ogranicza żądania tylko do tych przy użyciu protokołu HTTP POST i z `application/json` typ zawartości.</li><li><code>github</code>&mdash;Funkcja odpowiada [elementów webhook GitHub](https://developer.github.com/webhooks/). Nie używaj _authLevel_ właściwości z elementów webhook GitHub. Aby uzyskać więcej informacji zobacz sekcję elementów webhook GitHub w dalszej części tego artykułu.</li><li><code>slack</code>&mdash;Funkcja odpowiada [Slack elementów webhook](https://api.slack.com/outgoing-webhooks). Nie używaj _authLevel_ właściwości z elementów webhook Slack. Aby uzyskać więcej informacji zobacz sekcję Slack elementów webhook w dalszej części tego artykułu.</li></ul>|

## <a name="trigger---usage"></a>Wyzwalacz — użycie

Dla funkcji języka C# i F #, mogą zadeklarować typ danych wejściowych musi mieć przypisany wyzwalacz `HttpRequestMessage` lub typu niestandardowego. Jeśli wybierzesz `HttpRequestMessage`, możesz uzyskać dostęp do obiektu żądania. W przypadku typu niestandardowego funkcje próbuje przeanalizować treść żądania JSON można ustawić właściwości obiektu. 

W przypadku funkcji JavaScript środowisko uruchomieniowe Functions zapewnia treści żądania, zamiast obiektu żądania. Aby uzyskać więcej informacji, zobacz [przykład wyzwalacza JavaScript](#trigger---javascript-example).

### <a name="github-webhooks"></a>Elementów webhook GitHub

Aby odpowiedzieć elementów webhook GitHub, najpierw utwórz funkcji z wyzwalaczem HTTP i ustaw **webHookType** właściwości `github`. Następnie skopiuj jej adres URL i klucz API w **Dodawanie elementu webhook** strony repozytorium GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Aby zapoznać się z przykładem, zobacz [Tworzenie funkcji wyzwalanej przez element webhook GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="slack-webhooks"></a>Slack elementów webhook

Slack elementu webhook generuje token dla Ciebie, zamiast czekać na określeniu, dlatego należy skonfigurować klucz właściwe dla funkcji przy użyciu tokenu z zapas czasu. Zobacz [klucze autoryzacji](#authorization-keys).

### <a name="customize-the-http-endpoint"></a>Dostosowywanie punkt końcowy HTTP

Domyślnie podczas tworzenia funkcja wyzwalacza HTTP lub elementu WebHook, funkcja jest adresowanego trasa formularza:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Można dostosować tej trasy przy użyciu opcjonalnego `route` wejściowych powiązania właściwości wyzwalacza HTTP. Na przykład następujące *function.json* plik definiuje `route` właściwości dla wyzwalacza HTTP:

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

Za pomocą tej konfiguracji, funkcja jest teraz adresowanego trasa następujących zamiast oryginalnej trasy.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Dzięki temu kod funkcji do obsługi dwóch parametrów w adresie _kategorii_ i _identyfikator_. Można użyć dowolnego [ograniczenia trasy interfejsu API sieci Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) z parametrami. Poniższy kod funkcji języka C# korzysta z obu tych parametrów.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Oto kod funkcja Node.js, który używa tych samych parametrach trasy.

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

Domyślnie wszystkie trasy funkcji są poprzedzane prefiksem *interfejsu api*. Można również dostosować lub usunąć przy użyciu prefiksu `http.routePrefix` właściwości w Twojej [host.json](functions-host-json.md) pliku. Poniższy przykład umożliwia usunięcie *interfejsu api* prefiks trasy przy użyciu pustego ciągu dla prefiksu w *host.json* pliku.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>Klucze autoryzacji

Wyzwalacze HTTP pozwalają używać kluczy w celu zwiększenia bezpieczeństwa. Standardowa wyzwalacza HTTP można użyć je jako klucz interfejsu API wymagające klucza, który ma być obecne w żądaniu. Elementów Webhook można używać kluczy do autoryzowania żądań na różne sposoby, w zależności od tego, czy dostawca obsługuje.

> [!NOTE]
> Podczas uruchamiania lokalnego funkcje, autoryzacji jest wyłączone niezależnie `authLevel` w `function.json`. Jak opublikować do usługi Azure Functions `authLevel` natychmiast obowiązuje.

Klucze są przechowywane w ramach funkcji aplikacji na platformie Azure i są szyfrowane, gdy. Aby wyświetlić klucze, tworzenie nowych, lub Przywróć do nowych wartości kluczy, przejdź do jednej z funkcji w portalu i wybierz "Manage". 

Istnieją dwa typy kluczy:

- **Klucze hosta**: klucze te są współużytkowane przez wszystkie funkcje w ramach funkcji aplikacji. Gdy jest używany jako klucz interfejsu API, umożliwiają one dostęp do dowolnej funkcji w funkcji aplikacji.
- **Klawisze funkcyjne**: te klucze mają zastosowanie tylko do określonych funkcji, w których są zdefiniowane. Gdy jest używany jako klucz interfejsu API, te tylko zezwolić na dostęp do tej funkcji.

Każdy klucz nosi nazwę odwołania, a jest domyślny klucz (o nazwie "domyślne") na poziomie funkcji i hosta. Klawisze funkcyjne mają pierwszeństwo przed klucze hosta. Po zdefiniowaniu dwa klucze o takiej samej nazwie jest zawsze używana klawisze funkcyjne.

**Klucza głównego** jest domyślny klucz hosta o nazwie "_master", który jest zdefiniowany dla każdej funkcji aplikacji. Ten klucz nie może zostać odwołany. Zapewnia dostęp administracyjny do interfejsów API środowiska wykonawczego. Przy użyciu `"authLevel": "admin"` w powiązaniu JSON wymaga tego klucza, aby być prezentowana na żądanie; innego klucza powoduje błąd autoryzacji.

> [!IMPORTANT]  
> Z powodu podwyższonym poziomem uprawnień przyznanych przez klucz główny nie należy udostępnić ten klucz osobom trzecim ani rozpowszechnienie go w aplikacjach klienckich natywnego. Należy zachować ostrożność, wybierając poziom dostępu administratora.

### <a name="api-key-authorization"></a>Autoryzacji klucza interfejsu API

Domyślnie wyzwalacza HTTP wymaga klucza interfejsu API w żądaniu HTTP. Dlatego żądanie HTTP zwykle wygląda następująco:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

Klucz może być uwzględniony w zmiennej ciągu zapytania o nazwie `code`, zgodnie z powyższym, lub można je uwzględnić w `x-functions-key` nagłówka HTTP. Wartość klucza może być dowolny klawisz funkcji zdefiniowany dla funkcji lub dowolnego klucz hosta.

Można zezwolić na anonimowe żądania, które nie wymagają kluczy. Możesz również wymagać użytą klucza głównego. Zmiany domyślnego poziomu autoryzacji przy użyciu `authLevel` właściwości w powiązaniu JSON. Aby uzyskać więcej informacji, zobacz [wyzwalacza - konfiguracji](#trigger---configuration).

### <a name="keys-and-webhooks"></a>Klucze i elementów webhook

Autoryzacji elementu Webhook jest obsługiwany przez element webhook składnika odbiornika, część wyzwalacza HTTP i mechanizmu w zależności od typu elementu webhook. Nie mechanizmu, jednak zależne od klucza. Domyślnie jest używana funkcja klucz o nazwie "domyślne". Aby użyć innego klucza, należy skonfigurować dostawcę elementu webhook można wysłać nazwę klucza z żądaniem w jednym z następujących sposobów:

- **Długość ciągu zapytania**: dostawca przekazuje nazwę klucza w `clientid` parametr ciągu, zapytania, takie jak `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Nagłówek żądania**: dostawca przekazuje nazwę klucza w `x-functions-clientid` nagłówka.

## <a name="trigger---limits"></a>Wyzwalacz - limity

Długość żądania HTTP jest ograniczona do 100MB (w bajtach 104,857,600), a długość adresu URL jest ograniczony do 4KB (4096 bajtów). Ograniczenia te są określane przez `httpRuntime` elementu środowiska uruchomieniowego [pliku Web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config).

Jeśli funkcja, która używa wyzwalacza HTTP nie zakończyła się w ciągu około 2,5 minut, upływu limitu czasu bramy spowoduje i zwróci błąd HTTP 502. Funkcja będzie nadal działać, ale nie będzie można zwracać odpowiedzi HTTP. Do funkcji długotrwałe zaleca się, wykonaj wzorców asynchronicznych i zwrócenie lokalizacji, w którym można zbadać poleceniem ping stan żądania. Aby uzyskać informacje, jak długo można uruchomić funkcję, zobacz [skali i hostingu — planowanie zużycie](functions-scale.md#consumption-plan). 

## <a name="trigger---hostjson-properties"></a>Wyzwalacz - host.json właściwości

[Host.json](functions-host-json.md) plik zawiera ustawienia, które kontrolują zachowanie wyzwalacza HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Dane wyjściowe

Za pomocą raportu HTTP powiązanie odpowiedź do nadawcy żądania HTTP. To powiązanie wymaga wyzwalacza HTTP i umożliwia dostosowanie odpowiedzi skojarzony z żądaniem tego wyzwalacza. Jeśli powiązania wyjściowego HTTP nie zostanie podany, wyzwalacz HTTP zwraca HTTP 200 OK o pustej treści w funkcjach 1.x lub HTTP 204 nr zawartości o pustej treści w funkcjach 2.x.

## <a name="output---configuration"></a>OUTPUT — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku. Dla klasy C# Brak bibliotek nie ma żadnych atrybutów właściwości odpowiadające te *function.json* właściwości. 

|Właściwość  |Opis  |
|---------|---------|
| **type** |należy wybrać opcję `http`. |
| **direction** | należy wybrać opcję `out`. |
|**Nazwa** | Nazwa zmiennej używane w kodzie funkcja odpowiedzi, lub `$return` do Użyj wartości zwracanej. |

## <a name="output---usage"></a>Dane wyjściowe — użycie

Aby wysłać odpowiedzi HTTP, należy użyć wzorców standard języka odpowiedzi. W języku C# lub skryptu C#, wprowadź typ zwracany funkcji `HttpResponseMessage` lub `Task<HttpResponseMessage>`. W języku C# Atrybut wartości zwracanej nie jest wymagane.

Na przykład odpowiedzi, zobacz [przykład wyzwalacza](#trigger---example) i [przykład elementu webhook](#trigger---webhook-example).

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)
