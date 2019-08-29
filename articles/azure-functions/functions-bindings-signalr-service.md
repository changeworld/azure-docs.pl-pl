---
title: Powiązania usługi Azure Functions sygnalizującego
description: Dowiedz się, jak używać powiązań usługi sygnałów z Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
editor: ''
tags: ''
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 82bbd728bfd51595f0b6eacf344bc95ea5db1ea8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086159"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Powiązania usługi SignalR Service dla usługi Azure Functions

W tym artykule wyjaśniono, jak uwierzytelniać i wysyłać komunikaty w czasie rzeczywistym do klientów podłączonych do [usługi Azure Signal Service](https://azure.microsoft.com/services/signalr-service/) przy użyciu powiązań usługi sygnałów w Azure Functions. Azure Functions obsługuje powiązania wejściowe i wyjściowe dla usługi sygnalizującej.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania usługi sygnalizującego są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) w wersji 1. *. Kod źródłowy pakietu znajduje się w repozytorium [Azure-Functions-signalrservice-Extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Adnotacje języka Java

Aby użyć adnotacji usługi sygnalizującej w funkcjach języka Java, należy dodać zależność do artefaktu *platformy Azure-Functions-Java-library* (wersja 1,0 lub nowsza) do pliku pliku pom. XML.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Aby użyć powiązań usługi sygnalizującego w języku Java, upewnij się, że używasz wersji 2.4.419 lub nowszej Azure Functions Core Tools (wersja hosta 2.0.12332).

## <a name="using-signalr-service-with-azure-functions"></a>Korzystanie z usługi sygnalizującej z Azure Functions

Aby uzyskać szczegółowe informacje na temat konfigurowania i używania usługi sygnalizującego oraz Azure Functions ze sobą, zobacz [Azure Functions programowanie i Konfigurowanie za pomocą usługi Azure Signal Service](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>Powiązanie danych wejściowych informacji o połączeniu sygnalizującym

Aby klient mógł nawiązać połączenie z usługą Azure Signal, musi pobrać adres URL punktu końcowego usługi i prawidłowy token dostępu. Powiązanie danych wejściowych *SignalRConnectionInfo* generuje adres URL punktu końcowego usługi sygnalizującego oraz prawidłowy token, który jest używany do nawiązywania połączenia z usługą. Ponieważ token jest ograniczony do czasu i może służyć do uwierzytelniania określonego użytkownika w połączeniu, nie należy buforować tokenu ani udostępniać go między klientami. Wyzwalacz HTTP korzystający z tego powiązania może być używany przez klientów do pobierania informacji o połączeniu.

Zobacz przykład specyficzny dla języka:

* [2.xC#](#2x-c-input-examples)
* [2. x JavaScript](#2x-javascript-input-examples)
* [2. x Java](#2x-java-input-examples)

Aby uzyskać więcej informacji na temat tego powiązania do tworzenia funkcji "Negotiate", która może być używana przez zestaw SDK klienta sygnalizującego, zapoznaj się z [artykułem opracowywanie i konfigurowanie Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md) w dokumentacji dotyczącej pojęć dotyczących usługi sygnalizującej.

### <a name="2x-c-input-examples"></a>Przykładowe dane wejściowe C# 2. x

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

#### <a name="authenticated-tokens"></a>Tokeny uwierzytelnione

Jeśli funkcja jest wyzwalana przez uwierzytelnionego klienta, można dodać do wygenerowanego tokenu identyfikator użytkownika. Uwierzytelnianie w aplikacji funkcji można łatwo dodać przy użyciu [uwierzytelniania App Service](../app-service/overview-authentication-authorization.md).

App Service Authentication ustawia nagłówki HTTP o `x-ms-client-principal-id` nazwach i `x-ms-client-principal-name` , które zawierają odpowiednio Identyfikator podmiotu zabezpieczeń klienta uwierzytelnionego i nazwę. Można ustawić `UserId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub. `{headers.x-ms-client-principal-name}` 

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

### <a name="2x-javascript-input-examples"></a>Przykłady danych wejściowych JavaScript 2. x

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

#### <a name="authenticated-tokens"></a>Tokeny uwierzytelnione

Jeśli funkcja jest wyzwalana przez uwierzytelnionego klienta, można dodać do wygenerowanego tokenu identyfikator użytkownika. Uwierzytelnianie w aplikacji funkcji można łatwo dodać przy użyciu [uwierzytelniania App Service](../app-service/overview-authentication-authorization.md).

App Service Authentication ustawia nagłówki HTTP o `x-ms-client-principal-id` nazwach i `x-ms-client-principal-name` , które zawierają odpowiednio Identyfikator podmiotu zabezpieczeń klienta uwierzytelnionego i nazwę. Można ustawić `userId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub. `{headers.x-ms-client-principal-name}` 

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

### <a name="2x-java-input-examples"></a>2. x przykłady danych wejściowych Java

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

#### <a name="authenticated-tokens"></a>Tokeny uwierzytelnione

Jeśli funkcja jest wyzwalana przez uwierzytelnionego klienta, można dodać do wygenerowanego tokenu identyfikator użytkownika. Uwierzytelnianie w aplikacji funkcji można łatwo dodać przy użyciu [uwierzytelniania App Service](../app-service/overview-authentication-authorization.md).

App Service Authentication ustawia nagłówki HTTP o `x-ms-client-principal-id` nazwach i `x-ms-client-principal-name` , które zawierają odpowiednio Identyfikator podmiotu zabezpieczeń klienta uwierzytelnionego i nazwę. Można ustawić `UserId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub. `{headers.x-ms-client-principal-name}`

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

## <a name="signalr-output-binding"></a>Powiązanie danych wyjściowych sygnalizującego

Użyj powiązania danych wyjściowych sygnalizującego, aby wysłać co najmniej jeden komunikat przy użyciu usługi Azure Signal Service. Komunikat można emitować do wszystkich połączonych klientów lub można go emitować tylko do podłączonych klientów, którzy zostali uwierzytelnieni do danego użytkownika.

Można go również użyć do zarządzania grupami, do których należy użytkownik.

Zobacz przykład specyficzny dla języka:

* [2.xC#](#2x-c-send-message-output-examples)
* [2. x JavaScript](#2x-javascript-send-message-output-examples)
* [2. x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2. x C# Wyślij przykładowe dane wyjściowe komunikatu

#### <a name="broadcast-to-all-clients"></a>Emituj do wszystkich klientów

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która wysyła komunikat przy użyciu powiązania danych wyjściowych do wszystkich połączonych klientów. `Target` Jest nazwą metody, która ma być wywoływana na każdym kliencie. `Arguments` Właściwość jest tablicą zero lub więcej obiektów do przesłania do metody klienta.

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

#### <a name="send-to-a-user"></a>Wyślij do użytkownika

Komunikat można wysłać tylko do połączeń, które zostały uwierzytelnione do użytkownika, ustawiając `UserId` Właściwość komunikatu sygnalizującego.

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

Komunikat można wysłać tylko do połączeń, które zostały dodane do grupy przez ustawienie `GroupName` właściwości komunikatu sygnalizującego.

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

### <a name="2x-c-group-management-output-examples"></a>2. x C# przykłady danych wyjściowych zarządzania grupami

Usługa sygnalizująca pozwala dodawać użytkowników do grup. Komunikaty można następnie wysyłać do grupy. Można użyć `SignalRGroupAction` klasy `SignalR` z powiązaniem danych wyjściowych do zarządzania członkostwem w grupie użytkownika.

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
> Aby można było `ClaimsPrincipal` poprawnie uzyskać powiązane powiązania, należy skonfigurować ustawienia uwierzytelniania w Azure Functions.

### <a name="2x-javascript-send-message-output-examples"></a>Przykłady danych wyjściowych wysyłania komunikatów w języku JavaScript 2. x

#### <a name="broadcast-to-all-clients"></a>Emituj do wszystkich klientów

W poniższym przykładzie przedstawiono powiązanie danych wyjściowych sygnalizujące w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania do wysyłania wiadomości za pomocą usługi Azure Signal Service. Ustaw powiązanie danych wyjściowych z tablicą co najmniej jednego komunikatu sygnalizującego. Komunikat sygnalizujący składa się z `target` właściwości, która określa nazwę metody do wywołania na każdym kliencie `arguments` i właściwość, która jest tablicą obiektów do przekazania do metody klienta jako argumenty.

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

#### <a name="send-to-a-user"></a>Wyślij do użytkownika

Komunikat można wysłać tylko do połączeń, które zostały uwierzytelnione do użytkownika, ustawiając `userId` Właściwość komunikatu sygnalizującego.

*Funkcja. JSON* pozostaje taka sama. Poniżej przedstawiono kod JavaScript:

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

Komunikat można wysłać tylko do połączeń, które zostały dodane do grupy przez ustawienie `groupName` właściwości komunikatu sygnalizującego.

*Funkcja. JSON* pozostaje taka sama. Poniżej przedstawiono kod JavaScript:

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

### <a name="2x-javascript-group-management-output-examples"></a>2. x przykładowe dane wyjściowe zarządzania grupami JavaScript

Usługa sygnalizująca pozwala dodawać użytkowników do grup. Komunikaty można następnie wysyłać do grupy. Możesz użyć `SignalR` powiązania danych wyjściowych do zarządzania członkostwem w grupie użytkownika.

#### <a name="add-user-to-a-group"></a>Dodawanie użytkownika do grupy

Poniższy przykład dodaje użytkownika do grupy.

*Function. JSON*

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

*Function. JSON*

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

### <a name="2x-java-send-message-output-examples"></a>2. x dane wyjściowe wysyłania komunikatu Java

#### <a name="broadcast-to-all-clients"></a>Emituj do wszystkich klientów

Poniższy przykład pokazuje [funkcję języka Java](functions-reference-java.md) , która wysyła komunikat przy użyciu powiązania danych wyjściowych ze wszystkimi połączonymi klientami. `target` Jest nazwą metody, która ma być wywoływana na każdym kliencie. `arguments` Właściwość jest tablicą zero lub więcej obiektów do przesłania do metody klienta.

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

#### <a name="send-to-a-user"></a>Wyślij do użytkownika

Komunikat można wysłać tylko do połączeń, które zostały uwierzytelnione do użytkownika, ustawiając `userId` Właściwość komunikatu sygnalizującego.

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

Komunikat można wysłać tylko do połączeń, które zostały dodane do grupy przez ustawienie `groupName` właściwości komunikatu sygnalizującego.

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

### <a name="2x-java-group-management-output-examples"></a>2. x przykłady danych wyjściowych zarządzania grupami Java

Usługa sygnalizująca pozwala dodawać użytkowników do grup. Komunikaty można następnie wysyłać do grupy. Można użyć `SignalRGroupAction` klasy `SignalROutput` z powiązaniem danych wyjściowych do zarządzania członkostwem w grupie użytkownika.

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
|**name**|| Nazwa zmiennej używana w kodzie funkcji dla obiektu informacji o połączeniu. |
|**hubName**|**HubName**| Ta wartość musi być ustawiona na nazwę centrum sygnałów, dla którego są generowane informacje o połączeniu.|
|**userId**|**UserId**| Opcjonalnie: Wartość żądania identyfikatora użytkownika do ustawienia w tokenie klucza dostępu. |
|**connectionStringSetting**|**ConnectionStringSetting**| Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi sygnalizującego (wartość domyślna to "AzureSignalRConnectionString"). |

### <a name="signalr"></a>SignalR

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `SignalR` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**|| Musi być równa `signalR`.|
|**direction**|| Musi być równa `out`.|
|**name**|| Nazwa zmiennej używana w kodzie funkcji dla obiektu informacji o połączeniu. |
|**hubName**|**HubName**| Ta wartość musi być ustawiona na nazwę centrum sygnałów, dla którego są generowane informacje o połączeniu.|
|**connectionStringSetting**|**ConnectionStringSetting**| Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi sygnalizującego (wartość domyślna to "AzureSignalRConnectionString"). |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Programowanie i konfigurowanie w usłudze Azure Functions za pomocą usługi Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
