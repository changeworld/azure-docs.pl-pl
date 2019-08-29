---
title: Azure Functions wyzwalacze i powiązania HTTP
description: Dowiedz się, jak używać wyzwalaczy i powiązań HTTP w Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, Functions, przetwarzanie zdarzeń, webhooks, Dynamic COMPUTE, architektura bezserwerowa, HTTP, API, REST
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 983cf250f3a7188741c41386aac256bfdb28749b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097342"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Azure Functions wyzwalacze i powiązania HTTP

W tym artykule opisano sposób pracy z wyzwalaczami HTTP i powiązaniami wyjściowymi w Azure Functions.

Wyzwalacz HTTP można dostosować w taki sposób, aby [](https://en.wikipedia.org/wiki/Webhook)odpowiadał na elementy webhook.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

Kod w tym artykule domyślnie przyjmuje składnię Functions 2. x, która używa platformy .NET Core. Informacje na temat składni 1. x można znaleźć w [szablonach funkcji 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania HTTP są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) w wersji 1. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania HTTP są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) w wersji 3. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz HTTP umożliwia wywoływanie funkcji za pomocą żądania HTTP. Można użyć wyzwalacza HTTP do kompilowania bezserwerowych interfejsów API i reagowania na elementy webhook.

Domyślnie wyzwalacz HTTP zwraca wartość HTTP 200 OK z pustą treścią w funkcjach 1. x lub HTTP 204 Brak zawartości z pustą treścią w funkcjach 2. x. Aby zmodyfikować odpowiedź, skonfiguruj [powiązanie danych wyjściowych http](#output).

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-examples)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która `name` szuka parametru w ciągu zapytania lub treści żądania HTTP. Zwróć uwagę, że wartość zwracana jest używana dla powiązania danych wyjściowych, ale atrybut wartości zwracanej nie jest wymagany.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

Poniższy przykład pokazuje powiązanie wyzwalacza w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub treści żądania HTTP.

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

Kod skryptu C# , który jest powiązany z `HttpRequest`:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Można powiązać z obiektem niestandardowym, `HttpRequest`a nie. Ten obiekt jest tworzony na podstawie treści żądania i przeanalizowany jako kod JSON. Analogicznie, typ może być przekazywane do powiązania danych wyjściowych odpowiedzi HTTP i zwracany jako treść odpowiedzi wraz z kodem stanu 200.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

### <a name="trigger---f-example"></a>Wyzwalacz - F# przykład

Poniższy przykład pokazuje powiązanie wyzwalacza w pliku *Function. JSON* i [ F# funkcję](functions-reference-fsharp.md) , która używa powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub treści żądania HTTP.

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

Potrzebny `project.json` jest plik, który używa narzędzia NuGet do odwoływania `Dynamitey` się `FSharp.Interop.Dynamic` do zestawów i, jak pokazano w następującym przykładzie:

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

W poniższym przykładzie pokazano powiązanie wyzwalacza w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub treści żądania HTTP.

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

### <a name="trigger---python-example"></a>Wyzwalacz — przykład w języku Python

Poniższy przykład pokazuje powiązanie wyzwalacza w pliku *Function. JSON* i [funkcji języka Python](functions-reference-python.md) , która używa powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub treści żądania HTTP.

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

Oto kod języka Python:

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

### <a name="trigger---java-examples"></a>Wyzwalacz — przykłady dla języka Java

* [Odczytaj parametr z ciągu zapytania](#read-parameter-from-the-query-string-java)
* [Odczytaj treść z żądania POST](#read-body-from-a-post-request-java)
* [Odczyt parametru z trasy](#read-parameter-from-a-route-java)
* [Odczytaj treść POJO z żądania POST](#read-pojo-body-from-a-post-request-java)

W poniższych przykładach pokazano powiązanie wyzwalacza HTTP w pliku *Function. JSON* oraz odpowiednie [funkcje języka Java](functions-reference-java.md) , które używają tego powiązania. 

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

#### <a name="read-parameter-from-the-query-string-java"></a>Odczytaj parametr z ciągu zapytania (Java)  

Ten przykład odczytuje parametr o nazwie ```id```, z ciągu zapytania i używa go do skompilowania dokumentu JSON zwróconego do klienta z typem ```application/json```zawartości. 

```java
    @FunctionName("TriggerStringGet")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("GET parameters are: " + request.getQueryParameters());

        // Get named parameter
        String id = request.getQueryParameters().getOrDefault("id", "");

        // Convert and display
        if (id.isEmpty()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String name = "fake_name";
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-body-from-a-post-request-java"></a>Odczytaj treść z żądania POST (Java)  

Ten przykład odczytuje treść żądania post jako ```String```i używa go do skompilowania dokumentu JSON zwróconego do klienta z typem ```application/json```zawartości.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route-java"></a>Odczyt parametru z trasy (Java)  

Ten przykład odczytuje obowiązkowy parametr o nazwie ```id```i opcjonalny parametr ```name``` ze ścieżki trasy, a następnie używa ich do kompilowania dokumentu JSON zwróconego do klienta z typem ```application/json```zawartości. T

```java
    @FunctionName("TriggerStringRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
            HttpRequestMessage<Optional<String>> request,
            @BindingName("id") String id,
            @BindingName("name") String name,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Route parameters are: " + id);

        // Convert and display
        if (id == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-pojo-body-from-a-post-request-java"></a>Odczytaj treść POJO z żądania POST (Java)  

Oto kod ```ToDoItem``` klasy, do której odwołuje się w tym przykładzie:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Ten przykład odczytuje treść żądania POST. Treść żądania jest automatycznie deserializowana do ```ToDoItem``` obiektu i jest zwracana do klienta z typem ```application/json```zawartości. Parametr jest serializowany przez środowisko uruchomieniowe funkcji w miarę ich przypisywania ```body``` do właściwości ```HttpMessageResponse.Builder``` klasy. ```ToDoItem```

```java
    @FunctionName("TriggerPojoPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<ToDoItem>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(null));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final ToDoItem body = request.getBody().get();
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(body)
                          .build();
        }
    }
```

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

Można ustawić poziom autoryzacji i dozwolone metody HTTP w parametrach konstruktora atrybutów, a istnieją właściwości typu elementu webhook i szablonu trasy. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [wyzwalacz-konfiguracja](#trigger---configuration). `HttpTrigger` Oto atrybut w sygnaturze metody:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Aby uzyskać kompletny przykład, zobacz [wyzwalacza — przykład w języku C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `HttpTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **type** | Nie dotyczy| Wymagane — musi być ustawiony na `httpTrigger`wartość. |
| **direction** | Nie dotyczy| Wymagane — musi być ustawiony na `in`wartość. |
| **name** | Nie dotyczy| Wymagane — nazwa zmiennej używana w kodzie funkcji dla żądania lub treści żądania. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Określa, jakie klucze (jeśli istnieją) muszą być obecne w żądaniu w celu wywołania funkcji. Poziom autoryzacji może być jedną z następujących wartości: <ul><li><code>anonymous</code>&mdash;Nie jest wymagany żaden klucz interfejsu API.</li><li><code>function</code>&mdash;Wymagany jest klucz interfejsu API specyficzny dla funkcji. Jest to wartość domyślna, jeśli nie podano żadnej z nich.</li><li><code>admin</code>&mdash;Klucz główny jest wymagany.</li></ul> Aby uzyskać więcej informacji, zapoznaj się z sekcją dotyczącej [kluczy autoryzacji](#authorization-keys). |
| **methods** |**Methods** | Tablica metod HTTP, do których funkcja reaguje. Jeśli nie zostanie określony, funkcja reaguje na wszystkie metody HTTP. Zobacz [Dostosowywanie punktu końcowego http](#customize-the-http-endpoint). |
| **route** | **Route** | Definiuje szablon trasy, który kontroluje adresy URL żądań, które odpowiada funkcja. Wartość domyślna, jeśli nie jest podano `<functionname>`. Aby uzyskać więcej informacji, zobacz [Dostosowywanie punktu końcowego http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Obsługiwane tylko dla środowiska uruchomieniowego w wersji 1. x._<br/><br/>Konfiguruje wyzwalacz HTTP do działania jako odbiornik [elementu webhook](https://en.wikipedia.org/wiki/Webhook) dla określonego dostawcy. Nie ustawiaj `methods` właściwości w przypadku ustawienia tej właściwości. Typ elementu webhook może być jedną z następujących wartości:<ul><li><code>genericJson</code>&mdash;Punkt końcowy elementu webhook ogólnego przeznaczenia bez logiki dla określonego dostawcy. To ustawienie ogranicza żądania tylko do tych, które używają protokołu HTTP POST i `application/json` z typem zawartości.</li><li><code>github</code>&mdash;Funkcja reaguje na elementy [webhook usługi GitHub](https://developer.github.com/webhooks/). Nie należy używać właściwości _authLevel_ z elementami webhook usługi GitHub. Aby uzyskać więcej informacji, zobacz sekcję elementy webhook w usłudze GitHub w dalszej części tego artykułu.</li><li><code>slack</code>&mdash;Funkcja reaguje na elementy [webhook zapasowych](https://api.slack.com/outgoing-webhooks). Nie należy używać właściwości _authLevel_ z elementami webhook zapasowych. Aby uzyskać więcej informacji, zobacz sekcję elementy webhooks w dalszej części tego artykułu.</li></ul>|

## <a name="trigger---usage"></a>Wyzwalacz — użycie

Dla C# funkcji F# i można zadeklarować typ danych wejściowych `HttpRequest` wyzwalacza jako lub typ niestandardowy. W przypadku wybrania tej opcji `HttpRequest`uzyskasz pełny dostęp do obiektu żądania. W przypadku typu niestandardowego środowisko uruchomieniowe próbuje przeanalizować treści żądania JSON w celu ustawienia właściwości obiektu.

W przypadku funkcji JavaScript środowisko uruchomieniowe Functions dostarcza treści żądania zamiast obiektu żądania. Aby uzyskać więcej informacji, zobacz [przykład wyzwalacza JavaScript](#trigger---javascript-example).

### <a name="customize-the-http-endpoint"></a>Dostosowywanie punktu końcowego HTTP

Domyślnie podczas tworzenia funkcji dla wyzwalacza HTTP funkcja jest adresowana z trasą w postaci:

    http://<yourapp>.azurewebsites.net/api/<funcname>

Można dostosować tę trasę przy użyciu właściwości opcjonalnej `route` w powiązaniu danych wejściowych wyzwalacza http. Przykładowo następujący plik *Function. JSON* definiuje `route` Właściwość wyzwalacza http:

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

Korzystając z tej konfiguracji, funkcja jest teraz adresowana do następującej trasy zamiast oryginalnej trasy.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Pozwala to kodowi funkcji obsługiwać dwa parametry w adresie, _kategorii_ i _identyfikatorze_. Możesz użyć dowolnego [ograniczenia trasy interfejsu API sieci Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) z parametrami. Poniższy C# kod funkcji wykorzystuje oba parametry.

```csharp
public static Task<IActionResult> Run(HttpRequest req, string category, int? id, ILogger log)
{
    if (id == null)
    {
        return (ActionResult)new OkObjectResult($"All {category} items were requested.");
    }
    else
    {
        return (ActionResult)new OkObjectResult($"{category} item with id = {id} has been requested.");
    }
    
    // -----
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
}
```

Oto kod funkcji środowiska Node. js, który używa tych samych parametrów trasy.

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

Domyślnie wszystkie trasy funkcji są poprzedzone *interfejsem API*. Można również dostosować lub usunąć prefiks przy użyciu `http.routePrefix` właściwości w pliku [host. JSON](functions-host-json.md) . Poniższy przykład usuwa prefiks trasy *interfejsu API* za pomocą pustego ciągu dla prefiksu w pliku *host. JSON* .

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>Praca z tożsamościami klientów

Jeśli aplikacja funkcji używa [App Service uwierzytelniania/autoryzacji](../app-service/overview-authentication-authorization.md), można wyświetlić informacje o uwierzytelnionych klientach w kodzie. Te informacje są dostępne jako [nagłówki żądań wstrzykiwane przez platformę](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Te informacje można również odczytać z poziomu powiązań danych. Ta funkcja jest dostępna tylko dla środowiska uruchomieniowego Functions 2. x. Jest on również obecnie dostępny tylko dla języków .NET.

W językach .NET te informacje są dostępne jako [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal jest dostępny jako część kontekstu żądania, jak pokazano w następującym przykładzie:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternatywnie, ClaimsPrincipal można po prostu uwzględnić jako dodatkowy parametr w sygnaturze funkcji:

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

Funkcje umożliwiają korzystanie z kluczy, aby utrudnić dostęp do punktów końcowych funkcji HTTP podczas opracowywania.  Standardowy wyzwalacz HTTP może wymagać takiego klucza interfejsu API w żądaniu. 

> [!IMPORTANT]
> Chociaż klucze mogą pomóc w zablokowaniu punktów końcowych HTTP podczas opracowywania, nie są one przeznaczone do zabezpieczania wyzwalacza HTTP w środowisku produkcyjnym. Aby dowiedzieć się więcej, zobacz temat [Zabezpieczanie punktu końcowego HTTP w środowisku produkcyjnym](#secure-an-http-endpoint-in-production).

> [!NOTE]
> W środowisku uruchomieniowym Functions 1. x dostawcy elementów webhook mogą używać kluczy do autoryzowania żądań na różne sposoby, w zależności od tego, co obsługuje dostawca. Dotyczy to elementów [webhook i kluczy](#webhooks-and-keys). Środowisko uruchomieniowe w wersji 2. x nie obejmuje wbudowanej obsługi dostawców elementu webhook.

Istnieją dwa typy kluczy:

* **Klucze hosta**: Te klucze są udostępniane przez wszystkie funkcje w aplikacji funkcji. Gdy jest używany jako klucz interfejsu API, zezwalają na dostęp do dowolnej funkcji w aplikacji funkcji.
* **Klawisze funkcyjne**: Te klucze dotyczą tylko określonych funkcji, w których są zdefiniowane. Gdy jest używany jako klucz interfejsu API, zezwala na dostęp tylko do tej funkcji.

Każdy klucz ma nazwę dla odwołania i istnieje klucz domyślny (o nazwie "domyślny") na poziomie funkcji i hosta. Klucze funkcji mają pierwszeństwo przed Kluczami hosta. Gdy dwa klucze są zdefiniowane z tą samą nazwą, klucz funkcji jest zawsze używany.

Każda aplikacja funkcji ma również specjalny **klucz główny**. Ten klucz jest kluczem hosta o nazwie `_master`, który zapewnia dostęp administracyjny do interfejsów API środowiska uruchomieniowego. Nie można odwołać tego klucza. W przypadku ustawienia poziomu autoryzacji programu `admin`żądania muszą używać klucza głównego; wszystkie inne kluczowe wyniki w przypadku niepowodzenia autoryzacji.

> [!CAUTION]  
> Ze względu na podwyższony poziom uprawnień w aplikacji funkcji udzielanej przez klucz główny nie należy udostępniać tego klucza innym podmiotom lub rozpowszechniać go w natywnych aplikacjach klienckich. Należy zachować ostrożność podczas wybierania poziomu autoryzacji administratora.

### <a name="obtaining-keys"></a>Uzyskiwanie kluczy

Klucze są przechowywane jako część aplikacji funkcji na platformie Azure i są szyfrowane w stanie spoczynku. Aby wyświetlić klucze, utworzyć nowe lub przerzucić klucze do nowych wartości, przejdź do jednej z funkcji wyzwalanych przez protokół HTTP w [Azure Portal](https://portal.azure.com) i wybierz pozycję **Zarządzaj**.

![Zarządzaj kluczami funkcji w portalu.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Można programowo uzyskać klucze funkcji za pomocą [interfejsu API zarządzania kluczami](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

### <a name="api-key-authorization"></a>Autoryzacja klucza interfejsu API

Większość szablonów wyzwalacza HTTP wymaga klucza interfejsu API w żądaniu. Dlatego żądanie HTTP zwykle wygląda następująco:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Klucz może być uwzględniony w zmiennej ciągu zapytania o nazwie `code`, jak powyżej. Może być również uwzględniony w `x-functions-key` nagłówku HTTP. Wartość klucza może być dowolnym kluczem funkcji zdefiniowanym dla funkcji lub dowolnym kluczem hosta.

Można zezwolić na żądania anonimowe, które nie wymagają kluczy. Możesz również wymagać użycia klucza głównego. Domyślny poziom autoryzacji można zmienić przy użyciu `authLevel` właściwości w kodzie JSON powiązania. Aby uzyskać więcej informacji, zobacz [wyzwalacz-konfiguracja](#trigger---configuration).

> [!NOTE]
> Podczas lokalnego uruchamiania funkcji Autoryzacja jest wyłączona niezależnie od ustawienia określonego poziomu uwierzytelniania. Po opublikowaniu na platformie Azure `authLevel` wymuszane jest ustawienie w wyzwalaczu.



### <a name="secure-an-http-endpoint-in-production"></a>Zabezpieczanie punktu końcowego HTTP w środowisku produkcyjnym

Aby w pełni zabezpieczyć punkty końcowe funkcji w środowisku produkcyjnym, należy rozważyć implementację jednej z następujących opcji zabezpieczeń na poziomie aplikacji:

* Włącz App Service uwierzytelnianie/autoryzację dla aplikacji funkcji. Platforma App Service umożliwia korzystanie z usługi Azure Active Directory (AAD) i wielu dostawców tożsamości innych firm do uwierzytelniania klientów. Służy do implementowania niestandardowych reguł autoryzacji dla funkcji i można korzystać z informacji o użytkowniku z kodu funkcji. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie i autoryzacja w Azure App Service](../app-service/overview-authentication-authorization.md) i [Praca z tożsamościami klientów](#working-with-client-identities).

* Uwierzytelnianie żądań odbywa się przy użyciu usługi Azure API Management (APIM). APIM zapewnia różne opcje zabezpieczeń interfejsu API dla żądań przychodzących. Aby dowiedzieć się więcej, zobacz [API Management zasady uwierzytelniania](../api-management/api-management-authentication-policies.md). Mając APIM na miejscu, można skonfigurować aplikację funkcji do akceptowania żądań tylko z adresu IP wystąpienia usługi APIM. Aby dowiedzieć się więcej, zobacz temat [Ograniczenia adresów IP](ip-addresses.md#ip-address-restrictions).

* Wdróż aplikację funkcji w Azure App Service Environment (ASE). Środowisko ASE zapewnia dedykowane środowiska hostingu, w ramach którego można uruchamiać funkcje. Środowisko ASE umożliwia skonfigurowanie pojedynczej bramy frontonu, za pomocą której można uwierzytelniać wszystkie żądania przychodzące. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapory aplikacji sieci Web (WAF) dla App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Korzystając z jednej z tych metod zabezpieczeń na poziomie aplikacji, należy ustawić poziom uwierzytelniania funkcji wyzwalanej przez protokół HTTP na `anonymous`.

### <a name="webhooks"></a>Elementy webhook

> [!NOTE]
> Tryb elementu webhook jest dostępny tylko dla wersji 1. x środowiska uruchomieniowego funkcji. Ta zmiana została wprowadzona w celu zwiększenia wydajności wyzwalaczy HTTP w wersji 2. x.

W wersji 1. x szablony elementów webhook zapewniają dodatkową weryfikację ładunków elementu webhook. W wersji 2. x podstawowy wyzwalacz HTTP nadal działa i jest zalecanym rozwiązaniem dla elementów webhook. 

#### <a name="github-webhooks"></a>Elementy webhook usługi GitHub

Aby odpowiedzieć na elementy webhook usługi GitHub, najpierw Utwórz funkcję z wyzwalaczem HTTP i ustaw właściwość webhooktype na. `github` Następnie skopiuj swój adres URL i klucz interfejsu API na stronę **Dodawanie elementu webhook** w repozytorium GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Elementy webhook zapasu

Element webhook zapasu generuje token dla Ciebie, a nie pozwala na jego określenie, dlatego należy skonfigurować klucz specyficzny dla funkcji z tokenem ze zapasu czasu. Zobacz [klucze autoryzacji](#authorization-keys).

### <a name="webhooks-and-keys"></a>Elementy webhook i klucze

Autoryzacja elementu webhook jest obsługiwana przez składnik odbiornika elementu webhook, część wyzwalacza HTTP, a mechanizm zmienia się w zależności od typu elementu webhook. Każdy mechanizm polega na kluczu. Domyślnie jest używany klucz funkcji o nazwie "default". Aby użyć innego klucza, należy skonfigurować dostawcę elementu webhook w celu wysłania nazwy klucza z żądaniem w jeden z następujących sposobów:

* **Ciąg zapytania**: Dostawca przekazuje nazwę klucza w `clientid` parametrze ciągu zapytania, `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`na przykład.
* **Nagłówek żądania**: Dostawca przekazuje nazwę klucza w `x-functions-clientid` nagłówku.

## <a name="trigger---limits"></a>Wyzwalacze — limity

Długość żądania HTTP jest ograniczona do 100 MB (104 857 600 bajtów), a długość adresu URL jest ograniczona do 4 KB (4 096 bajtów). Limity te są określane przez `httpRuntime` element [pliku Web. config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)środowiska uruchomieniowego.

Jeśli funkcja, która używa wyzwalacza HTTP, nie kończy się w ciągu około 2,5 minut, Brama przekroczy limit czasu i zwróci błąd HTTP 502. Funkcja będzie kontynuowała działanie, ale nie będzie mogła zwracać odpowiedzi HTTP. W przypadku długotrwałych funkcji zalecamy wykonanie wzorców asynchronicznych i zwrócenie lokalizacji, w której można wysłać polecenie ping do stanu żądania. Informacje o tym, jak długo funkcja może być uruchamiana, znajduje się w artykule [skalowanie i planowanie zużycia](functions-scale.md#timeout).

## <a name="trigger---hostjson-properties"></a>Wyzwalacz — właściwości host.json

Plik [host. JSON](functions-host-json.md) zawiera ustawienia kontrolujące zachowanie wyzwalacza http.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Output

Użyj powiązania wyjściowego HTTP, aby odpowiedzieć na nadawcę żądania HTTP. To powiązanie wymaga wyzwalacza HTTP i umożliwia dostosowanie odpowiedzi skojarzonej z żądaniem wyzwalacza. Jeśli nie podano powiązania danych wyjściowych HTTP, wyzwalacz HTTP zwróci wartość HTTP 200 OK z pustą treścią w funkcjach 1. x lub HTTP 204 Brak zawartości z pustą treścią w funkcjach 2. x.

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* . W C# przypadku bibliotek klas nie ma właściwości atrybutów odpowiadających tym właściwościom *funkcji Function. JSON* .

|Właściwość  |Opis  |
|---------|---------|
| **type** |Musi być równa `http`. |
| **direction** | Musi być równa `out`. |
|**name** | Nazwa zmiennej używana w kodzie funkcji dla odpowiedzi lub `$return` do użycia wartości zwracanej. |

## <a name="output---usage"></a>Dane wyjściowe — użycie

Aby wysłać odpowiedź HTTP, użyj standardowych wzorców odpowiedzi języka. W C# programie C# lub, należy sprawić, aby funkcja `IActionResult` zwracała typ lub `Task<IActionResult>`. W C#programie atrybut wartości zwracanej nie jest wymagany.

Na przykład odpowiedzi, zobacz [przykład wyzwalacza](#trigger---example).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
