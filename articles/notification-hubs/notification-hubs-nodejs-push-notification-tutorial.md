---
title: Wysyłanie powiadomień wypychanych za pomocą centrów powiadomień platformy Azure i node.js
description: Dowiedz się, jak używać Centrów powiadomień do wysyłania powiadomień wypychanych z aplikacji Node.js.
keywords: powiadomienie wypychanie,powiadomienia wypychania,wypychanie node.js,wypychanie w systemu ios
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 6e109c5a7f4911893c81c88ae84322fb962fff6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71213194"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Wysyłanie powiadomień wypychanych za pomocą centrów powiadomień platformy Azure i node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Omówienie

> [!IMPORTANT]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli nie masz konta, utwórz bezpłatne konto próbne w ciągu zaledwie kilku minut za pośrednictwem [bezpłatnej wersji próbnej platformy Azure.](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)

W tym przewodniku pokazano, jak wysyłać powiadomienia wypychane za pomocą usługi Azure Notification Hubs bezpośrednio z aplikacji [Node.js.](https://nodejs.org)

Scenariusze obejmują wysyłanie powiadomień wypychanych do aplikacji na następujących platformach:

- Android
- iOS
- Platforma uniwersalna systemu Windows
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Usługi Azure Notification Hubs zapewniają łatwą w użyciu, wieloplatformową, skalowalną infrastrukturę do wysyłania powiadomień wypychanych do urządzeń przenośnych. Aby uzyskać szczegółowe informacje na temat infrastruktury usług, zobacz [stronę Usługi Azure Notification Hubs.](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx)

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji node.js

Pierwszym krokiem w tym samouczku jest utworzenie nowej pustej aplikacji Node.js. Aby uzyskać instrukcje dotyczące tworzenia aplikacji Node.js, zobacz [Tworzenie i wdrażanie aplikacji Node.js w witrynie azure w sieci Web,][nodejswebsite] [usługa w chmurze Node.js][Node.js Cloud Service] przy użyciu programu Windows PowerShell lub [witryny sieci Web z programem WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Konfigurowanie aplikacji do korzystania z centrów powiadomień

Aby korzystać z usługi Azure Notification Hubs, należy pobrać i używać [pakietu platformy Azure](https://www.npmjs.com/package/azure)Node.js , który zawiera wbudowany zestaw bibliotek pomocniczych, które komunikują się z usługami REST powiadomień wypychanych.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Użyj Menedżera pakietów węzłów (NPM), aby uzyskać pakiet

1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (Windows), **Terminal** (Mac) lub **Bash** (Linux) i przejdź do folderu, w którym utworzono pustą aplikację.
2. Wykonaj `npm install azure-sb` w oknie polecenia.
3. Można ręcznie uruchomić `ls` lub `dir` polecenie, aby `node_modules` sprawdzić, czy folder został utworzony.
4. Wewnątrz tego folderu znajdź pakiet **platformy Azure,** który zawiera biblioteki potrzebne do uzyskania dostępu do Centrum powiadomień.

> [!NOTE]
> Możesz dowiedzieć się więcej o instalacji NPM na oficjalnym [blogu NPM](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Importowanie modułu
Za pomocą edytora tekstu dodaj następującą `server.js` część pliku aplikacji:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Konfigurowanie połączenia usługi Azure Notification Hub

Obiekt `NotificationHubService` umożliwia pracę z centrami powiadomień. Poniższy kod `NotificationHubService` tworzy obiekt dla `hubname`centrum powiadomień o nazwie . Dodaj go w górnej `server.js` części pliku, po instrukcji, aby zaimportować moduł azure:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Uzyskaj wartość `connectionstring` połączenia z [witryny Azure portal,] wykonując następujące kroki:

1. W lewym okienku nawigacji kliknij pozycję **Przeglądaj**.
2. Wybierz **pozycję Centra powiadomień**, a następnie znajdź koncentrator, którego chcesz użyć dla przykładu. Jeśli potrzebujesz pomocy w tworzeniu nowego Centrum powiadomień, możesz zapoznać się z samouczkiem Wprowadzenie do [Sklepu Windows.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
3. Wybierz **pozycję Ustawienia**.
4. Kliknij przycisk **Zasady dostępu**. Widoczne są zarówno parametry połączenia udostępnionego, jak i pełnego dostępu.

![Portal Azure — Centra powiadomień](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Ciąg połączenia można również pobrać przy użyciu polecenia cmdlet **Get-AzureSbNamespace** dostarczonego przez [usługę Azure PowerShell](/powershell/azureps-cmdlets-docs) lub polecenia **azure sb namespace show** z [interfejsem wiersza polecenia platformy Azure (Azure CLI).](../cli-install-nodejs.md)

## <a name="general-architecture"></a>Architektura ogólna

Obiekt `NotificationHubService` udostępnia następujące wystąpienia obiektów do wysyłania powiadomień wypychanych do określonych urządzeń i aplikacji:

- **Android** - `GcmService` użyj obiektu, który jest dostępny pod adresem`notificationHubService.gcm`
- **iOS** - `ApnsService` użyj obiektu, który jest dostępny pod adresem`notificationHubService.apns`
- **Windows Phone** - `MpnsService` użyj obiektu, który jest dostępny pod adresem`notificationHubService.mpns`
- **Uniwersalna platforma windowsowa** - użyj `WnsService` obiektu, który jest dostępny pod adresem`notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Jak: Wysyłanie powiadomień push do aplikacji na Androida

Obiekt `GcmService` zawiera `send` metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji systemu Android. Metoda `send` akceptuje następujące parametry:

- **Tagi** - identyfikator znacznika. Jeśli nie zostanie podany żaden tag, powiadomienie jest wysyłane do wszystkich klientów.
- **Ładunek** — ładunek JSON lub nieprzetworzonego ciągu wiadomości.
- **Wywołanie zwrotne** - funkcja wywołania zwrotnego.

Aby uzyskać więcej informacji na temat formatu ładunku, zobacz [dokumentację ładunku](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload).

Poniższy kod używa `GcmService` wystąpienia udostępniane `NotificationHubService` przez do wysyłania powiadomień wypychanych do wszystkich zarejestrowanych klientów.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Jak: Wysyłanie powiadomień wypychanych do aplikacji na iOS

Podobnie jak w przypadku aplikacji `ApnsService` systemu Android `send` opisanych powyżej, obiekt zapewnia metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji systemu iOS. Metoda `send` akceptuje następujące parametry:

- **Tagi** - identyfikator znacznika. Jeśli nie zostanie podany żaden tag, powiadomienie jest wysyłane do wszystkich klientów.
- **Ładunek** — ładunek JSON wiadomości lub ładunku ciągu.
- **Wywołanie zwrotne** - funkcja wywołania zwrotnego.

Aby uzyskać więcej informacji na temat formatu ładunku, zobacz sekcję **Ładunek powiadomień** w dokumencie [Przewodnik po programowaniu powiadomień lokalnych i wypychanych.](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)

Poniższy kod używa `ApnsService` wystąpienia udostępniane `NotificationHubService` przez do wysyłania komunikatu alertu do wszystkich klientów:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Jak: Wysyłanie powiadomień wypychanych do aplikacji systemu Windows Phone

Obiekt `MpnsService` udostępnia `send` metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji systemu Windows Phone. Metoda `send` akceptuje następujące parametry:

- **Tagi** - identyfikator znacznika. Jeśli nie zostanie podany żaden tag, powiadomienie jest wysyłane do wszystkich klientów.
- **Ładunek** — ładunek XML wiadomości.
- **TargetName**  -  `toast` dla wyskakujących powiadomień. `token`dla powiadomień kafelkowych.
- **NotificationClass** — priorytet powiadomienia. Prawidłowe wartości można znaleźć w sekcji **Elementy nagłówka HTTP** [w powiadomieniach wypychanych z](https://msdn.microsoft.com/library/hh221551.aspx) dokumentu serwera.
- **Opcje** — opcjonalne nagłówki żądań.
- **Wywołanie zwrotne** - funkcja wywołania zwrotnego.

Aby uzyskać listę `TargetName` `NotificationClass` prawidłowych opcji i nagłówków, zapoznaj się [z powiadomieniami wypychanych ze](https://msdn.microsoft.com/library/hh221551.aspx) strony serwera.

Poniższy przykładowy kod `MpnsService` używa wystąpienia `NotificationHubService` udostępnianego przez do wysłania wyskakującego powiadomienia wypychanego:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Jak: Wysyłanie powiadomień wypychanych do aplikacji platformy uniwersalnej systemu Windows (UWP)

Obiekt `WnsService` udostępnia `send` metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji platformy uniwersalnej systemu Windows.  Metoda `send` akceptuje następujące parametry:

- **Tagi** - identyfikator znacznika. Jeśli nie zostanie podany żaden tag, powiadomienie jest wysyłane do wszystkich zarejestrowanych klientów.
- **Ładunek** - ładunek wiadomości XML.
- **Typ** - typ powiadomienia.
- **Opcje** — opcjonalne nagłówki żądań.
- **Wywołanie zwrotne** - funkcja wywołania zwrotnego.

Aby uzyskać listę prawidłowych typów i nagłówków żądań, zobacz [Żądania usługi powiadomień wypychanych i nagłówki odpowiedzi](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Poniższy kod używa `WnsService` wystąpienia udostępniane `NotificationHubService` przez do wysyłania wyskakujących powiadomień wypychanych do aplikacji platformy uniwersalnej systemu ip:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Następne kroki

Powyższe przykładowe fragmenty kodu umożliwiają łatwe tworzenie infrastruktury usług w celu dostarczania powiadomień wypychanych do wielu różnych urządzeń. Teraz, gdy poznasz podstawy korzystania z centrów powiadomień z node.js, skorzystaj z tych łączy, aby dowiedzieć się więcej o tym, jak można rozszerzyć te możliwości.

- Zobacz odwołanie MSDN dla [usługi Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Odwiedź [zestaw SDK platformy Azure dla] repozytorium węzłów w usłudze GitHub, aby uzyskać więcej przykładów i szczegółów implementacji.

[Zestaw Azure SDK dla platformy Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
