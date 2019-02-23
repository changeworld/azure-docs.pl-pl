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
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/23/2018
ms.author: cshoe
ms.openlocfilehash: 73fd388ad8d35543138c06b413cd40d7052806a7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735648"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Powiązania usługi SignalR Service dla usługi Azure Functions

W tym artykule wyjaśniono, jak uwierzytelniać i wysyłanie komunikatów w czasie rzeczywistym do klientów dołączonych do [usługi Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) przy użyciu usługi SignalR powiązań w usłudze Azure Functions. Usługi Azure Functions obsługuje danych wejściowych i wyjściowych powiązań dla usług SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania usługi SignalR znajdują się w [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) pakietu NuGet, 1.0.0-preview1-* wersji. Kod źródłowy dla pakietu znajduje się w [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) repozytorium GitHub.

> [!NOTE]
> Usługi Azure SignalR Service jest ogólnie dostępna. Powiązania usługi SignalR dla usługi Azure Functions są jednak obecnie w wersji zapoznawczej.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

## <a name="signalr-connection-info-input-binding"></a>Powiązania danych wejściowych informacje połączenia SignalR

Zanim klient może nawiązać połączenie usługi Azure SignalR Service, należy pobrać, adres URL punktu końcowego usługi i prawidłowym tokenem dostępu. *SignalRConnectionInfo* powiązania danych wejściowych generuje adres URL punktu końcowego usługi SignalR i prawidłowy token, które są używane do połączenia z usługą. Ponieważ token jest ograniczony czasowo i może służyć do uwierzytelniania określonego użytkownika na połączenie, nie należy pamięci podręcznej tokenu lub udostępniać je między klientami. Wyzwalacz HTTP używa tego powiązania może służyć przez klientów można pobrać informacji o połączeniu.

Zobacz przykład specyficzny dla języka:

* [2.x C#](#2x-c-input-example)
* [2.x JavaScript](#2x-javascript-input-example)

### <a name="2x-c-input-example"></a>przykład danych wejściowych 2.x języka C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , uzyskuje informacje dotyczące połączenia SignalR przy użyciu powiązania danych wejściowych i zwraca go za pośrednictwem protokołu HTTP.

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Uwierzytelniony tokenów

Jeśli funkcja jest wyzwalana przez uwierzytelnionego klienta, możesz dodać oświadczenie Identyfikatora użytkownika do wygenerowanego tokenu. Można łatwo dodać uwierzytelnianie do aplikacji funkcji przy użyciu [uwierzytelnianie usługi App Service] (.. /App-Service/overview-Authentication-Authorization.MD).

Uwierzytelnianie usługi App Service ustawia nagłówki HTTP o nazwie `x-ms-client-principal-id` i `x-ms-client-principal-name` zawierające odpowiednio identyfikator podmiotu zabezpieczeń klienta i nazwa uwierzytelnionego użytkownika. Możesz ustawić `UserId` właściwości powiązania wartości z przy użyciu nagłówka [powiązanie wyrażenie](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}`. 

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-example"></a>przykład danych wejściowych JavaScript 2.x

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
module.exports = function (context, req, connectionInfo) {
    context.res = { body: connectionInfo };
    context.done();
};
```

#### <a name="authenticated-tokens"></a>Uwierzytelniony tokenów

Jeśli funkcja jest wyzwalana przez uwierzytelnionego klienta, możesz dodać oświadczenie Identyfikatora użytkownika do wygenerowanego tokenu. Można łatwo dodać uwierzytelnianie do aplikacji funkcji przy użyciu [uwierzytelnianie usługi App Service] (.. /App-Service/overview-Authentication-Authorization.MD).

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
module.exports = function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier 
    // claim set to the authenticated user
    context.res = { body: connectionInfo };
    context.done();
};
```

## <a name="signalr-output-binding"></a>Powiązanie danych wyjściowych SignalR

Użyj *SignalR* powiązania do wysyłania komunikatów co najmniej jeden, za pomocą usługi Azure SignalR Service danych wyjściowych. Może emitować wiadomość do wszystkich połączonych klientów lub emitowanie jej tylko do połączonych klientów, które zostały uwierzytelnione do danego użytkownika.

Zobacz przykład specyficzny dla języka:

* [2.x C#](#2x-c-output-example)
* [2.x JavaScript](#2x-javascript-output-example)

### <a name="2x-c-output-example"></a>przykład danych wyjściowych 2.x języka C#

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
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

### <a name="2x-javascript-output-example"></a>przykład danych wyjściowych JavaScript 2.x

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
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

#### <a name="send-to-a-user"></a>Wysłać do użytkownika

Możesz wysłać wiadomość tylko do połączeń, które zostały uwierzytelnione, ustawiając dla użytkownika `userId` właściwości komunikatu SignalR.

*Function.JSON* pozostaje taka sama. Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to these user IDs
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

## <a name="configuration"></a>Konfigurowanie

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `SignalRConnectionInfo` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**|| Musi być równa `signalRConnectionInfo`.|
|**direction**|| Musi być równa `in`.|
|**Nazwa**|| Nazwa zmiennej użytą w kodzie funkcji dla obiektu informacje o połączeniu. |
|**hubName**|**HubName**| Ta wartość musi być równa nazwę Centrum SignalR, dla którego jest generowane informacje o połączeniu.|
|**userId**|**UserId**| Opcjonalnie: Wartość identyfikatora użytkownika oświadczenia w tokenie klucza dostępu. |
|**connectionStringSetting**|**ConnectionStringSetting**| Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi SignalR (wartość domyślna to "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `SignalR` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**|| Musi być równa `signalR`.|
|**direction**|| Musi być równa `out`.|
|**Nazwa**|| Nazwa zmiennej użytą w kodzie funkcji dla obiektu informacje o połączeniu. |
|**hubName**|**HubName**| Ta wartość musi być równa nazwę Centrum SignalR, dla którego jest generowane informacje o połączeniu.|
|**connectionStringSetting**|**ConnectionStringSetting**| Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi SignalR (wartość domyślna to "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

