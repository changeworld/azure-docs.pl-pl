---
title: Powiązania centra powiadomień dla usługi Azure Functions
description: Zrozumienie, jak używać Centrum powiadomień Azure powiązania w usługi Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
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
ms.author: glenga
ms.openlocfilehash: 4208a7be3c002bcefed273015d002cb1aee0fecd
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724979"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Centra powiadomień w danych wyjściowych wiązanie dla usługi Azure Functions

W tym artykule wyjaśniono, jak wysyłać powiadomienia wypychane przy użyciu [usługi Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) powiązania usługi Azure Functions. Azure funkcji obsługuje dane wyjściowe powiązania usługi Notification Hubs.

Centra powiadomień Azure musi być skonfigurowana dla platformy powiadomienia usługi (PNS) ma być używany. Aby dowiedzieć się, jak otrzymywać powiadomień wypychanych w aplikacji klienta z usługi Notification Hubs, zobacz [wprowadzenie do korzystania z usługi Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) i wybierz z listy rozwijanej w górnej części strony docelowej platformy klienta.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania usługi Notification Hubs znajdują się w [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) pakietu NuGet, wersja 1.x. Kod źródłowy dla pakietu jest w [azure-zadań webjob sdk rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

To powiązanie nie jest dostępna w funkcji 2.x.

## <a name="example---template"></a>Przykład — szablonu

Możesz wysłać powiadomienia mogą być natywnych powiadomień lub [szablonu powiadomienia](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Natywnych powiadomień Docelowa platforma określonego klienta zgodnie z konfiguracją `platform` właściwości powiązania danych wyjściowych. Powiadomienie o szablonu może służyć do wielu platform docelowych.   

Zapoznaj się z przykładem specyficzny dla języka:

* [C# skrypt - parametru out](#c-script-template-example---out-parameter)
* [C# skrypt - asynchroniczne](#c-script-template-example---asynchronous)
* [C# skrypt - JSON](#c-script-template-example---json)
* [Skryptu C# — Biblioteka typów](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# przykładowy szablon skrypt - parametru out

W tym przykładzie wysyła powiadomienie [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierający `message` symbolu zastępczego w szablonie.

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

### <a name="c-script-template-example---asynchronous"></a>C# szablonu przykładowy skrypt - asynchroniczne

Jeśli używasz kodu asynchroniczne parametry wyjściowe nie są dozwolone. W takim przypadku użyj `IAsyncCollector` do zwrócenia szablonu powiadomienia. Następujący kod jest przykładem asynchroniczne kodu powyżej. 

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

### <a name="c-script-template-example---json"></a>C# szablonu przykładowy skrypt - JSON

W tym przykładzie wysyła powiadomienie [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierający `message` symbolu zastępczego w szablonie przy użyciu prawidłowego ciągu JSON.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# szablonu przykładowy skrypt - biblioteki typów

W tym przykładzie przedstawiono użycie typów zdefiniowanych w [Biblioteka programu Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

### <a name="f-template-example"></a>Przykład szablonu F #

W tym przykładzie wysyła powiadomienie [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierający `location` i `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Przykład szablonu JavaScript

W tym przykładzie wysyła powiadomienie [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierający `location` i `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
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

## <a name="example---apns-native"></a>Przykład — APNS macierzystego

Ten przykładowy skrypt języka C# przedstawia sposób wysłania natywnego powiadomienia usługi APNS. 

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

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---gcm-native"></a>Przykład — GCM natywnego

Ten przykładowy skrypt języka C# przedstawia sposób wysłania natywnych powiadomień usługi GCM. 

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
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Przykład — WNS macierzystego

Ten przykładowy skrypt języka C# przedstawia sposób użycia typów zdefiniowanych w [Biblioteka programu Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) do wysyłania wyskakujących powiadomień WNS macierzystego. 

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

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) atrybutu.

Ten atrybut parametrami konstruktora a właściwości zostały opisane w [konfiguracji](#configuration) sekcji.

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `NotificationHub` atrybutu:

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** |Nie dotyczy| Musi być równa "notificationHub". |
|**direction** |Nie dotyczy| Należy wybrać opcję "out". | 
|**Nazwa** |Nie dotyczy| Nazwa zmiennej używane w kodzie funkcja komunikat Centrum powiadomień. |
|**tagExpression** |**TagExpression** | Wyrażeń tagów umożliwiają określenie, czy do zestawu urządzeń, które zostały zarejestrowane, aby otrzymywać powiadomienia, które odpowiada wyrażeniu tagu można dostarczyć powiadomień.  Aby uzyskać więcej informacji, zobacz [wyrażenia routingu i tagu](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Nazwa zasobu Centrum powiadomień, w portalu Azure. |
|**Połączenia** | **ConnectionStringSetting** | Nazwa ustawienia aplikacji, który zawiera ciąg połączenia usługi Notification Hubs.  Ciąg połączenia musi mieć ustawioną *DefaultFullSharedAccessSignature* wartość dla Centrum powiadomień. Zobacz [ustawienia połączenia](#connection-string-setup) dalszej części tego artykułu.|
|**platform** | **Platformy** | Właściwość platformy wskazuje platforma klienta celów powiadomień. Domyślnie w przypadku pominięcia właściwości platformy z wiązania danych wyjściowych szablonu powiadomienia można współpracować z dowolną platformą skonfigurowany w Centrum powiadomień Azure. Aby uzyskać więcej informacji na korzystanie z szablonów ogólnie do wysyłania wielu powiadomień platformy przy użyciu Centrum powiadomień Azure, zobacz [szablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Po ustawieniu **platformy** musi mieć jedną z następujących wartości: <ul><li><code>apns</code>&mdash;Usługa powiadomień wypychanych firmy Apple. Aby uzyskać więcej informacji na temat konfigurowania Centrum powiadomień dla APNS odbieranie powiadomień w aplikacji klienta, zobacz [wysyłanie powiadomień wypychanych do systemu iOS przy użyciu usługi Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Usługi Amazon Device Messaging](https://developer.amazon.com/device-messaging). Aby uzyskać więcej informacji na temat konfigurowania Centrum powiadomień dla ADM odbieranie powiadomień w aplikację dla urządzeń Kindle, zobacz [wprowadzenie do korzystania z usługi Notification Hubs dla aplikacji dla urządzeń Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Usługa Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Firebase Cloud Messaging, która jest nowa wersja usługi GCM, jest również obsługiwany. Aby uzyskać więcej informacji, zobacz [wysyłanie powiadomień wypychanych w systemie Android przy użyciu usługi Azure Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Usług powiadomień Wns](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) przeznaczonych dla platformy systemu Windows. Windows Phone 8.1 lub nowszy jest również obsługiwana przez usługi WNS. Aby uzyskać więcej informacji, zobacz [wprowadzenie Notification Hubs dla uniwersalnych aplikacji systemu Windows platformy](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Usługa powiadomień wypychanych firmy Microsoft](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx). Ta platforma obsługuje starszych platform Windows Phone i Windows Phone 8. Aby uzyskać więcej informacji, zobacz [wysyłanie powiadomień wypychanych przy użyciu usługi Azure Notification Hubs na Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>Przykładowy plik Function.JSON

Oto przykład powiązania centra powiadomień w *function.json* pliku.

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
      "platform": "gcm"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Ustawienia połączenia

Aby użyć powiązania danych wyjściowych Centrum powiadomień, należy skonfigurować parametry połączenia dla koncentratora. Możesz wybrać istniejącą Centrum powiadomień lub utworzyć nowe prawo jednego z *integracji* kartę w portalu Azure. Parametry połączenia można również skonfigurować ręcznie. 

Aby skonfigurować parametry połączenia do istniejącego Centrum powiadomień:

1. Przejdź do Centrum powiadomień w [portalu Azure](https://portal.azure.com), wybierz **zasady dostępu**i kliknij przycisk Kopiuj obok **DefaultFullSharedAccessSignature** zasad. Spowoduje to skopiowanie ciąg połączenia dla *DefaultFullSharedAccessSignature* zasad do Centrum powiadomień. Ten ciąg połączenia umożliwia funkcji wysyłania powiadomień do koncentratora.
    ![Skopiuj parametry połączenia Centrum powiadomień](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Przejdź do aplikacji funkcji w portalu Azure, wybierz **ustawienia aplikacji**, takie jak dodawanie klucza **MyHubConnectionString**, wklej skopiowane *DefaultFullSharedAccessSignature* dla Centrum powiadomień jako wartość, a następnie kliknij przycisk **zapisać**.

Nazwa tego ustawienia aplikacji jest co umieszczane w ustawieniu połączenia powiązania danych wyjściowych w *function.json* lub atrybut .NET. Zobacz [sekcji konfiguracji](#configuration) we wcześniejszej części tego artykułu.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Informacje ogólne |
|---|---|
| Centrum powiadomień | [Przewodnik obsługi](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)

