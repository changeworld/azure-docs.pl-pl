---
title: Usługa Azure powiązania usługi SendGrid funkcji
description: Dokumentacja usługi Azure powiązania usługi SendGrid funkcji.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: glenga
ms.openlocfilehash: 79fb24e85dea5a8d8d9ca637612ea4a65339a4e3
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087416"
---
# <a name="azure-functions-sendgrid-bindings"></a>Usługa Azure powiązania usługi SendGrid funkcji

W tym artykule wyjaśniono, jak wysyłać pocztę e-mail przy użyciu [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) powiązań w usłudze Azure Functions. Usługa Azure Functions obsługuje powiązanie danych wyjściowych dla usługi SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania usługi SendGrid znajdują się w [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) pakietu NuGet w wersji 2.x. Kod źródłowy dla pakietu znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania usługi SendGrid znajdują się w [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) pakietu NuGet w wersji 3.x. Kod źródłowy dla pakietu znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Przykład

Zobacz przykład specyficzny dla języka:

* [C#](#c-example)
* [Skryptu C# (csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) że wyzwolić kolejki usługi Service Bus używa i powiązania danych wyjściowych usługi SendGrid.

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
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
            "name": "message",
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

Poniżej przedstawiono kod skryptu języka C#:

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;
using Microsoft.Extensions.Logging;

public static void Run(ILogger log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
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
|**Nazwa**|| Wymagana — nazwa zmiennej, używane w kodzie funkcji żądania lub treści żądania. Ta wartość jest ```$return``` gdy istnieje tylko jedna wartość zwracaną. |
|**apiKey**|**ApiKey**| Nazwa ustawienia aplikacji zawierającego klucz interfejsu API. Jeśli nie jest ustawiony, ustawienie aplikacji domyślną jest "AzureWebJobsSendGridApiKey".|
|**Aby**|**Do**| adres e-mail adresata. |
|**Z**|**From**| adres e-mail nadawcy. |
|**subject**|**Temat**| temat wiadomości e-mail. |
|**Tekst**|**Tekst**| Treść wiadomości e-mail. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
