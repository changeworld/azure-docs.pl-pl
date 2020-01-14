---
title: Powiązania usługi Azure Functions sygnalizującego
description: Dowiedz się, jak używać powiązań usługi sygnałów z Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: cbc45036497547b78514b1d175d67c6ed437ee2d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769493"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Powiązania usługi SignalR Service dla usługi Azure Functions

W tym artykule wyjaśniono, jak uwierzytelniać i wysyłać komunikaty w czasie rzeczywistym do klientów podłączonych do [usługi Azure Signal Service](https://azure.microsoft.com/services/signalr-service/) przy użyciu powiązań usługi sygnałów w Azure Functions. Usługa Azure Functions obsługuje powiązania wejściowe i wyjściowe dla usługi SignalR Service.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2. x i nowsze

Powiązania usługi sygnalizującego są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) w wersji 1. *. Kod źródłowy pakietu znajduje się w repozytorium [Azure-Functions-signalrservice-Extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

Aby uzyskać szczegółowe informacje na temat konfigurowania i używania usługi sygnalizującego oraz Azure Functions ze sobą, zobacz [Azure Functions programowanie i Konfigurowanie za pomocą usługi Azure Signal Service](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Biblioteka adnotacji (tylko w języku Java)

Aby użyć adnotacji usługi sygnalizującej w funkcjach języka Java, należy dodać zależność do artefaktu *platformy Azure-Functions-Java-library* (wersja 1,0 lub nowsza) do pliku pliku pom. XML.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="input"></a>Dane wejściowe

Aby klient mógł nawiązać połączenie z usługą Azure Signal, musi pobrać adres URL punktu końcowego usługi i prawidłowy token dostępu. Powiązanie danych wejściowych *SignalRConnectionInfo* generuje adres URL punktu końcowego usługi sygnalizującego oraz prawidłowy token, który jest używany do nawiązywania połączenia z usługą. Ponieważ token jest ograniczony do czasu i może służyć do uwierzytelniania określonego użytkownika w połączeniu, nie należy buforować tokenu ani udostępniać go między klientami. Wyzwalacz HTTP korzystający z tego powiązania może być używany przez klientów do pobierania informacji o połączeniu.

Aby uzyskać więcej informacji na temat tego powiązania do tworzenia funkcji "Negotiate", która może być używana przez zestaw SDK klienta sygnalizującego, zapoznaj się z [artykułem opracowywanie i konfigurowanie Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md) w dokumentacji dotyczącej pojęć dotyczących usługi sygnalizującej.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W poniższym przykładzie pokazano [ C# funkcję](functions-dotnet-class-library.md) , która uzyskuje informacje o połączeniu sygnalizujące przy użyciu powiązania wejściowego i zwraca je za pośrednictwem protokołu HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie danych wejściowych informacji o połączeniu sygnalizującego w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania do zwrócenia informacji o połączeniu.

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod C# skryptu:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie danych wejściowych informacji o połączeniu sygnalizującego w pliku *Function. JSON* i [funkcji języka JavaScript](functions-reference-node.md) , która używa powiązania do zwrócenia informacji o połączeniu.

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Poniższy przykład przedstawia powiązanie danych wejściowych informacji o połączeniu sygnalizującego w pliku *Function. JSON* i funkcji języka [Python](functions-reference-python.md) , która używa powiązania do zwrócenia informacji o połączeniu.

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod języka Python:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

Poniższy przykład pokazuje [funkcję języka Java](functions-reference-java.md) , która uzyskuje informacje o połączeniu sygnalizujące przy użyciu powiązania wejściowego i zwraca je za pośrednictwem protokołu HTTP.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

### <a name="authenticated-tokens"></a>Tokeny uwierzytelnione

Jeśli funkcja jest wyzwalana przez uwierzytelnionego klienta, można dodać do wygenerowanego tokenu identyfikator użytkownika. Uwierzytelnianie w aplikacji funkcji można łatwo dodać przy użyciu [uwierzytelniania App Service](../app-service/overview-authentication-authorization.md).

App Service Authentication ustawia nagłówki HTTP o nazwach `x-ms-client-principal-id` i `x-ms-client-principal-name`, które zawierają odpowiednio Identyfikator podmiotu zabezpieczeń klienta uwierzytelnionego użytkownika i jego nazwę.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Właściwość `UserId` powiązania można ustawić na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}`.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Właściwość `userId` powiązania można ustawić na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}`.

Przykład Function. JSON:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod C# skryptu:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Właściwość `userId` powiązania można ustawić na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}`.

Przykład Function. JSON:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Właściwość `userId` powiązania można ustawić na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}`.

Przykład Function. JSON:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod języka Python:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

Właściwość `userId` powiązania można ustawić na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}`.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="output"></a>Dane wyjściowe

Użyj powiązania danych wyjściowych *sygnalizującego* , aby wysłać co najmniej jeden komunikat przy użyciu usługi Azure Signal Service. Komunikat można emitować do wszystkich połączonych klientów lub można go emitować tylko do podłączonych klientów, którzy zostali uwierzytelnieni do danego użytkownika.

Można go również użyć do zarządzania grupami, do których należy użytkownik.

### <a name="broadcast-to-all-clients"></a>Emituj do wszystkich klientów

Poniższy przykład pokazuje funkcję, która wysyła komunikat przy użyciu powiązania danych wyjściowych do wszystkich połączonych klientów. *Obiekt docelowy* jest nazwą metody, która ma być wywoływana na każdym kliencie. *Argumenty* jest tablicą zero lub więcej obiektów do przesłania do metody klienta.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Oto kod C# skryptu:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Oto kod języka Python:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-user"></a>Wyślij do użytkownika

Komunikat można wysłać tylko do połączeń, które zostały uwierzytelnione do użytkownika przez ustawienie *identyfikatora użytkownika* w komunikacie sygnalizującym.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Przykład Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Oto kod C# skryptu:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Przykład Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Oto kod języka Python:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-group"></a>Wyślij do grupy

Komunikat można wysłać tylko do połączeń, które zostały dodane do grupy przez ustawienie *nazwy grupy* w komunikacie sygnalizującym.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Przykład Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Oto kod C# skryptu:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Przykład Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Oto kod języka Python:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="group-management"></a>Zarządzanie grupami

Usługa sygnalizująca pozwala dodawać użytkowników do grup. Komunikaty można następnie wysyłać do grupy. Możesz użyć powiązania danych wyjściowych `SignalR` do zarządzania członkostwem w grupie użytkownika.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

#### <a name="add-user-to-a-group"></a>Dodawanie użytkownika do grupy

Poniższy przykład dodaje użytkownika do grupy.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Usuwanie użytkownika z grupy

Poniższy przykład usuwa użytkownika z grupy.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Aby można było prawidłowo uzyskać `ClaimsPrincipal`, musisz skonfigurować ustawienia uwierzytelniania w Azure Functions.

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

#### <a name="add-user-to-a-group"></a>Dodawanie użytkownika do grupy

Poniższy przykład dodaje użytkownika do grupy.

Przykład *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Uruchom. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Usuwanie użytkownika z grupy

Poniższy przykład usuwa użytkownika z grupy.

Przykład *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Uruchom. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Aby można było prawidłowo uzyskać `ClaimsPrincipal`, musisz skonfigurować ustawienia uwierzytelniania w Azure Functions.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="add-user-to-a-group"></a>Dodawanie użytkownika do grupy

Poniższy przykład dodaje użytkownika do grupy.

Przykład *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index. js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>Usuwanie użytkownika z grupy

Poniższy przykład usuwa użytkownika z grupy.

Przykład *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index. js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

#### <a name="add-user-to-a-group"></a>Dodawanie użytkownika do grupy

Poniższy przykład dodaje użytkownika do grupy.

Przykład *Function. JSON*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

#### <a name="remove-user-from-a-group"></a>Usuwanie użytkownika z grupy

Poniższy przykład usuwa użytkownika z grupy.

Przykład *Function. JSON*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

#### <a name="add-user-to-a-group"></a>Dodawanie użytkownika do grupy

Poniższy przykład dodaje użytkownika do grupy.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>Usuwanie użytkownika z grupy

Poniższy przykład usuwa użytkownika z grupy.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

---

## <a name="configuration"></a>Konfigurowanie

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `SignalRConnectionInfo` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**|| Musi być równa `signalRConnectionInfo`.|
|**direction**|| Musi być równa `in`.|
|**Nazwa**|| Nazwa zmiennej używana w kodzie funkcji dla obiektu informacji o połączeniu. |
|**hubName**|**HubName**| Ta wartość musi być ustawiona na nazwę centrum sygnałów, dla którego są generowane informacje o połączeniu.|
|**userId**|**UserId**| Opcjonalnie: wartość żądania identyfikatora użytkownika do ustawienia w tokenie klucza dostępu. |
|**connectionStringSetting**|**ConnectionStringSetting**| Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi sygnalizującego (wartość domyślna to "AzureSignalRConnectionString"). |

### <a name="signalr"></a>SignalR

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `SignalR` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**|| Musi być równa `signalR`.|
|**direction**|| Musi być równa `out`.|
|**Nazwa**|| Nazwa zmiennej używana w kodzie funkcji dla obiektu informacji o połączeniu. |
|**hubName**|**HubName**| Ta wartość musi być ustawiona na nazwę centrum sygnałów, dla którego są generowane informacje o połączeniu.|
|**connectionStringSetting**|**ConnectionStringSetting**| Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi sygnalizującego (wartość domyślna to "AzureSignalRConnectionString"). |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Programowanie i konfigurowanie w usłudze Azure Functions za pomocą usługi Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
