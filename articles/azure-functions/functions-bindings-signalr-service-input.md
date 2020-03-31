---
title: Powiązanie wejściowe usługi SignalR usługi azure functions
description: Dowiedz się, aby zwrócić adres URL punktu końcowego usługi SignalR i token dostępu w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530265"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Powiązanie wejściowe usługi SignalR dla funkcji platformy Azure

Zanim klient może połączyć się z usługą Azure SignalR Service, musi pobrać adres URL punktu końcowego usługi i prawidłowy token dostępu. Powiązanie wejściowe *SignalRConnectionInfo* tworzy adres URL punktu końcowego usługi SignalR i prawidłowy token, które są używane do łączenia się z usługą. Ponieważ token jest ograniczony czasowo i może służyć do uwierzytelniania określonego użytkownika do połączenia, nie należy buforować tokenu lub udostępniać go między klientami. Wyzwalacz HTTP przy użyciu tego powiązania może służyć przez klientów do pobierania informacji o połączeniu.

Aby uzyskać więcej informacji na temat tego powiązania jest używany do tworzenia funkcji "negotiate", które mogą być używane przez SDK klienta signalr, zobacz [artykuł rozwoju i konfiguracji usługi Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md) w dokumentacji koncepcji usługi SignalR.

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](functions-bindings-signalr-service.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która uzyskuje informacje o połączeniu SignalR przy użyciu powiązania wejściowego i zwraca go za pośrednictwem protokołu HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wejściowe informacji o połączeniu SignalR w pliku *function.json* i [funkcję skryptu języka C#,](functions-reference-csharp.md) która używa powiązania do zwracania informacji o połączeniu.

Oto dane powiązania w pliku *function.json:*

Przykładowa funkcja.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod skryptu języka C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wejściowe informacji o połączeniu SignalR w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania do zwracania informacji o połączeniu.

Oto dane powiązania w pliku *function.json:*

Przykładowa funkcja.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano powiązanie wejściowe informacji o połączeniu SignalR w pliku *function.json* i [funkcję Języka Python,](functions-reference-python.md) która używa powiązania do zwracania informacji o połączeniu.

Oto dane powiązania w pliku *function.json:*

Przykładowa funkcja.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod Pythona:

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

# <a name="java"></a>[Java](#tab/java)

W poniższym przykładzie pokazano [funkcję Java,](functions-reference-java.md) która pobiera informacje o połączeniu SignalR przy użyciu powiązania wejściowego i zwraca go za pośrednictwem protokołu HTTP.

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

## <a name="authenticated-tokens"></a>Uwierzytelnione tokeny

Jeśli funkcja jest wyzwalana przez uwierzytelnionego klienta, można dodać oświadczenie identyfikatora użytkownika do wygenerowanego tokenu. Uwierzytelnianie można łatwo dodać do aplikacji funkcyjnej przy użyciu [funkcji App Service Authentication](../app-service/overview-authentication-authorization.md).

Uwierzytelnianie usługi aplikacji ustawia `x-ms-client-principal-id` `x-ms-client-principal-name` nagłówki HTTP o nazwie i które zawierają odpowiednio identyfikator i nazwę główną klienta uwierzytelnionego użytkownika.

# <a name="c"></a>[C #](#tab/csharp)

Właściwość powiązania `UserId` można ustawić na wartość z nagłówka za `{headers.x-ms-client-principal-id}` pomocą `{headers.x-ms-client-principal-name}` [wyrażenia wiążącego](./functions-bindings-expressions-patterns.md): lub .

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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Właściwość powiązania `userId` można ustawić na wartość z nagłówka za `{headers.x-ms-client-principal-id}` pomocą `{headers.x-ms-client-principal-name}` [wyrażenia wiążącego](./functions-bindings-expressions-patterns.md): lub .

Przykładowa funkcja.json:

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

Oto kod skryptu języka C#:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Właściwość powiązania `userId` można ustawić na wartość z nagłówka za `{headers.x-ms-client-principal-id}` pomocą `{headers.x-ms-client-principal-name}` [wyrażenia wiążącego](./functions-bindings-expressions-patterns.md): lub .

Przykładowa funkcja.json:

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

Oto kod JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Właściwość powiązania `userId` można ustawić na wartość z nagłówka za `{headers.x-ms-client-principal-id}` pomocą `{headers.x-ms-client-principal-name}` [wyrażenia wiążącego](./functions-bindings-expressions-patterns.md): lub .

Przykładowa funkcja.json:

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

Oto kod Pythona:

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

# <a name="java"></a>[Java](#tab/java)

Właściwość powiązania `userId` można ustawić na wartość z nagłówka za `{headers.x-ms-client-principal-id}` pomocą `{headers.x-ms-client-principal-name}` [wyrażenia wiążącego](./functions-bindings-expressions-patterns.md): lub .

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

## <a name="next-steps"></a>Następne kroki

- [Wyślij komunikaty usługi SignalR (powiązanie wyjściowe)](./functions-bindings-signalr-service-output.md) 
