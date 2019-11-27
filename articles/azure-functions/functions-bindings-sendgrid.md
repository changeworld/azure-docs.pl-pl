---
title: Azure Functions powiązania SendGrid
description: Azure Functions odwołanie do powiązań SendGrid.
author: craigshoemaker
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 997c9427883e2a099c2c185b618701fb85cb96a6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231086"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions powiązania SendGrid

W tym artykule wyjaśniono, jak wysyłać wiadomości e-mail przy użyciu powiązań [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) w Azure Functions. Azure Functions obsługuje powiązanie danych wyjściowych dla SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania SendGrid są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) w wersji 2. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania SendGrid są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) w wersji 3. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) GitHub.

> [!NOTE]
> Wersja 2. x nie tworzy tematu ani subskrypcji skonfigurowanej w wystąpieniu `ServiceBusTrigger`. Wersja 2. x jest oparta na [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) i nie obsługuje zarządzania kolejki.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Przykład

Zobacz przykład specyficzny dla języka:

* [C#](#c-example)
* [C#skrypt (. CSX)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>C#przyklad

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która używa wyzwalacza kolejki Service Bus i powiązania danych wyjściowych SendGrid.

#### <a name="synchronous-c-example"></a>Przykład C# synchroniczny:

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
#### <a name="asynchronous-c-example"></a>Przykład C# asynchroniczny:

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

Możesz pominąć ustawienie właściwości `ApiKey` atrybutu, jeśli masz klucz interfejsu API w ustawieniu aplikacji o nazwie "AzureWebJobsSendGridApiKey".

### <a name="c-script-example"></a>C#przykład skryptu

Poniższy przykład przedstawia powiązanie danych wyjściowych SendGrid w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania.

Oto dane powiązania w pliku *Function. JSON* :

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

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

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

### <a name="java-example"></a>Przykład Java

W poniższym przykładzie użyto adnotacji `@SendGridOutput` z [biblioteki środowiska uruchomieniowego usługi Java Functions](/java/api/overview/azure/functions/runtime) do wysłania wiadomości e-mail za pomocą powiązania danych wyjściowych SendGrid.

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

Poniższy przykład przedstawia powiązanie danych wyjściowych SendGrid w pliku *Function. JSON* i [funkcję języka JavaScript](functions-reference-node.md) , która używa powiązania.

Oto dane powiązania w pliku *Function. JSON* :

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

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

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

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) .

Aby uzyskać informacje na temat właściwości atrybutów, które można skonfigurować, zobacz [Konfiguracja](#configuration). Oto przykład atrybutu `SendGrid` w sygnaturze metody:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Aby zapoznać się z pełnym przykładem, zobacz [ C# przykład](#c-example).

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `SendGrid` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**|| Wymagane — musi być ustawiony na `sendGrid`.|
|**direction**|| Wymagane — musi być ustawiony na `out`.|
|**Nazwij**|| Wymagane — nazwa zmiennej używana w kodzie funkcji dla żądania lub treści żądania. Ta wartość jest ```$return```, gdy istnieje tylko jedna wartość zwracana. |
|**apiKey**|**ApiKey**| Nazwa ustawienia aplikacji, która zawiera klucz interfejsu API. Jeśli nie zostanie ustawiona, domyślna nazwa ustawienia aplikacji to "AzureWebJobsSendGridApiKey".|
|**do**|**Do**| adres e-mail adresata. |
|**wniosek**|**Wniosek**| Adres e-mail nadawcy. |
|**Temat**|**Temat**| temat wiadomości e-mail. |
|**Opis**|**Tekst**| zawartość wiadomości e-mail. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Ustawienia host.JSON

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2.x. Przykład pliku host.json poniżej zawiera tylko ustawienia 2.x wersji dla tego powiązania. Aby uzyskać więcej informacji na temat ustawień konfiguracji globalnej w wersji 2. x, zobacz informacje dotyczące pliku [host. JSON dla Azure Functions wersji 2. x](functions-host-json.md).

> [!NOTE]
> Aby uzyskać odwołanie do pliku host. JSON w funkcjach 1. x, zobacz informacje dotyczące pliku [host. JSON dla Azure Functions 1. x](functions-host-json-v1.md).

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
|from|Nie dotyczy|Adres e-mail nadawcy we wszystkich funkcjach.| 


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)
