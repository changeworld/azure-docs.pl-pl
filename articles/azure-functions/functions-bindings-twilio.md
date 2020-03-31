---
title: Powiązanie usługi Azure Functions Twilio
description: Dowiedz się, jak używać powiązań usługi Twilio za pomocą usługi Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1426d6e770cca566c4b77ca4742e2f8a0fbb5465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715064"
---
# <a name="twilio-binding-for-azure-functions"></a>Powiązanie usługi Twilio dla funkcji platformy Azure

W tym artykule wyjaśniono, jak wysyłać wiadomości tekstowe przy użyciu powiązań [usługi Twilio](https://www.twilio.com/) w usłudze Azure Functions. Usługa Azure Functions obsługuje powiązania danych wyjściowych dla usługi Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety - Funkcje 1.x

Powiązania usługi Twilio znajdują się w pakiecie [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet w wersji 1.x. Kod źródłowy pakietu znajduje się w repozytorium [GitHub azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2.x lub nowsze

Powiązania usługi Twilio znajdują się w pakiecie [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet w wersji 3.x. Kod źródłowy pakietu znajduje się w repozytorium [GitHub azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>Przykład — funkcje 2.x lub nowsze

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która wysyła wiadomość tekstową po wyzwoleniu przez komunikat kolejki.

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

W tym przykładzie `TwilioSms` użyto atrybutu z wartością zwracaną metody. Alternatywą jest użycie atrybutu `out CreateMessageOptions` z parametrem `IAsyncCollector<CreateMessageOptions>` lub parametrem. `ICollector<CreateMessageOptions>`

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie przedstawiono powiązanie danych wyjściowych usługi Twilio w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja używa parametru `out` do wysyłania wiadomości tekstowych.

Oto dane powiązania w pliku *function.json:*

Przykładowa funkcja.json:

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

Oto kod skryptu języka C#:

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

Nie można użyć parametrów w kodzie asynchroniza. Oto przykład kodu skryptu asynchronizajnego języka C#:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie przedstawiono powiązanie wyjściowe usługi Twilio w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania.

Oto dane powiązania w pliku *function.json:*

Przykładowa funkcja.json:

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

Oto kod JavaScript:

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

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano, jak wysłać wiadomość SMS przy użyciu powiązania wyjściowego zdefiniowanego w następującej *funkcji.js*.

```json
    {
      "type": "twilioSms",
      "name": "twilioMessage",
      "accountSidSetting": "TwilioAccountSID",
      "authTokenSetting": "TwilioAuthToken",
      "from": "+1XXXXXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }
```

Można przekazać seryjny obiekt JSON `func.Out` do parametru, aby wysłać wiadomość SMS.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, twilioMessage: func.Out[str]) -> func.HttpResponse:

    message = req.params.get('message')
    to = req.params.get('to')

    value = {
      "body": message,
      "to": to
    }

    twilioMessage.set(json.dumps(value))

    return func.HttpResponse(f"Message sent")
```

# <a name="java"></a>[Java](#tab/java)

W poniższym przykładzie pokazano, jak użyć adnotacji [TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) do wysyłania wiadomości SMS. Wartości `to`dla `from`, `body` i są wymagane w definicji atrybutu, nawet jeśli zastąpić je programowo.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class TwilioOutput {

    @FunctionName("TwilioOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST },
                authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @TwilioSmsOutput(
                name = "twilioMessage",
                accountSid = "AzureWebJobsTwilioAccountSID",
                authToken = "AzureWebJobsTwilioAuthToken",
                to = "+1XXXXXXXXXX",
                body = "From Azure Functions",
                from = "+1XXXXXXXXXX") OutputBinding<String> twilioMessage,
            final ExecutionContext context) {

        String message = request.getQueryParameters().get("message");
        String to = request.getQueryParameters().get("to");

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"body\": \"%s\",")
            .append("\"to\": \"%s\"")
            .append("}");

        final String body = String.format(builder.toString(), message, to);

        twilioMessage.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Message sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [TwilioSms.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs)

Aby uzyskać informacje o właściwościach atrybutów, które można skonfigurować, zobacz [Konfiguracja](#configuration). Oto przykład `TwilioSms` atrybutu w podpisie metody:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
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

Umieść [adnotację TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) na parametrze, [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.outputbinding) w `T` którym `int` `String`może `byte[]`być dowolny natywny typ Java, taki jak , lub typ POJO.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `TwilioSms` ustawić w pliku *function.json* i atrybut.

| v1 function.json właściwość | v2 function.json właściwość | Właściwość atrybutu |Opis|
|---------|---------|---------|----------------------|
|**Typu**|**Typu**| musi być `twilioSms`ustawiona na .|
|**Kierunku**|**Kierunku**| musi być `out`ustawiona na .|
|**Nazwa**|**Nazwa**| Nazwa zmiennej używana w kodzie funkcji dla wiadomości tekstowej SMS usługi Twilio. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Wartość ta musi być ustawiona na nazwę ustawienia aplikacji, które`TwilioAccountSid`przechowuje identyfikator Sid konta usługi Twilio ( ). Jeśli nie jest ustawiona, domyślna nazwa ustawienia aplikacji to "AzureWebJobsTwilioAccountSid". |
|**authToken (authToken)**|**authTokenSetting**|**AuthTokenSetting**| Ta wartość musi być ustawiona na nazwę ustawienia aplikacji, które`TwilioAccountAuthToken`przechowuje token uwierzytelniania usługi Twilio ( ). Jeśli nie jest ustawiona, domyślna nazwa ustawienia aplikacji to "AzureWebJobsTwilioAuthToken". |
|**do**| Nie dotyczy - określ w kodzie | **Do**| Ta wartość jest ustawiona na numer telefonu, na który jest wysyłany tekst SMS.|
|**Z**|**Z** | **Od**| Ta wartość jest ustawiona na numer telefonu, z których wysyłany jest tekst SMS.|
|**Ciała**|**Ciała** | **Treść**| Ta wartość może służyć do kodu twardego wiadomości tekstowej SMS, jeśli nie trzeba ustawić go dynamicznie w kodzie dla funkcji. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach funkcji platformy Azure](functions-triggers-bindings.md)
