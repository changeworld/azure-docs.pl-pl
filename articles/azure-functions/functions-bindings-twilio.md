---
title: Azure Functions powiązania Twilio
description: Dowiedz się, jak używać powiązań Twilio z Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee78234b43e94a5c537161556410ee6246e625ca
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230939"
---
# <a name="twilio-binding-for-azure-functions"></a>Twilio powiązanie dla Azure Functions

W tym artykule wyjaśniono, jak wysyłać wiadomości SMS przy użyciu powiązań [Twilio](https://www.twilio.com/) w Azure Functions. Azure Functions obsługuje powiązania wyjściowe dla Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania Twilio są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) w wersji 1. x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania Twilio są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) w wersji 3. x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example---functions-1x"></a>Przykład — Functions 1. x

Zobacz przykład specyficzny dla języka:

* [C#](#c-example)
* [Skryptu C# (csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C#przyklad

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która wysyła wiadomość tekstową, gdy jest wyzwalany przez komunikat w kolejce.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

W tym przykładzie zastosowano atrybut `TwilioSms` z wartością zwracaną metody. Alternatywą jest użycie atrybutu z parametrem `out SMSMessage` lub parametrem `ICollector<SMSMessage>` lub `IAsyncCollector<SMSMessage>`.

### <a name="c-script-example"></a>C#przykład skryptu

Poniższy przykład przedstawia powiązanie danych wyjściowych Twilio w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja używa `out` parametru do wysłania wiadomości tekstowej.

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Kod C# skryptu:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

Nie można używać parametrów out w kodzie asynchronicznym. Oto przykład kodu skryptu C# asynchronicznego:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>Przykład JavaScript

Poniższy przykład przedstawia powiązanie danych wyjściowych Twilio w pliku *Function. JSON* i [funkcję języka JavaScript](functions-reference-node.md) , która używa powiązania.

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="example---functions-2x"></a>Przykład — Functions 2. x

Zobacz przykład specyficzny dla języka:

* [2.xC#](#2x-c-example)
* [skrypt 2. C# x (. CSX)](#2x-c-script-example)
* [2. x JavaScript](#2x-javascript-example)

### <a name="2x-c-example"></a>przykład 2. C# x

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która wysyła wiadomość tekstową, gdy jest wyzwalany przez komunikat w kolejce.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

W tym przykładzie zastosowano atrybut `TwilioSms` z wartością zwracaną metody. Alternatywą jest użycie atrybutu z parametrem `out CreateMessageOptions` lub parametrem `ICollector<CreateMessageOptions>` lub `IAsyncCollector<CreateMessageOptions>`.

### <a name="2x-c-script-example"></a>Przykładowy skrypt 2 C# . x

Poniższy przykład przedstawia powiązanie danych wyjściowych Twilio w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja używa `out` parametru do wysłania wiadomości tekstowej.

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Kod C# skryptu:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

Nie można używać parametrów out w kodzie asynchronicznym. Oto przykład kodu skryptu C# asynchronicznego:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

### <a name="2x-javascript-example"></a>przykład JavaScript 2. x

Poniższy przykład przedstawia powiązanie danych wyjściowych Twilio w pliku *Function. JSON* i [funkcję języka JavaScript](functions-reference-node.md) , która używa powiązania.

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>Atrybuty

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) .

Aby uzyskać informacje na temat właściwości atrybutów, które można skonfigurować, zobacz [Konfiguracja](#configuration). Oto `TwilioSms` przykład atrybutu w podpisie metody:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Aby zapoznać się z pełnym przykładem, zobacz [ C# przykład](#c-example).

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `TwilioSms` atrybutu.

| Funkcja v1. JSON — Właściwość | v2 Function. JSON — Właściwość | Właściwość atrybutu |Opis|
|---------|---------|---------|----------------------|
|**type**|**type**| Musi być ustawiony na `twilioSms`.|
|**direction**|**direction**| Musi być ustawiony na `out`.|
|**name**|**name**| Nazwa zmiennej używana w kodzie funkcji dla wiadomości tekstowej SMS Twilio. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Ta wartość musi być ustawiona na nazwę ustawienia aplikacji, która zawiera identyfikator SID konta Twilio, np. TwilioAccountSid. Jeśli nie zostanie ustawiona, domyślna nazwa ustawienia aplikacji to "AzureWebJobsTwilioAccountSid". |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Ta wartość musi być ustawiona na nazwę ustawienia aplikacji, która zawiera token uwierzytelniania Twilio, np. TwilioAccountAuthToken. Jeśli nie zostanie ustawiona, domyślna nazwa ustawienia aplikacji to "AzureWebJobsTwilioAuthToken". |
|**to**| Nie dotyczy — Określ w kodzie | **Do**| Ta wartość jest ustawiona na numer telefonu, na który jest wysyłany tekst SMS.|
|**from**|**from** | **From**| Ta wartość jest ustawiona na numer telefonu, z którego jest wysyłany tekst SMS.|
|**body**|**body** | **Treść**| Ta wartość może służyć do napełnienia kodu wiadomości tekstowej SMS, jeśli nie musisz ustawiać jej dynamicznie w kodzie funkcji. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
