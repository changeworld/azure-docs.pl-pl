---
title: Usługa Azure Functions SignalR powiązania
description: Opis sposobu użycia powiązania usługi SignalR z usługą Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
editor: ''
tags: ''
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: f0d4a607676285ed4f0f91d8ce8c83ddf1313b89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64511139"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Powiązania usługi SignalR Service dla usługi Azure Functions

W tym artykule wyjaśniono, jak uwierzytelniać i wysyłanie komunikatów w czasie rzeczywistym do klientów dołączonych do [usługi Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) przy użyciu usługi SignalR powiązań w usłudze Azure Functions. Usługi Azure Functions obsługuje danych wejściowych i wyjściowych powiązań dla usług SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania usługi SignalR znajdują się w [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) pakietu NuGet, 1.* wersji. Kod źródłowy dla pakietu znajduje się w [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Adnotacje języka Java

Aby użyć adnotacji usługi SignalR w funkcjach języka Java, musisz dodać zależność do *azure-functions-java — biblioteki — signalr* artefaktu (w wersji 1.0 lub nowszej) do usługi pom.xml.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Aby użyć powiązania usługi SignalR w języku Java, upewnij się, że używasz wersji 2.4.419 lub nowszej podstawowe narzędzia usługi Azure Functions (wersja 2.0.12332 hosta).

## <a name="using-signalr-service-with-azure-functions"></a>Przy użyciu usługi SignalR z usługą Azure Functions

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania i używania razem SignalR Service i Azure Functions, zobacz [rozwoju usługi Azure Functions i konfiguracji za pomocą usługi Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>Powiązania danych wejściowych informacje połączenia SignalR

Zanim klient może nawiązać połączenie usługi Azure SignalR Service, należy pobrać, adres URL punktu końcowego usługi i prawidłowym tokenem dostępu. *SignalRConnectionInfo* powiązania danych wejściowych generuje adres URL punktu końcowego usługi SignalR i prawidłowy token, które są używane do połączenia z usługą. Ponieważ token jest ograniczony czasowo i może służyć do uwierzytelniania określonego użytkownika na połączenie, nie należy pamięci podręcznej tokenu lub udostępniać je między klientami. Wyzwalacz HTTP używa tego powiązania może służyć przez klientów można pobrać informacji o połączeniu.

Zobacz przykład specyficzny dla języka:

* [2.x C#](#2x-c-input-examples)
* [2.x JavaScript](#2x-javascript-input-examples)
* [2.x języka Java](#2x-java-input-examples)

Aby uzyskać więcej informacji na temat sposobu to powiązanie jest używany do tworzenia "negocjowania" funkcji, które mogą być używane przez klienta SignalR zestawu SDK, zobacz [artykułu projektowania i konfiguracji usługi Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md) w pojęcia dotyczące usługi SignalR dokumentacja.

### <a name="2x-c-input-examples"></a>2.x C# wejściowych przykłady

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , uzyskuje informacje dotyczące połączenia SignalR przy użyciu powiązania danych wejściowych i zwraca go za pośrednictwem protokołu HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Uwierzytelniony tokenów

Jeśli funkcja jest wyzwalana przez uwierzytelnionego klienta, możesz dodać oświadczenie Identyfikatora użytkownika do wygenerowanego tokenu. Możesz łatwo dodawać do aplikacji funkcji przy użyciu uwierzytelniania [uwierzytelnianie usługi App Service](../app-service/overview-authentication-authorization.md).

Uwierzytelnianie usługi App Service ustawia nagłówki HTTP o nazwie `x-ms-client-principal-id` i `x-ms-client-principal-name` zawierające odpowiednio identyfikator podmiotu zabezpieczeń klienta i nazwa uwierzytelnionego użytkownika. Możesz ustawić `UserId` właściwości powiązania wartości z przy użyciu nagłówka [powiązanie wyrażenie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}`. 

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

### <a name="2x-javascript-input-examples"></a>Przykłady danych wejściowych języka JavaScript 2.x

W poniższym przykładzie pokazano SignalR połączenia informacje powiązania danych wejściowych w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) , używa powiązania do zwracania informacji o połączeniu.

Oto powiązanie danych w *function.json* pliku:

Przykład function.json:

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

#### <a name="authenticated-tokens"></a>Uwierzytelniony tokenów

Jeśli funkcja jest wyzwalana przez uwierzytelnionego klienta, możesz dodać oświadczenie Identyfikatora użytkownika do wygenerowanego tokenu. Możesz łatwo dodawać do aplikacji funkcji przy użyciu uwierzytelniania [uwierzytelnianie usługi App Service](../app-service/overview-authentication-authorization.md).

Uwierzytelnianie usługi App Service ustawia nagłówki HTTP o nazwie `x-ms-client-principal-id` i `x-ms-client-principal-name` zawierające odpowiednio identyfikator podmiotu zabezpieczeń klienta i nazwa uwierzytelnionego użytkownika. Możesz ustawić `userId` właściwości powiązania wartości z przy użyciu nagłówka [powiązanie wyrażenie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}`. 

Przykład function.json:

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

### <a name="2x-java-input-examples"></a>wejściowy przykładów w języku Java 2.x

W poniższym przykładzie przedstawiono [funkcja Java](functions-reference-java.md) , uzyskuje informacje dotyczące połączenia SignalR przy użyciu powiązania danych wejściowych i zwraca go za pośrednictwem protokołu HTTP.

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

#### <a name="authenticated-tokens"></a>Uwierzytelniony tokenów

Jeśli funkcja jest wyzwalana przez uwierzytelnionego klienta, możesz dodać oświadczenie Identyfikatora użytkownika do wygenerowanego tokenu. Możesz łatwo dodawać do aplikacji funkcji przy użyciu uwierzytelniania [uwierzytelnianie usługi App Service](../app-service/overview-authentication-authorization.md).

Uwierzytelnianie usługi App Service ustawia nagłówki HTTP o nazwie `x-ms-client-principal-id` i `x-ms-client-principal-name` zawierające odpowiednio identyfikator podmiotu zabezpieczeń klienta i nazwa uwierzytelnionego użytkownika. Możesz ustawić `UserId` właściwości powiązania wartości z przy użyciu nagłówka [powiązanie wyrażenie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}`.

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

## <a name="signalr-output-binding"></a>Powiązanie danych wyjściowych SignalR

Użyj *SignalR* powiązania do wysyłania komunikatów co najmniej jeden, za pomocą usługi Azure SignalR Service danych wyjściowych. Może emitować wiadomość do wszystkich połączonych klientów lub emitowanie jej tylko do połączonych klientów, które zostały uwierzytelnione do danego użytkownika.

Można również użyć do zarządzania grupami, do których należy użytkownik.

Zobacz przykład specyficzny dla języka:

* [2.x C#](#2x-c-send-message-output-examples)
* [2.x JavaScript](#2x-javascript-send-message-output-examples)
* [2.x języka Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2.x C# wysłać komunikat wyjściowy przykłady

#### <a name="broadcast-to-all-clients"></a>Można rozgłaszać do wszystkich klientów

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , wysyła komunikat przy użyciu powiązania danych wyjściowych, aby wszyscy połączeni klienci. `Target` Jest nazwa metody do wywołania na każdym komputerze klienckim. `Arguments` Właściwość jest tablicą zero lub więcej obiektów, które mają być przekazane do metody klienta.

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

#### <a name="send-to-a-user"></a>Wysłać do użytkownika

Możesz wysłać wiadomość tylko do połączeń, które zostały uwierzytelnione, ustawiając dla użytkownika `UserId` właściwości komunikatu SignalR.

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

#### <a name="send-to-a-group"></a>Wyślij do grupy

Możesz wysłać wiadomość tylko do połączeń, które zostały dodane do grupy, ustawiając `GroupName` właściwości komunikatu SignalR.

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

### <a name="2x-c-group-management-output-examples"></a>2.x C# Zarządzanie grupami danych wyjściowych przykłady

SignalR Service pozwala użytkownikom mają zostać dodane do grupy. Komunikaty mogą być wysyłane następnie do grupy. Możesz użyć `SignalRGroupAction` klasy `SignalR` powiązania do zarządzania członkostwa w grupie użytkownika danych wyjściowych.

#### <a name="add-user-to-a-group"></a>Dodawanie użytkownika do grupy

Poniższy przykład dodaje użytkownika do grupy.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
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
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

### <a name="2x-javascript-send-message-output-examples"></a>2.x JavaScript wysyła komunikat wyjściowy przykłady

#### <a name="broadcast-to-all-clients"></a>Można rozgłaszać do wszystkich klientów

W poniższym przykładzie pokazano powiązania w danych wyjściowych SignalR *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającej powiązania do wysyłania komunikatów z usługi Azure SignalR Service. Ustaw powiązania danych wyjściowych do tablicy jeden lub więcej komunikatów SignalR. Komunikat SignalR składa się z `target` właściwość, która określa nazwę metody do wywołania na każdym komputerze klienckim, a `arguments` właściwość, która jest tablicą obiektów, które należy przekazać do metody klienta jako argumenty.

Oto powiązanie danych w *function.json* pliku:

Przykład function.json:

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

#### <a name="send-to-a-user"></a>Wysłać do użytkownika

Możesz wysłać wiadomość tylko do połączeń, które zostały uwierzytelnione, ustawiając dla użytkownika `userId` właściwości komunikatu SignalR.

*Function.JSON* pozostaje taka sama. Poniżej przedstawiono kod JavaScript:

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

#### <a name="send-to-a-group"></a>Wyślij do grupy

Możesz wysłać wiadomość tylko do połączeń, które zostały dodane do grupy, ustawiając `groupName` właściwości komunikatu SignalR.

*Function.JSON* pozostaje taka sama. Poniżej przedstawiono kod JavaScript:

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

### <a name="2x-javascript-group-management-output-examples"></a>2.x języka JavaScript, Zarządzanie grupami danych wyjściowych przykłady

SignalR Service pozwala użytkownikom mają zostać dodane do grupy. Komunikaty mogą być wysyłane następnie do grupy. Możesz użyć `SignalR` powiązania do zarządzania członkostwa w grupie użytkownika danych wyjściowych.

#### <a name="add-user-to-a-group"></a>Dodawanie użytkownika do grupy

Poniższy przykład dodaje użytkownika do grupy.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

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

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2.x Java wysyłania komunikatów wyjściowych przykłady

#### <a name="broadcast-to-all-clients"></a>Można rozgłaszać do wszystkich klientów

W poniższym przykładzie przedstawiono [funkcja Java](functions-reference-java.md) , wysyła komunikat przy użyciu powiązania danych wyjściowych, aby wszyscy połączeni klienci. `target` Jest nazwa metody do wywołania na każdym komputerze klienckim. `arguments` Właściwość jest tablicą zero lub więcej obiektów, które mają być przekazane do metody klienta.

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

#### <a name="send-to-a-user"></a>Wysłać do użytkownika

Możesz wysłać wiadomość tylko do połączeń, które zostały uwierzytelnione, ustawiając dla użytkownika `userId` właściwości komunikatu SignalR.

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

#### <a name="send-to-a-group"></a>Wyślij do grupy

Możesz wysłać wiadomość tylko do połączeń, które zostały dodane do grupy, ustawiając `groupName` właściwości komunikatu SignalR.

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

### <a name="2x-java-group-management-output-examples"></a>2.x języka Java, Zarządzanie grupami danych wyjściowych przykłady

SignalR Service pozwala użytkownikom mają zostać dodane do grupy. Komunikaty mogą być wysyłane następnie do grupy. Możesz użyć `SignalRGroupAction` klasy `SignalROutput` powiązania do zarządzania członkostwa w grupie użytkownika danych wyjściowych.

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

## <a name="configuration"></a>Konfigurowanie

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `SignalRConnectionInfo` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**|| Musi być równa `signalRConnectionInfo`.|
|**direction**|| Musi być równa `in`.|
|**name**|| Nazwa zmiennej użytą w kodzie funkcji dla obiektu informacje o połączeniu. |
|**hubName**|**HubName**| Ta wartość musi być równa nazwę Centrum SignalR, dla którego jest generowane informacje o połączeniu.|
|**userId**|**UserId**| Opcjonalnie: Wartość identyfikatora użytkownika oświadczenia w tokenie klucza dostępu. |
|**connectionStringSetting**|**ConnectionStringSetting**| Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi SignalR (wartość domyślna to "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `SignalR` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**|| Musi być równa `signalR`.|
|**direction**|| Musi być równa `out`.|
|**name**|| Nazwa zmiennej użytą w kodzie funkcji dla obiektu informacje o połączeniu. |
|**hubName**|**HubName**| Ta wartość musi być równa nazwę Centrum SignalR, dla którego jest generowane informacje o połączeniu.|
|**connectionStringSetting**|**ConnectionStringSetting**| Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi SignalR (wartość domyślna to "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Programowanie i konfigurowanie w usłudze Azure Functions za pomocą usługi Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
