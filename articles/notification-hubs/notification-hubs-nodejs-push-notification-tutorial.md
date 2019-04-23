---
title: Wysyłanie powiadomień wypychanych przy użyciu usługi Azure Notification Hubs i środowiska Node.js
description: Dowiedz się, jak używać usługi Notification Hubs wysyłać powiadomienia wypychane z poziomu aplikacji Node.js.
keywords: powiadomienie wypychane, wypychanie notifications,node.js wypychania, wypychania dla systemu ios
services: notification-hubs
documentationcenter: nodejs
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bdeba401e99ad16555b9f6ea00017fc525302983
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995008"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Wysyłanie powiadomień wypychanych przy użyciu usługi Azure Notification Hubs i środowiska Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Omówienie

> [!IMPORTANT]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli nie masz konta, Utwórz bezpłatne konto próbne w zaledwie kilka minut za pośrednictwem [bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Ten przewodnik pokazuje, jak wysyłać powiadomienia wypychane za pomocą usługi Azure Notification hubs bezpośrednio z [Node.js](https://nodejs.org) aplikacji.

Omówione scenariusze obejmują wysyłanie powiadomień wypychanych do aplikacji na następujących platformach:

- Android
- iOS
- Platforma uniwersalna systemu Windows
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Usługa Azure Notification Hubs zapewnia łatwe w użyciu, wieloplatformową skalowalne infrastrukturę umożliwiającą wysyłanie powiadomień wypychanych do urządzeń przenośnych. Szczegółowe informacje na temat infrastruktury usługi, [usługi Azure Notification Hubs](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) strony.

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji Node.js

Pierwszym krokiem w ramach tego samouczka jest utworzenie nowej pustej aplikacji Node.js. Instrukcje dotyczące tworzenia aplikacji w technologii Node.js można znaleźć [tworzenie i wdrażanie aplikacji Node.js do witryny sieci Web Azure][nodejswebsite], [Node.js usługi w chmurze] [ Node.js Cloud Service] przy użyciu programu Windows PowerShell, lub [witryny sieci Web za pomocą programu WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Skonfiguruj aplikację, aby używać usługi Notification Hubs

Za pomocą usługi Azure Notification Hubs, musisz pobrać i używać narzędzia Node.js [pakiet azure](https://www.npmjs.com/package/azure), który zawiera zestaw wbudowanych bibliotek pomocnika, które komunikują się z usług REST powiadomień wypychanych.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Uzyskiwanie pakietu przy użyciu Node Package Manager (NPM)

1. Korzystanie z interfejsu wiersza polecenia takich jak **PowerShell** (Windows), **terminalu** (Mac) lub **Bash** (Linux) i przejdź do folderu, w której została utworzona pusta aplikacja.
2. Wykonaj `npm install azure-sb` w oknie wiersza polecenia.
3. Można ręcznie uruchomić `ls` lub `dir` polecenie, aby sprawdzić, czy `node_modules` folder został utworzony.
4. Wewnątrz tego folderu Znajdź **azure** pakiet, który zawiera biblioteki, musisz mieć dostęp do Centrum powiadomień.

> [!NOTE]
> Dowiedz się więcej na temat instalowania NPM na official będzie przydatna dla [NPM blog](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Zaimportuj moduł
Za pomocą edytora tekstów, Dodaj następujący kod do górnej części `server.js` pliku aplikacji:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Konfigurowanie połączenia usługi Azure Notification Hubs

`NotificationHubService` Obiekt umożliwia pracę z usługą notification hubs. Poniższy kod tworzy `NotificationHubService` obiektu dla Centrum powiadomień o nazwie `hubname`. Dodaj ją w górnej części `server.js` pliku po instrukcji, aby zaimportować moduł platformy azure:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Uzyskaj połączenie `connectionstring` wartość z [Azure Portal] , wykonując następujące czynności:

1. W okienku nawigacji po lewej stronie kliknij **Przeglądaj**.
2. Wybierz **usługi Notification Hubs**, a następnie znajdź koncentratora, o których chcesz użyć na potrzeby przykładu. Możesz zapoznać się z [Windows Store wprowadzenie do samouczka](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) Jeśli potrzebujesz pomocy przy tworzeniu nowego centrum powiadomień.
3. Wybierz **ustawienia**.
4. Kliknij pozycję **zasady dostępu**. Zostanie wyświetlony oba parametry połączenia udostępnionego i pełny dostęp.

![Witryna Azure portal — usługa Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Możesz również pobrać parametry połączenia za pomocą **Get AzureSbNamespace** udostępniane przez polecenia cmdlet [programu Azure PowerShell](/powershell/azureps-cmdlets-docs) lub **Pokaż przestrzeni nazw azure sb** polecenia [Interfejs wiersza polecenia platformy (Azure CLI)](../cli-install-nodejs.md).

## <a name="general-architecture"></a>Architektura ogólna

`NotificationHubService` Obiekt udostępnia następujące wystąpienia obiektu umożliwiającą wysyłanie powiadomień wypychanych do konkretnych urządzeń i aplikacji:

- **Android** — użyj `GcmService` obiektu, który znajduje się w temacie `notificationHubService.gcm`
- **iOS** — użyj `ApnsService` obiektu, który jest dostępny pod adresem `notificationHubService.apns`
- **Windows Phone** — użyj `MpnsService` obiektu, który znajduje się w temacie `notificationHubService.mpns`
- **Universal Windows Platform** — użyj `WnsService` obiektu, który znajduje się w temacie `notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Instrukcje: Wysyłanie powiadomień wypychanych do aplikacji dla systemu Android

`GcmService` Obiektu `send` metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji systemu Android. `send` Metoda przyjmuje następujące parametry:

- **Tagi** — identyfikator tagu. Jeśli zostanie podany żaden tag, powiadomienie jest wysyłane do wszystkich klientów.
- **Ładunek** -JSON lub nieprzetworzonego ciągu ładunek komunikatu.
- **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Aby uzyskać więcej informacji o formacie ładunek, zobacz **ładunku** części [Implementowanie serwera GCM](https://developer.android.com/google/gcm/server.html#payload) dokumentu.

Poniższy kod używa `GcmService` wystąpienia udostępnianych przez `NotificationHubService` do wysyłania powiadomień wypychanych do wszystkich zarejestrowanych klientów.

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

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Instrukcje: Wysyłanie powiadomień wypychanych do aplikacji systemu iOS

Taki sam, jak za pomocą aplikacji dla systemu Android opisanych powyżej, `ApnsService` obiektu `send` metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji systemu iOS. `send` Metoda przyjmuje następujące parametry:

- **Tagi** — identyfikator tagu. Jeśli zostanie podany żaden tag, powiadomienie jest wysyłane do wszystkich klientów.
- **Ładunek** -ładunku JSON lub ciąg komunikatu.
- **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Aby uzyskać więcej informacji na format ładunku zobacz **ładunek powiadomienia** części [lokalnych i wypychanych Podręcznik programowania powiadomień](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) dokumentu.

Poniższy kod używa `ApnsService` wystąpienia udostępnianych przez `NotificationHubService` Aby wysłać komunikat ostrzegawczy do wszystkich klientów:

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

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Instrukcje: Wysyłanie powiadomień wypychanych do aplikacji Windows Phone

`MpnsService` Obiektu `send` metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji Windows Phone. `send` Metoda przyjmuje następujące parametry:

- **Tagi** — identyfikator tagu. Jeśli zostanie podany żaden tag, powiadomienie jest wysyłane do wszystkich klientów.
- **Ładunek** -ładunek XML komunikatu.
- **TargetName**  -  `toast` powiadomienia wyskakującego powiadomienia. `token` Kafelek powiadomienia.
- **NotificationClass** — priorytet zawiadomienia. Zobacz **elementów nagłówka HTTP** części [powiadomienia wypychane z serwera](https://msdn.microsoft.com/library/hh221551.aspx) dokumentu prawidłowych wartości.
- **Opcje** — jest to opcjonalne nagłówki żądania.
- **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Aby uzyskać listę prawidłowe `TargetName`, `NotificationClass` i opcje nagłówka, zapoznaj się z [powiadomienia wypychane z serwera](https://msdn.microsoft.com/library/hh221551.aspx) strony.

Poniższy przykładowy kod używa `MpnsService` wystąpienia udostępnianych przez `NotificationHubService` wysyłać powiadomienia wypychane wyskakujące:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Instrukcje: Wysyłanie powiadomień wypychanych do aplikacji platformy uniwersalnej Windows (UWP)

`WnsService` Obiektu `send` metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji uniwersalnych platformy Windows.  `send` Metoda przyjmuje następujące parametry:

- **Tagi** — identyfikator tagu. Jeśli zostanie podany żaden tag, powiadomienie jest wysyłane do wszystkich zarejestrowanych klientów.
- **Ładunek** -XML ładunek komunikatu.
- **Typ** — typ powiadomienia.
- **Opcje** — jest to opcjonalne nagłówki żądania.
- **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Aby uzyskać listę prawidłowych typów i nagłówków żądań, zobacz [nagłówki żądania i odpowiedzi usługi powiadomień wypychanych](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Poniższy kod używa `WnsService` wystąpienia udostępnianych przez `NotificationHubService` wysyłać wyskakujące powiadomienia wypychane do aplikacji platformy uniwersalnej systemu Windows:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Następne kroki

Przykładowe fragmenty kodu powyżej umożliwiają łatwe tworzenie infrastruktury usługi do dostarczania powiadomień wypychanych do szerokiej gamy urządzeń. Teraz, kiedy znasz już podstawy używania usługi Notification Hubs przy użyciu środowiska node.js, skorzystaj z poniższych linków, aby dowiedzieć się więcej o jak rozszerzyć możliwości dalszego.

- Zobacz dokumentacja MSDN [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Odwiedź stronę [Zestaw Azure SDK dla węzła] repozytorium w serwisie GitHub, aby uzyskać więcej przykładów i szczegółów implementacji.

[Zestaw Azure SDK dla węzła]: https://github.com/WindowsAzure/azure-sdk-for-node
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
