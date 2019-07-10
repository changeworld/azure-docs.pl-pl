---
title: Usługa Azure powiązania usługi SendGrid funkcji
description: Dokumentacja usługi Azure powiązania usługi SendGrid funkcji.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 8b7666b043379f3ff143e2a5eaae6b40ea80ab90
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480249"
---
# <a name="azure-functions-sendgrid-bindings"></a>Usługa Azure powiązania usługi SendGrid funkcji

W tym artykule wyjaśniono, jak wysyłać pocztę e-mail przy użyciu [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) powiązań w usłudze Azure Functions. Usługa Azure Functions obsługuje powiązanie danych wyjściowych dla usługi SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania usługi SendGrid znajdują się w [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) pakietu NuGet w wersji 2.x. Kod źródłowy dla pakietu znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania usługi SendGrid znajdują się w [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) pakietu NuGet w wersji 3.x. Kod źródłowy dla pakietu znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Przykład

Zobacz przykład specyficzny dla języka:

* [C#](#c-example)
* [Skryptu C# (csx)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>Przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) że wyzwolić kolejki usługi Service Bus używa i powiązania danych wyjściowych usługi SendGrid.

#### <a name="synchronous-c-example"></a>Synchroniczne C# przykładu:

```cs
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
#### <a name="asynchronous-c-example"></a>Asynchroniczne C# przykładu:

```cs
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

Można pominąć, ustawienie atrybutu `ApiKey` właściwość, jeśli masz klucz interfejsu API w ustawieniu aplikacji o nazwie "AzureWebJobsSendGridApiKey".

### <a name="c-script-example"></a>Przykładowy skrypt w języku C#

Poniższy przykład pokazuje usługi SendGrid powiązania danych wyjściowych usługi w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa.

Oto powiązanie danych w *function.json* pliku:

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

[Konfiguracji](#configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

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

### <a name="java-example"></a>Przykładzie w języku Java

W poniższym przykładzie użyto `@SendGridOutput` adnotację [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime) Aby wysłać wiadomość e-mail za pomocą usługi SendGrid powiązania danych wyjściowych.

```java
@FunctionName("SendEmail")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @SendGridOutput(
                name = "email", dataType = "String", apiKey = "SendGridConnection", to = "test@example.com", from = "test@example.com",
                subject= "Sending with SendGrid", text = "Hello from Azure Functions"
                ) OutputBinding<String> email
            )
    {
        String name = request.getBody().orElse("World");

        final String emailBody = "{\"personalizations\":" +
                                    "[{\"to\":[{\"email\":\"test@example.com\"}]," +
                                    "\"subject\":\"Sending with SendGrid\"}]," +
                                    "\"from\":{\"email\":\"test@example.com\"}," +
                                    "\"content\":[{\"type\":\"text/plain\",\"value\": \"Hello" + name + "\"}]}";

        email.setValue(emailBody);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
```

### <a name="javascript-example"></a>Przykład JavaScript

Poniższy przykład pokazuje usługi SendGrid powiązania danych wyjściowych usługi w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa.

Oto powiązanie danych w *function.json* pliku:

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

[Konfiguracji](#configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

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

## <a name="attributes"></a>Atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) atrybutu.

Aby uzyskać informacji na temat właściwości atrybutów, które można skonfigurować, zobacz [konfiguracji](#configuration). Oto `SendGrid` przykład atrybutu w podpisie metody:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Aby uzyskać kompletny przykład, zobacz [przykład w języku C#](#c-example).

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `SendGrid` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**|| Wymagana — musi być równa `sendGrid`.|
|**direction**|| Wymagana — musi być równa `out`.|
|**name**|| Wymagana — nazwa zmiennej, używane w kodzie funkcji żądania lub treści żądania. Ta wartość jest ```$return``` gdy istnieje tylko jedna wartość zwracaną. |
|**apiKey**|**ApiKey**| Nazwa ustawienia aplikacji zawierającego klucz interfejsu API. Jeśli nie jest ustawiony, ustawienie aplikacji domyślną jest "AzureWebJobsSendGridApiKey".|
|**to**|**To**| adres e-mail adresata. |
|**from**|**From**| adres e-mail nadawcy. |
|**subject**|**Subject**| temat wiadomości e-mail. |
|**text**|**Text**| Treść wiadomości e-mail. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Ustawienia host.JSON

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2.x. Przykład pliku host.json poniżej zawiera tylko ustawienia 2.x wersji dla tego powiązania. Aby uzyskać więcej informacji na temat globalne ustawienia konfiguracji w wersji 2.x, zobacz [dokumentacja pliku host.JSON dla usługi Azure Functions w wersji 2.x](functions-host-json.md).

> [!NOTE]
> Odwołanie host.json w funkcjach 1.x, zobacz [dokumentacja pliku host.JSON dla usługi Azure Functions 1.x](functions-host-json-v1.md).

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
|from|Nie dotyczy|Adres e-mail nadawcy dla wszystkich funkcji.| 


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
