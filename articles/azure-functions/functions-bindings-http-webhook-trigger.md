---
title: Wyzwalacz HTTP funkcji platformy Azure
description: Dowiedz się, jak wywołać funkcję platformy Azure za pośrednictwem protokołu HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 045f3ccdc8dc09bf657ab39ce15a0d0524c73fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277598"
---
# <a name="azure-functions-http-trigger"></a>Wyzwalacz HTTP funkcji platformy Azure

Wyzwalacz HTTP umożliwia wywołanie funkcji z żądaniem HTTP. Za pomocą wyzwalacza HTTP można tworzyć interfejsy API bez użycia serwera i odpowiadać na elementów webhook.

Domyślna wartość zwracana dla funkcji wyzwalanej przez HTTP to:

- `HTTP 204 No Content`z pustym korpusem w funkcjach 2.x lub nowszych
- `HTTP 200 OK`z pustym korpusem w funkcji 1.x

Aby zmodyfikować odpowiedź HTTP, skonfiguruj [powiązanie danych wyjściowych](./functions-bindings-http-webhook-output.md).

Aby uzyskać więcej informacji na temat powiązań HTTP, zobacz [omówienie](./functions-bindings-http-webhook.md) i [odwołanie do powiązania danych wyjściowych](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie przedstawiono funkcję `name` Języka [C#,](functions-dotnet-class-library.md) która wyszukuje parametr w ciągu zapytania lub w treści żądania HTTP. Należy zauważyć, że zwracana wartość jest używana dla powiązania danych wyjściowych, ale atrybut wartości zwracanej nie jest wymagany.

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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja wyszukuje `name` parametr w ciągu zapytania lub w treści żądania HTTP.

Oto plik *function.json:*

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

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod skryptu C#, `HttpRequest`który wiąże się z:

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

Zamiast obiektu można powiązać obiekt `HttpRequest`niestandardowy. Ten obiekt jest tworzony z treści żądania i analizowane jako JSON. Podobnie typ może być przekazany do powiązania danych wyjściowych odpowiedzi HTTP i `200` zwracany jako treść odpowiedzi, wraz z kodem stanu.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja wyszukuje `name` parametr w ciągu zapytania lub w treści żądania HTTP.

Oto plik *function.json:*

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

W sekcji [konfiguracji](#configuration) opisano te właściwości.

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

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano powiązanie wyzwalacza w pliku *function.json* i [funkcję Języka Python,](functions-reference-python.md) która używa powiązania. Funkcja wyszukuje `name` parametr w ciągu zapytania lub w treści żądania HTTP.

Oto plik *function.json:*

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

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod Pythona:

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

# <a name="java"></a>[Java](#tab/java)

* [Odczyt parametru z ciągu zapytania](#read-parameter-from-the-query-string)
* [Odczyt treści z żądania POST](#read-body-from-a-post-request)
* [Odczyt parametru z trasy](#read-parameter-from-a-route)
* [Odczyt treści POJO z żądania POST](#read-pojo-body-from-a-post-request)

Poniższe przykłady pokazują powiązanie wyzwalacza HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Odczyt parametru z ciągu zapytania

W tym przykładzie `id`odczytuje parametr o nazwie , z ciągu zapytania i używa go do `application/json`tworzenia dokumentu JSON zwróconego do klienta, z typem zawartości .

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

#### <a name="read-body-from-a-post-request"></a>Odczyt treści z żądania POST

W tym przykładzie odczytuje treść `String`żądania POST jako program i używa go do tworzenia dokumentu `application/json`JSON zwróconego klientowi z typem zawartości.

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

#### <a name="read-parameter-from-a-route"></a>Odczyt parametru z trasy

W tym przykładzie odczytuje `id`parametr obowiązkowy `name` o nazwie i opcjonalny z ścieżki trasy i używa ich do `application/json`utworzenia dokumentu JSON zwróconego klientowi z typem zawartości . T

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

#### <a name="read-pojo-body-from-a-post-request"></a>Odczyt treści POJO z żądania POST

Oto kod `ToDoItem` dla klasy, do którego odwołuje się w tym przykładzie:

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

W tym przykładzie odczytuje treść żądania POST. Treść żądania zostanie automatycznie odsealizowana do obiektu i jest zwracana do klienta z typem `ToDoItem` `application/json`zawartości. Parametr `ToDoItem` jest serializowany przez środowisko uruchomieniowe Functions, ponieważ jest przypisany do `body` właściwości `HttpMessageResponse.Builder` klasy.

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

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

W [bibliotekach klas języka C#](functions-dotnet-class-library.md) i java `HttpTrigger` atrybut jest dostępny do skonfigurowania funkcji.

Można ustawić poziom autoryzacji i dopuszczalne metody HTTP w parametrach konstruktora atrybutów, typie elementu webhook i szablonie trasy. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [konfiguracja](#configuration).

# <a name="c"></a>[C #](#tab/csharp)

W tym przykładzie pokazano, jak używać [atrybutu HttpTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs)

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Pełny przykład można znaleźć w [przykładzie wyzwalacza](#example).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

W tym przykładzie pokazano, jak używać [atrybutu HttpTrigger.](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java)

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Pełny przykład można znaleźć w [przykładzie wyzwalacza](#example).

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `HttpTrigger` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **Typu** | Nie dotyczy| Wymagane - musi być `httpTrigger`ustawiona na . |
| **Kierunku** | Nie dotyczy| Wymagane - musi być `in`ustawiona na . |
| **Nazwa** | Nie dotyczy| Wymagane — nazwa zmiennej używana w kodzie funkcji dla treści żądania lub żądania. |
| <a name="http-auth"></a>**authLevel (poziom ów)** |  **AuthLevel (AuthLevel)** |Określa, jakie klucze, jeśli istnieją, muszą być obecne na żądanie w celu wywołania funkcji. Poziom autoryzacji może być jedną z następujących wartości: <ul><li><code>anonymous</code>&mdash;Nie jest wymagany żaden klucz interfejsu API.</li><li><code>function</code>&mdash;Wymagany jest klucz interfejsu API specyficzny dla funkcji. Jest to wartość domyślna, jeśli nie podano.</li><li><code>admin</code>&mdash;Wymagany jest klucz główny.</li></ul> Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [kluczy autoryzacji](#authorization-keys). |
| **Metody** |**Metody** | Tablica metod HTTP, na które odpowiada funkcja. Jeśli nie zostanie określony, funkcja odpowiada na wszystkie metody HTTP. Zobacz [dostosowywanie punktu końcowego HTTP](#customize-the-http-endpoint). |
| **Trasy** | **Trasa** | Definiuje szablon trasy, kontrolując, na które adresy URL żądania odpowiada funkcja. Wartość domyślna, jeśli `<functionname>`nie jest podany jest . Aby uzyskać więcej informacji, zobacz [dostosowywanie punktu końcowego HTTP](#customize-the-http-endpoint). |
| **Element elementów webHookType** | **Typ elementu WebHookType** | _Obsługiwane tylko dla środowiska wykonawczego w wersji 1.x._<br/><br/>Konfiguruje wyzwalacz HTTP do działania jako odbiornik [elementu webhook](https://en.wikipedia.org/wiki/Webhook) dla określonego dostawcy. Nie ustawiaj `methods` właściwości, jeśli ustawisz tę właściwość. Typ elementu webhook może być jedną z następujących wartości:<ul><li><code>genericJson</code>&mdash;Punkt końcowy elementu sieci Web ogólnego przeznaczenia bez logiki dla określonego dostawcy. To ustawienie ogranicza żądania tylko do tych, `application/json` które używają protokołu HTTP POST i do typu zawartości.</li><li><code>github</code>&mdash;Funkcja odpowiada na elementów [webhook GitHub](https://developer.github.com/webhooks/). Nie należy używać _authLevel_ właściwości z elementami webhook GitHub. Aby uzyskać więcej informacji, zobacz sekcję elementów webhook usługi GitHub w dalszej części tego artykułu.</li><li><code>slack</code>&mdash;Funkcja odpowiada na [funkcje webhook Slack](https://api.slack.com/outgoing-webhooks). Nie należy używać _authLevel_ właściwości z slack webhooks. Aby uzyskać więcej informacji, zobacz slack webhooks sekcji w dalszej części tego artykułu.</li></ul>|

## <a name="payload"></a>Ładunku

Typ wejściowy wyzwalacza jest `HttpRequest` zadeklarowany jako typ niestandardowy. Jeśli wybierzesz `HttpRequest`opcję, uzyskasz pełny dostęp do obiektu żądania. Dla typu niestandardowego środowisko wykonawcze próbuje przeanalizować treść żądania JSON, aby ustawić właściwości obiektu.

## <a name="customize-the-http-endpoint"></a>Dostosowywanie punktu końcowego HTTP

Domyślnie podczas tworzenia funkcji wyzwalacza HTTP funkcja jest adresowalna z trasą formularza:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Tę trasę można dostosować przy `route` użyciu właściwości opcjonalnej na powiązaniu wejściowym wyzwalacza HTTP. Na przykład następujący plik *function.json* definiuje `route` właściwość dla wyzwalacza HTTP:

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

Przy użyciu tej konfiguracji funkcja jest teraz adresowalna z następującą trasą zamiast oryginalnej trasy.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Ta konfiguracja umożliwia kod funkcji do obsługi dwóch parametrów w adresie, _kategorii_ i _id_.

# <a name="c"></a>[C #](#tab/csharp)

Z parametrami można użyć dowolnego [ograniczenia trasy interfejsu API sieci Web.](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) Następujący kod funkcji Języka C# używa obu parametrów.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Z parametrami można użyć dowolnego [ograniczenia trasy interfejsu API sieci Web.](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) Następujący kod funkcji Języka C# używa obu parametrów.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W node, funkcja środowiska wykonawczego udostępnia `context` treść żądania z obiektu. Aby uzyskać więcej informacji, zobacz [przykład wyzwalacza JavaScript](#example).

W poniższym przykładzie pokazano, `context.bindingData`jak odczytać parametry trasy z .

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="python"></a>[Python](#tab/python)

Kontekst wykonywania funkcji jest narażony `func.HttpRequest`za pośrednictwem parametru zadeklarowanego jako . To wystąpienie umożliwia funkcji dostęp do parametrów trasy danych, wartości ciągu zapytania i metod, które umożliwiają zwracanie odpowiedzi HTTP.

Po zdefiniowaniu parametry trasy są dostępne dla `route_params` funkcji, wywołując metodę.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="java"></a>[Java](#tab/java)

Kontekst wykonywania funkcji jest właściwości zadeklarowane w `HttpTrigger` atrybucie. Atrybut umożliwia definiowanie parametrów trasy, poziomów autoryzacji, zleceń HTTP i wystąpienia żądania przychodzącego.

Parametry trasy są `HttpTrigger` definiowane za pomocą atrybutu.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

Domyślnie wszystkie trasy funkcji są poprzedzone *api*. Można również dostosować lub usunąć prefiks przy użyciu `http.routePrefix` właściwości w pliku [host.json.](functions-host-json.md) Poniższy przykład usuwa prefiks trasy *interfejsu API* przy użyciu pustego ciągu dla prefiksu w pliku *host.json.*

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

## <a name="using-route-parameters"></a>Korzystanie z parametrów trasy

Parametry trasy, które zdefiniowały `route` wzorzec funkcji są dostępne dla każdego powiązania. Na przykład jeśli masz trasę `"route": "products/{id}"` zdefiniowaną jako powiązanie magazynu tabeli można użyć wartości parametru `{id}` w konfiguracji powiązania.

Poniższa konfiguracja `{id}` pokazuje, jak parametr jest `rowKey`przekazywany do powiązania .

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>Praca z tożsamościami klientów

Jeśli aplikacja funkcji używa [uwierzytelniania / autoryzacji usługi app service,](../app-service/overview-authentication-authorization.md)można wyświetlić informacje o uwierzytelnionych klientów z kodu. Te informacje są dostępne jako [nagłówki żądań wstrzyknięte przez platformę](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Można również odczytać te informacje z danych wiążących. Ta funkcja jest dostępna tylko dla środowiska wykonawczego Functions w 2.x lub nowszym. Jest również obecnie dostępna tylko dla języków platformy .NET.

# <a name="c"></a>[C #](#tab/csharp)

Informacje dotyczące uwierzytelnionych klientów są dostępne jako [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal jest dostępny jako część kontekstu żądania, jak pokazano w poniższym przykładzie:

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

Alternatywnie ClaimsPrincipal można po prostu uwzględnić jako dodatkowy parametr w podpisie funkcji:

```csharp
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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Informacje dotyczące uwierzytelnionych klientów są dostępne jako [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal jest dostępny jako część kontekstu żądania, jak pokazano w poniższym przykładzie:

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

Alternatywnie ClaimsPrincipal można po prostu uwzględnić jako dodatkowy parametr w podpisie funkcji:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Uwierzytelniony użytkownik jest dostępny za pośrednictwem [nagłówków HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

Uwierzytelniony użytkownik jest dostępny za pośrednictwem [nagłówków HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="java"></a>[Java](#tab/java)

Uwierzytelniony użytkownik jest dostępny za pośrednictwem [nagłówków HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

## <a name="authorization-keys"></a>Klucze autoryzacji

Funkcje umożliwia użycie klawiszy, aby utrudnić dostęp do punktów końcowych funkcji HTTP podczas tworzenia.  O ile poziom autoryzacji HTTP w funkcji `anonymous`wyzwalanej HTTP jest ustawiony na , żądania muszą zawierać klucz interfejsu API w żądaniu. 

> [!IMPORTANT]
> Podczas gdy klucze mogą pomóc zaciemnić punkty końcowe HTTP podczas tworzenia, nie są one przeznaczone jako sposób zabezpieczenia wyzwalacza HTTP w produkcji. Aby dowiedzieć się więcej, zobacz [Zabezpieczanie punktu końcowego HTTP w produkcji](#secure-an-http-endpoint-in-production).

> [!NOTE]
> W czasie wykonywania Functions 1.x dostawcy zestawów webhook mogą używać kluczy do autoryzowania żądań na różne sposoby, w zależności od tego, co obsługuje dostawca. Jest to objęte [webhooks i klucze](#webhooks-and-keys). Środowisko wykonawcze Funkcji w wersji 2.x i nowszej nie obejmuje wbudowanej obsługi dostawców elementu webhook.

#### <a name="authorization-scopes-function-level"></a>Zakresy autoryzacji (na poziomie funkcji)

Istnieją dwa zakresy autoryzacji dla kluczy na poziomie funkcji:

* **Funkcja:** Te klawisze dotyczą tylko określonych funkcji, w ramach których są zdefiniowane. Gdy jest używany jako klucz interfejsu API, umożliwiają one tylko dostęp do tej funkcji.

* **Host**: Klucze z zakresem hosta mogą być używane do uzyskiwania dostępu do wszystkich funkcji w aplikacji funkcji. Gdy jest używany jako klucz interfejsu API, umożliwiają one dostęp do dowolnej funkcji w aplikacji funkcji. 

Każdy klucz jest nazwany dla odwołania i istnieje klucz domyślny (o nazwie "domyślny") na poziomie funkcji i hosta. Klucze funkcyjne mają pierwszeństwo przed kluczami hosta. Gdy dwa klucze są zdefiniowane o tej samej nazwie, klucz funkcyjny jest zawsze używany.

#### <a name="master-key-admin-level"></a>Klucz główny (na poziomie administratora) 

Każda aplikacja funkcji ma również klucz `_master`hosta na poziomie administratora o nazwie . Oprócz zapewnienia dostępu na poziomie hosta do wszystkich funkcji w aplikacji, klucz główny zapewnia również dostęp administracyjny do interfejsów API REST środowiska wykonawczego. Nie można odwołać tego klucza. Po ustawieniu poziomu `admin`autoryzacji , żądania muszą używać klucza głównego; inne kluczowe wyniki w przypadku niepowodzenia autoryzacji.

> [!CAUTION]  
> Ze względu na podwyższone uprawnienia w aplikacji funkcji przyznane przez klucz główny, nie należy udostępniać tego klucza stronom trzecim lub rozpowszechniać go w natywnych aplikacjach klienckich. Należy zachować ostrożność przy wyborze poziomu autoryzacji administratora.

## <a name="obtaining-keys"></a>Uzyskiwanie kluczy

Klucze są przechowywane jako część aplikacji funkcji na platformie Azure i są szyfrowane w spoczynku. Aby wyświetlić klucze, utworzyć nowe lub przywinąć klucze do nowych wartości, przejdź do jednej z funkcji wyzwalanych przez protokół HTTP w [witrynie Azure portal](https://portal.azure.com) i wybierz pozycję **Zarządzaj**.

![Zarządzanie klawiszami funkcyjnymi w portalu.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Klucze funkcyjne można uzyskać programowo za pomocą [interfejsów API zarządzania kluczami](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

## <a name="api-key-authorization"></a>Autoryzacja klucza interfejsu API

Większość szablonów wyzwalaczy HTTP wymaga klucza interfejsu API w żądaniu. Więc żądanie HTTP zwykle wygląda następująco:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Klucz może być zawarty w zmiennej `code`ciągu zapytania o nazwie , jak powyżej. Może być również zawarty `x-functions-key` w nagłówku HTTP. Wartością klucza może być dowolny klawisz funkcyjny zdefiniowany dla funkcji lub dowolny klucz hosta.

Można zezwolić na anonimowe żądania, które nie wymagają kluczy. Można również wymagać, aby używany był klucz główny. Możesz zmienić domyślny poziom `authLevel` autoryzacji przy użyciu właściwości w json powiązania. Aby uzyskać więcej informacji, zobacz [Trigger - konfiguracja](#configuration).

> [!NOTE]
> Podczas uruchamiania funkcji lokalnie autoryzacja jest wyłączona niezależnie od określonego ustawienia poziomu autoryzacji. Po opublikowaniu na `authLevel` platformie Azure ustawienie w wyzwalaczu jest wymuszane. Klucze są nadal wymagane podczas [uruchamiania lokalnie w kontenerze](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Zabezpieczanie punktu końcowego HTTP w produkcji

Aby w pełni zabezpieczyć punkty końcowe funkcji w produkcji, należy rozważyć wdrożenie jednej z następujących opcji zabezpieczeń na poziomie aplikacji funkcji:

* Włącz uwierzytelnianie / autoryzację usługi app service dla aplikacji funkcji. Platforma usługi App Service umożliwia uwierzytelnianie klientów za pomocą usługi Azure Active Directory (AAD) i kilku dostawców tożsamości innych firm. Tej strategii można użyć do zaimplementowania reguł autoryzacji niestandardowej dla funkcji i można pracować z informacjami o użytkowniku z kodu funkcji. Aby dowiedzieć się więcej, zobacz [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](../app-service/overview-authentication-authorization.md) i [praca z tożsamościami klientów.](#working-with-client-identities)

* Użyj usługi Azure API Management (APIM) do uwierzytelniania żądań. Protokół APIM udostępnia wiele opcji zabezpieczeń interfejsu API dla żądań przychodzących. Aby dowiedzieć się więcej, zobacz [Zasady uwierzytelniania zarządzania interfejsami API](../api-management/api-management-authentication-policies.md). Z APIM w miejscu, można skonfigurować aplikację funkcji do akceptowania żądań tylko z adresu IP wystąpienia interfejsu APIM. Aby dowiedzieć się więcej, zobacz [Ograniczenia adresów IP](ip-addresses.md#ip-address-restrictions).

* Wdrażanie aplikacji funkcji w środowisku usługi Azure App Service (ASE). ASE zapewnia dedykowane środowisko hostingowe, w którym można uruchamiać swoje funkcje. Program ASE umożliwia skonfigurowanie pojedynczej bramy front-end, której można używać do uwierzytelniania wszystkich żądań przychodzących. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapory aplikacji sieci Web (WAF) dla środowiska usługi app service](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Korzystając z jednej z tych metod zabezpieczeń na poziomie aplikacji funkcji, należy `anonymous`ustawić poziom autoryzacji funkcji wyzwalanych przez PROTOKÓŁ HTTP na .

## <a name="webhooks"></a>Elementy webhook

> [!NOTE]
> Tryb webhook jest dostępny tylko dla wersji 1.x środowiska wykonawczego Functions. Ta zmiana została wywłyń w celu zwiększenia wydajności wyzwalaczy HTTP w wersji 2.x i nowszej.

W wersji 1.x szablony elementu webhook zapewniają dodatkową weryfikację ładunków elementu webhook. W wersji 2.x i nowszej podstawowy wyzwalacz HTTP nadal działa i jest zalecanym podejściem dla witryn webhook. 

### <a name="github-webhooks"></a>Elementów webhook GitHub

Aby odpowiedzieć na elementów webhook GitHub, najpierw utwórz funkcję za pomocą `github`wyzwalacza HTTP i ustaw właściwość **webHookType** na . Następnie skopiuj jego adres URL i klucz interfejsu API na stronie **Dodaj element webhook** repozytorium GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Luźne zaczepy internetowe

Element webhook Slack generuje token dla Ciebie, zamiast pozwala określić go, więc należy skonfigurować klucz specyficzny dla funkcji z tokenem z Slack. Zobacz [Klucze autoryzacji](#authorization-keys).

## <a name="webhooks-and-keys"></a>Elementów webhook i klucze

Autoryzacja elementu webhook jest obsługiwana przez składnik odbiornika elementu webhook, część wyzwalacza HTTP, a mechanizm różni się w zależności od typu elementu webhook. Każdy mechanizm opiera się na kluczu. Domyślnie używany jest klawisz funkcyjny o nazwie "default". Aby użyć innego klucza, skonfiguruj dostawcę elementu webhook do wysyłania nazwy klucza z żądaniem w jeden z następujących sposobów:

* **Ciąg zapytania:** Dostawca przekazuje nazwę `clientid` klucza w parametrze ciągu zapytania, na przykład `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Nagłówek żądania:** Dostawca przekazuje nazwę `x-functions-clientid` klucza w nagłówku.

## <a name="limits"></a>Limity

Długość żądania HTTP jest ograniczona do 100 MB (104 857 600 bajtów), a długość adresu URL jest ograniczona do 4 KB (4096 bajtów). Limity te są `httpRuntime` określone przez element [pliku Web.config](https://github.com/Azure/azure-functions-host/blob/3.x/src/WebJobs.Script.WebHost/web.config)środowiska wykonawczego .

Jeśli funkcja, która używa wyzwalacza HTTP nie zostanie ukończona w ciągu 230 sekund, [moduł równoważenia obciążenia platformy Azure](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) upuszczy limit czasu i zwróci błąd HTTP 502. Funkcja będzie nadal działać, ale nie będzie można zwrócić odpowiedzi HTTP. W przypadku długotrwałych funkcji zaleca się śledzenie wzorców asynchronizowych i zwracanie lokalizacji, w której można pingować stan żądania. Aby uzyskać informacje o tym, jak długo funkcja może działać, zobacz [Skalowanie i hosting — Plan zużycia](functions-scale.md#timeout).


## <a name="next-steps"></a>Następne kroki

- [Zwracanie odpowiedzi HTTP z funkcji](./functions-bindings-http-webhook-output.md)
