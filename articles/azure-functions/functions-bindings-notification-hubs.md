---
title: Powiązania centrów powiadomień dla funkcji platformy Azure
description: Dowiedz się, jak używać powiązania usługi Azure Notification Hub w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 211f8c8a203b81a4df6a8e9515b403f99cec572a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277286"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Powiązanie danych wyjściowych centrów powiadomień dla funkcji platformy Azure

W tym artykule wyjaśniono, jak wysyłać powiadomienia wypychane przy użyciu powiązań [usługi Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) w usłudze Azure Functions. Usługa Azure Functions obsługuje powiązania danych wyjściowych dla centrów powiadomień.

Usługi Azure Notification Hubs muszą być skonfigurowane dla usługi powiadomień platformy (PNS), której chcesz użyć. Aby dowiedzieć się, jak otrzymywać powiadomienia wypychane w aplikacji klienckiej z Centrów powiadomień, zobacz Wprowadzenie do [centrów powiadomień](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) i wybierz docelową platformę klienta z listy rozwijanej u góry strony.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Google [ma przestarzały Google Chmura Przekaz (GCM) na rzecz Firebase Cloud Messaging (FCM)](https://developers.google.com/cloud-messaging/faq). To powiązanie danych wyjściowych nie obsługuje fcm. Aby wysyłać powiadomienia za pomocą FCM, użyj [interfejsu API Firebase](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) bezpośrednio w swojej funkcji lub użyj [powiadomień szablonu.](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)

## <a name="packages---functions-1x"></a>Pakiety - Funkcje 1.x

Powiązania Centrów powiadomień znajdują się w pakiecie [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet w wersji 1.x. Kod źródłowy pakietu znajduje się w repozytorium GitHub w [zakresie azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2.x lub nowsze

To powiązanie nie jest dostępne w funkcji 2.x lub nowszych.

## <a name="example---template"></a>Przykład — szablon

Wysyłane powiadomienia mogą być powiadomieniami natywnymi lub [powiadomieniami o szablonach](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Powiadomienia natywne są przeznaczone dla `platform` określonej platformy klienta skonfigurowanej we właściwości powiązania danych wyjściowych. Powiadomienie o szablonie może służyć do kierowania na wiele platform.   

Zobacz przykład specyficzny dla języka:

* [Skrypt C# - parametr out](#c-script-template-example---out-parameter)
* [Skrypt C# - asynchroniczne](#c-script-template-example---asynchronous)
* [Skrypt języka C# — JSON](#c-script-template-example---json)
* [Skrypt C# — typy bibliotek](#c-script-template-example---library-types)
* [F #](#f-template-example)
* [Javascript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Przykład szablonu skryptu języka C# — parametr out

W tym przykładzie wysyła powiadomienie o `message` [rejestracji szablonu,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) który zawiera symbol zastępczy w szablonie.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>Przykład szablonu skryptu języka C# — asynchroniczne

Jeśli używasz kodu asynchroniowego, out parametry nie są dozwolone. W takim `IAsyncCollector` przypadku użyj do zwrócenia powiadomienia o szablonie. Poniższy kod jest asynchroniką przykład kodu powyżej. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>Przykład szablonu skryptu języka C# — JSON

W tym przykładzie wysyła powiadomienie o `message` [rejestracji szablonu,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) który zawiera symbol zastępczy w szablonie przy użyciu prawidłowego ciągu JSON.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>Przykład szablonu skryptu języka C# — typy bibliotek

W tym przykładzie pokazano, jak używać typów zdefiniowanych w [bibliotece Centrów powiadomień platformy Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>Przykład szablonu F#

W tym przykładzie wysyła powiadomienie `location` o `message` [rejestracji szablonu,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) który zawiera i .

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Przykład szablonu JavaScript

W tym przykładzie wysyła powiadomienie `location` o `message` [rejestracji szablonu,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) który zawiera i .

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Przykład — natywny apns

W tym przykładzie skryptu języka C# pokazano, jak wysłać natywne powiadomienie APNS. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Przykład — natywny WNS

W tym przykładzie skryptu języka C# pokazano, jak używać typów zdefiniowanych w [bibliotece centrów powiadomień platformy Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) do wysyłania natywnego powiadomienia wyskakującego WNS. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [NotificationHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs)

Parametry i właściwości konstruktora atrybutu są opisane w sekcji [konfiguracji.](#configuration)

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania ustawione w pliku `NotificationHub` *function.json* i atrybut:

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** |Nie dotyczy| Musi być `notificationHub`ustawiona na . |
|**Kierunku** |Nie dotyczy| Musi być `out`ustawiona na . | 
|**Nazwa** |Nie dotyczy| Nazwa zmiennej używana w kodzie funkcji dla komunikatu centrum powiadomień. |
|**tagExpression (wysmażenie tagexpress** |**TagExpression (wysmażenie znaczników)** | Wyrażenia tagów umożliwiają określenie, że powiadomienia mają być dostarczane do zestawu urządzeń, które zarejestrowały się, aby otrzymywać powiadomienia pasujące do wyrażenia tagu.  Aby uzyskać więcej informacji, zobacz [Routing i wyrażenia znaczników](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**nazwa centrum** | **Nazwa centrum** | Nazwa zasobu centrum powiadomień w witrynie Azure portal. |
|**Połączenia** | **ConnectionStringSetting (Ustawienia połączeń)** | Nazwa ustawienia aplikacji zawierającego parametry połączenia Centra powiadomień.  Parametry połączenia muszą być ustawione na wartość *DefaultFullSharedAccessSignature* dla centrum powiadomień. Zobacz [Konfiguracja ciągu połączenia](#connection-string-setup) w dalszej części tego artykułu.|
|**Platformy** | **Platforma** | Właściwość platformy wskazuje platformę klienta, do tego cele powiadomień. Domyślnie jeśli właściwość platformy zostanie pominięta w powiązaniu danych wyjściowych, powiadomienia o szablonach mogą służyć do kierowania dowolnej platformy skonfigurowanej w usłudze Azure Notification Hub. Aby uzyskać więcej informacji na temat używania szablonów w ogóle do wysyłania powiadomień między platformami za pomocą usługi Azure Notification Hub, zobacz [Szablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Po ustawieniu **platforma** musi być jedną z następujących wartości: <ul><li><code>apns</code>&mdash;Usługa powiadomień push Firmy Apple. Aby uzyskać więcej informacji na temat konfigurowania centrum powiadomień dla usługi APNS i odbierania powiadomień w aplikacji klienckiej, zobacz [Wysyłanie powiadomień wypychanych do systemu iOS za pomocą usługi Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Aby uzyskać więcej informacji na temat konfigurowania centrum powiadomień dla usługi ADM i odbierania powiadomień w aplikacji Kindle, zobacz [Wprowadzenie do centrów powiadomień dla aplikacji Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>wns</code>&mdash;[Usługi powiadomień wypychaowych systemu Windows](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) przeznaczone dla platform Windows. System Windows Phone 8.1 lub nowszy jest również obsługiwany przez WNS. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do centrów powiadomień dla aplikacji platformy uniwersalnej systemu Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Usługa powiadomień wypychaowych firmy Microsoft](/previous-versions/windows/apps/ff402558(v=vs.105)). Ta platforma obsługuje windows phone 8 i wcześniejsze platformy Windows Phone. Aby uzyskać więcej informacji, zobacz [Wysyłanie powiadomień wypychanych za pomocą usługi Azure Notification Hubs w systemie Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>przykład pliku function.json

Oto przykład powiązania centrum powiadomień w pliku *function.json.*

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Konfiguracja ciągu połączenia

Aby użyć powiązania wyjściowego centrum powiadomień, należy skonfigurować parametry połączenia dla koncentratora. Możesz wybrać istniejące centrum powiadomień lub utworzyć nowe bezpośrednio z karty *Integruj* w witrynie Azure portal. Ciąg połączenia można również skonfigurować ręcznie. 

Aby skonfigurować parametry połączenia do istniejącego centrum powiadomień:

1. Przejdź do centrum powiadomień w [witrynie Azure portal](https://portal.azure.com), wybierz **pozycję Zasady programu Access**i wybierz przycisk kopiowania obok zasady **DefaultFullSharedAccessSignature.** Spowoduje to skopiowanie ciągu połączenia dla zasad *DefaultFullSharedAccessSignature* do centrum powiadomień. Ten ciąg połączenia umożliwia funkcji wysyłania wiadomości powiadomień do centrum.
    ![Kopiowanie ciągu połączenia koncentratora powiadomień](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Przejdź do aplikacji funkcji w witrynie Azure portal, wybierz **pozycję Ustawienia aplikacji,** dodaj klucz, taki jak **MyHubConnectionString,** wklej skopiowaną *domyślną funkcję DefaultlSharedAccessSignature* dla centrum powiadomień jako wartość, a następnie kliknij przycisk **Zapisz**.

Nazwa tego ustawienia aplikacji jest to, co idzie w ustawienia połączenia powiązania danych wyjściowych w *function.json* lub atrybutu .NET. Zobacz [sekcję Konfiguracja](#configuration) wcześniej w tym artykule.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody zwrotne

| Wiązanie | Tematy pomocy |
|---|---|
| Centrum powiadomień | [Przewodnik po operacjach](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach funkcji platformy Azure](functions-triggers-bindings.md)

