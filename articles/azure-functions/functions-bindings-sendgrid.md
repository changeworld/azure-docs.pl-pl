---
title: Powiązania SendGrid usług Azure Functions
description: Odwołanie do powiązań SendGrid usługi Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 9ed2b81c12c698822b9542bb6903189c865b572b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277468"
---
# <a name="azure-functions-sendgrid-bindings"></a>Powiązania SendGrid usług Azure Functions

W tym artykule wyjaśniono, jak wysyłać wiadomości e-mail przy użyciu powiązań [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) w usłudze Azure Functions. Usługa Azure Functions obsługuje powiązanie danych wyjściowych dla sendgrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety - Funkcje 1.x

Powiązania SendGrid znajdują się w pakiecie [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet w wersji 2.x. Kod źródłowy pakietu znajduje się w repozytorium GitHub w [zakresie azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2.x lub nowsze

Powiązania SendGrid znajdują się w pakiecie [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet w wersji 3.x. Kod źródłowy pakietu znajduje się w repozytorium GitHub w [zakresie azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która używa wyzwalacza kolejki usługi Service Bus i powiązania danych wyjściowych SendGrid.

### <a name="synchronous"></a>Synchroniczny

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

### <a name="asynchronous"></a>Asynchroniczne

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

Można pominąć ustawienie `ApiKey` właściwości atrybutu, jeśli masz klucz interfejsu API w ustawieniu aplikacji o nazwie "AzureWebJobsSendGridApiKey".

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie danych wyjściowych SendGrid w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania.

Oto dane powiązania w pliku *function.json:*

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod skryptu języka C#:

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie danych wyjściowych SendGrid w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania.

Oto dane powiązania w pliku *function.json:*

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, input) {
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie przedstawiono funkcję wyzwalaną przez protokół HTTP, która wysyła wiadomość e-mail przy użyciu powiązania SendGrid. Wartości domyślne można podać w konfiguracji powiązania. Na przykład *from* z adresu e-mail jest skonfigurowany w *function.json*. 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "sendGrid",
      "name": "sendGridMessage",
      "direction": "out",
      "apiKey": "SendGrid_API_Key",
      "from": "sender@contoso.com"
    }
  ]
}
```

Poniższa funkcja pokazuje, jak można podać wartości niestandardowych dla właściwości opcjonalnych.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, sendGridMessage: func.Out[str]) -> func.HttpResponse:

    value = "Sent from Azure Functions"

    message = {
        "personalizations": [ {
          "to": [{
            "email": "user@contoso.com"
            }]}],
        "subject": "Azure Functions email with SendGrid",
        "content": [{
            "type": "text/plain",
            "value": value }]}

    sendGridMessage.set(json.dumps(message))

    return func.HttpResponse(f"Sent")
```

# <a name="java"></a>[Java](#tab/java)

W poniższym `@SendGridOutput` przykładzie użyto adnotacji z [biblioteki środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime) do wysyłania wiadomości e-mail przy użyciu powiązania danych wyjściowych SendGrid.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerSendGrid {

    @FunctionName("HttpTriggerSendGrid")
    public HttpResponseMessage run(

        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.GET, HttpMethod.POST },
            authLevel = AuthorizationLevel.FUNCTION)
                HttpRequestMessage<Optional<String>> request,

        @SendGridOutput(
            name = "message",
            dataType = "String",
            apiKey = "SendGrid_API_Key",
            to = "user@contoso.com",
            from = "sender@contoso.com",
            subject = "Azure Functions email with SendGrid",
            text = "Sent from Azure Functions")
                OutputBinding<String> message,

        final ExecutionContext context) {

        final String toAddress = "user@contoso.com";
        final String value = "Sent from Azure Functions";

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"personalizations\": [{ \"to\": [{ \"email\": \"%s\"}]}],")
            .append("\"content\": [{\"type\": \"text/plain\", \"value\": \"%s\"}]")
            .append("}");

        final String body = String.format(builder.toString(), toAddress, value);

        message.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [SendGrid.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs)

Aby uzyskać informacje o właściwościach atrybutów, które można skonfigurować, zobacz [Konfiguracja](#configuration). Oto przykład `SendGrid` atrybutu w podpisie metody:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Aby uzyskać pełny przykład, zobacz [przykład języka C#.](#example)

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Adnotacja [SendGridOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) umożliwia deklaratywne skonfigurowanie powiązania SendGrid przez podanie wartości konfiguracji. Zobacz [przykład](#example) i sekcje [konfiguracji,](#configuration) aby uzyskać więcej szczegółów.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli wymieniono właściwości konfiguracji powiązania dostępne `SendGrid` w pliku *function.json* oraz atrybut/adnotację.

| *właściwość function.json* | Właściwość atrybut/adnotacja | Opis | Optional (Opcjonalność) |
|--------------------------|-------------------------------|-------------|----------|
| type |Nie dotyczy| Musi być `sendGrid`ustawiona na .| Nie |
| kierunek |Nie dotyczy| Musi być `out`ustawiona na .| Nie |
| name |Nie dotyczy| Nazwa zmiennej używana w kodzie funkcji dla treści żądania lub żądania. Ta wartość `$return` jest, gdy istnieje tylko jedna wartość zwracana. | Nie |
| apiKey (klawisz apiKey) | Klawisz ApiKey | Nazwa ustawienia aplikacji zawierającego klucz interfejsu API. Jeśli nie jest ustawiona, domyślną nazwą ustawienia aplikacji jest *AzureWebJobsSendGridApiKey*.| Nie |
| na| Do | Adres e-mail odbiorcy. | Tak |
| Z| Z | Adres e-mail nadawcy. |  Tak |
| Temat| Podmiot | Temat wiadomości e-mail. | Tak |
| tekst| Tekst | Zawartość wiadomości e-mail. | Tak |

Właściwości opcjonalne mogą mieć wartości domyślne zdefiniowane w powiązaniu i dodane lub zastąpione programowo.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>ustawienia host.json

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersjach 2.x lub nowszych. Przykładowy plik host.json poniżej zawiera tylko ustawienia w wersji 2.x+ dla tego powiązania. Aby uzyskać więcej informacji na temat globalnych ustawień konfiguracji w wersjach 2.x i nowszych, zobacz [odwołanie do host.json dla usługi Azure Functions](functions-host-json.md).

> [!NOTE]
> Aby uzyskać odwołanie do pliku host.json w usługach 1.x, zobacz [odwołanie do host.json dla usługi Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|Z|Nie dotyczy|Adres e-mail nadawcy we wszystkich funkcjach.| 


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach funkcji platformy Azure](functions-triggers-bindings.md)
