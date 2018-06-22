---
title: Powiązanie usługi Twilio funkcji platformy Azure
description: Zrozumienie sposobu korzystania z usługi Azure Functions powiązania usługi Twilio.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: funkcje usługi Azure, funkcje, przetwarzania zdarzeń, dynamiczne obliczeń niekorzystającą architektury
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: tdykstra
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4ab0ea0726031a1db36f4fbc4ee1db8a0c7f07bf
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308279"
---
# <a name="twilio-binding-for-azure-functions"></a>Powiązanie usługi Twilio dla usługi Azure Functions

W tym artykule wyjaśniono, jak wysyłać wiadomości tekstowych przy użyciu [usługi Twilio](https://www.twilio.com/) powiązania usługi Azure Functions. Azure funkcji obsługuje dane wyjściowe powiązania usługi Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania usługi Twilio znajdują się w [Microsoft.Azure.WebJobs.Extensions.Twilio](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) pakietu NuGet, wersja 1.x. Kod źródłowy dla pakietu jest w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania usługi Twilio znajdują się w [Microsoft.Azure.WebJobs.Extensions.Twilio](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) pakietu NuGet w wersji 3.x. Kod źródłowy dla pakietu jest w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#c-example)
* [Skryptu C# (csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) który wysyła komunikat tekstowy wyzwolenia komunikatu w kolejce.

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

W tym przykładzie użyto `TwilioSms` atrybutu z wartością zwracaną metody. Alternatywą jest użycie atrybutu o `out SMSMessage` parametru lub `ICollector<SMSMessage>` lub `IAsyncCollector<SMSMessage>` parametru.

### <a name="c-script-example"></a>Przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono dane wyjściowe z usługi Twilio powiązanie w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja używa `out` parametr, aby wysłać wiadomość SMS.

W tym miejscu jest powiązanie danych *function.json* pliku:

Przykład function.json:

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

Oto kod skryptu C#:

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

Nie można używać parametrów w kodzie asynchroniczne out. Oto asynchroniczne C# kod przykładowy skrypt:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

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

W poniższym przykładzie przedstawiono dane wyjściowe z usługi Twilio powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania.

W tym miejscu jest powiązanie danych *function.json* pliku:

Przykład function.json:

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

Oto kod JavaScript:

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

## <a name="attributes"></a>Atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) atrybutu.

Informacje o właściwości atrybutów, które można skonfigurować, zobacz [konfiguracji](#configuration). Oto `TwilioSms` przykład atrybutu w podpisie metody:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(
    AccountSidSetting = "TwilioAccountSid", 
    AuthTokenSetting = "TwilioAuthToken", 
    From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    ...
}
 ```

Pełny przykład, zobacz [przykład C#](#c-example).

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `TwilioSms` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type**|| należy wybrać opcję `twilioSms`.|
|**direction**|| należy wybrać opcję `out`.|
|**Nazwa**|| Nazwa zmiennej używane w kodzie funkcja wiadomości SMS usługi Twilio. |
|**accountSid**|**AccountSid**| Ta wartość musi mieć ustawioną nazwę ustawienia aplikacji obsługującej Twojego identyfikatora Sid konta usługi Twilio.|
|**authToken**|**Parametr AuthToken**| Ta wartość musi mieć ustawioną Nazwa ustawienia aplikacji, który zawiera token uwierzytelniania usługi Twilio.|
|**Aby**|**Do**| Ta wartość jest równa numer telefonu, który tekst wiadomości SMS są wysyłane do.|
|**Z**|**From**| Ta wartość jest równa numer telefonu, który tekst wiadomości SMS są wysyłane z.|
|**body**|**Treść**| Ta wartość może być używana twardego kodu wiadomość SMS, jeśli nie trzeba ustawić ją dynamicznie w kodzie dla funkcji. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)


