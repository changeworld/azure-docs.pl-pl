---
title: Notification Hubs powiązania dla usługi Azure Functions
description: Dowiedz się, jak używać usługi Azure Notification Hub powiązania w usłudze Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 79ea9455fec7d31f800b2b5d36df6a2a53f502c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61438202"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Usługa Notification Hubs danych wyjściowych powiązania dla usługi Azure Functions

W tym artykule wyjaśniono, jak wysyłać powiadomienia wypychane przy użyciu [usługi Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) powiązań w usłudze Azure Functions. Usługa Azure Functions obsługuje powiązania danych wyjściowych do usługi Notification Hubs.

Usługa Azure Notification Hubs, muszą być skonfigurowane dla platformy powiadomienia usługi (system powiadomień platformy) chcesz użyć. Aby dowiedzieć się, jak otrzymywać powiadomienia wypychane w aplikacji klienckiej z usługi Notification Hubs, zobacz [wprowadzenie do usługi Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) i wybierz platformę klienta docelowego z listy rozwijanej w górnej części strony.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Google ma [przestarzałe Google Cloud Messaging (GCM) na rzecz usługi Firebase Cloud Messaging (FCM)](https://developers.google.com/cloud-messaging/faq). To powiązanie danych wyjściowych nie obsługuje usługi FCM. Aby wysłać powiadomienia za pomocą usługi FCM, należy użyć [interfejsu API usługi Firebase](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) bezpośrednio w funkcji lub użyj [powiadomienia o szablonach](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania usługi Notification Hubs znajdują się w [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) pakietu NuGet w wersji 1.x. Kod źródłowy dla pakietu znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

To powiązanie nie jest dostępna w funkcji 2.x.

## <a name="example---template"></a>Przykład — szablon

Powiadomienia wysyłane mogą być natywne powiadomienia lub [powiadomienia o szablonach](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Natywne powiadomienia Docelowa platforma określonego klienta, zgodnie z konfiguracją `platform` właściwości powiązania danych wyjściowych. Powiadomienia szablonu może służyć do wielu platform docelowych.   

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# — parametr wyjściowy](#c-script-template-example---out-parameter)
* [Skrypt języka C# — asynchroniczne](#c-script-template-example---asynchronous)
* [Skrypt języka C# — JSON](#c-script-template-example---json)
* [Skrypt języka C# — Biblioteka typów](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# przykład szablonu skryptu — parametr wyjściowy

W tym przykładzie wysyła powiadomienie [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierający `message` symbol zastępczy w szablonie.

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

### <a name="c-script-template-example---asynchronous"></a>C# szablon przykładowy skrypt - asynchroniczne

Jeśli używasz kodu asynchronicznego parametry wyjściowe nie są dozwolone. W tym przypadku użyj `IAsyncCollector` do zwrócenia szablonu powiadomienia. Poniższy kod jest przykładem asynchronicznego kodu powyżej. 

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

### <a name="c-script-template-example---json"></a>C# szablon przykładowy skrypt - JSON

W tym przykładzie wysyła powiadomienie [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierający `message` symbol zastępczy w szablonie, za pomocą prawidłowy ciąg JSON.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# szablon przykładowy skrypt - biblioteki typów

W tym przykładzie pokazano, jak używać typów zdefiniowanych w [Biblioteka usługi Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

### <a name="f-template-example"></a>F#Przykładowy szablon

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

## <a name="example---apns-native"></a>Przykład — natywnej usługi APNS

Ten przykładowy skrypt języka C# pokazano, jak wysyłać natywne powiadomienia usługi APNS. 

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

## <a name="example---wns-native"></a>Przykład — natywnej usługi WNS

Ten przykładowy skrypt języka C# pokazano, jak używać typów zdefiniowanych w [Biblioteka usługi Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) do wysyłania natywnych powiadomień wyskakującego powiadomienia usługi WNS. 

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

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [Centrum NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) atrybutu.

Ten atrybut konstruktora parametry i właściwości, które są opisane w [konfiguracji](#configuration) sekcji.

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `NotificationHub` atrybutu:

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** |Nie dotyczy| Musi być równa "Centrum notificationHub". |
|**direction** |Nie dotyczy| Musi być równa "out". | 
|**name** |Nie dotyczy| Nazwa zmiennej, używane w kodzie funkcji komunikatów Centrum powiadomień. |
|**tagExpression** |**TagExpression** | Wyrażenia tagu umożliwiają określenie, że powiadomienia dostarczone do zestawu urządzeń, które zostały zarejestrowane do otrzymywania powiadomień, które pasują do wyrażenia tagu.  Aby uzyskać więcej informacji, zobacz [routingu i znaczników wyrażeń](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Nazwa zasobu Centrum powiadomień w witrynie Azure portal. |
|**połączenia** | **ConnectionStringSetting** | Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Notification Hubs.  Parametry połączenia musi być równa *DefaultFullSharedAccessSignature* wartość dla Centrum powiadomień. Zobacz [ustawienia parametrów połączenia](#connection-string-setup) w dalszej części tego artykułu.|
|**platform** | **Platforma** | Właściwość platformy wskazuje platforma klienta elementy docelowe powiadomień. Domyślnie w przypadku pominięcia właściwości platformy z powiązania danych wyjściowych szablonu powiadomienia mogą służyć do przeznaczone na dowolną platformę skonfigurowany w Centrum powiadomień platformy Azure. Aby uzyskać więcej informacji na temat korzystania z szablonów ogólnie rzecz biorąc do wysyłania wielu powiadomień platformy przy użyciu usługi Azure Notification Hub, zobacz [szablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Po ustawieniu **platformy** musi mieć jedną z następujących wartości: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Aby uzyskać więcej informacji na temat konfigurowania Centrum powiadomień dla usługi APNS i otrzymywanie powiadomienia w aplikacji klienta, zobacz [wysyłanie powiadomień wypychanych do systemu iOS z usługą Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Aby uzyskać więcej informacji na temat konfigurowania Centrum powiadomień dla usługi ADM i odbieranie powiadomień w aplikację dla urządzeń Kindle, zobacz [wprowadzenie do usługi Notification Hubs dla aplikacji na urządzenie Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>wns</code>&mdash;[Usługi powiadomień wypychanych Windows](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) przeznaczonych dla platform Windows. Windows Phone 8.1 lub nowszy, jest również obsługiwana przez usługi WNS. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Notification Hubs dla Windows aplikacji platformy uniwersalnej](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Usługi powiadomień wypychanych firmy Microsoft](/previous-versions/windows/apps/ff402558(v=vs.105)). Ta platforma obsługuje wcześniejszych platform Windows Phone i Windows Phone 8. Aby uzyskać więcej informacji, zobacz [wysyłanie powiadomień wypychanych przy użyciu usługi Azure Notification Hubs na Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>przykład pliku Function.JSON

Oto przykład powiązania usługi Notification Hubs w *function.json* pliku.

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

### <a name="connection-string-setup"></a>Ustawienia parametrów połączenia

Aby użyć powiązania danych wyjściowych Centrum powiadomień, należy skonfigurować parametry połączenia dla koncentratora. Można wybrać istniejące Centrum powiadomień, lub utworzyć nowe uprawnienie jednego z *integracja* kartę w witrynie Azure portal. Parametry połączenia można również skonfigurować ręcznie. 

Aby skonfigurować parametry połączenia do istniejącego Centrum powiadomień:

1. Przejdź do Centrum powiadomień w [witryny Azure portal](https://portal.azure.com), wybierz **zasady dostępu**i kliknij przycisk kopiowania obok **DefaultFullSharedAccessSignature** zasad. Spowoduje to skopiowanie parametrów połączenia dla *DefaultFullSharedAccessSignature* zasady Centrum powiadomień. Te parametry połączenia informuje funkcję wysyłania komunikatów powiadomień do Centrum.
    ![Skopiuj parametry połączenia Centrum powiadomień](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Przejdź do aplikacji funkcji w witrynie Azure portal, wybierz **ustawienia aplikacji**, takie jak dodawanie klucza **MyHubConnectionString**, Wklej skopiowany *DefaultFullSharedAccessSignature* Centrum powiadomień wartość, a następnie kliknij przycisk **Zapisz**.

Nazwa tego ustawienia aplikacji to, co jest umieszczane w ustawieniach połączenia powiązania danych wyjściowych w *function.json* lub atrybut .NET. Zobacz [sekcji konfiguracji](#configuration) we wcześniejszej części tego artykułu.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Tematy pomocy |
|---|---|
| Centrum powiadomień | [Przewodnik obsługi](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

