---
title: Wysyłanie powiadomień wypychanych przy użyciu usługi Azure Notification Hubs i Node.js
description: Dowiedz się, jak wysyłać powiadomienia wypychane z poziomu aplikacji Node.js przy użyciu usługi Notification Hubs.
keywords: powiadomienia wypychane, wypychanie notifications,node.js wypychania, wypychanych systemu ios
services: notification-hubs
documentationcenter: nodejs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 7463d41382c59e4f7f03b58dbcbc3f5c45e9d15c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Wysyłanie powiadomień wypychanych przy użyciu usługi Azure Notification Hubs i Node.js
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Przegląd
> [!IMPORTANT]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).
> 
> 

Ten przewodnik przedstawia, jak wysyłać powiadomienia wypychane za pomocą usługi Azure Notification Hubs bezpośrednio z poziomu aplikacji Node.js. 

Omówione scenariusze obejmują wysyłanie powiadomień wypychanych do aplikacji na następujących platformach:

* Android
* iOS
* Windows Phone
* Platforma uniwersalna systemu Windows 

Aby uzyskać więcej informacji dotyczących usługi notification hubs, zobacz [następne kroki](#next) sekcji.

## <a name="what-are-notification-hubs"></a>Co to jest Notification Hubs?
Centra powiadomień Azure zapewnia łatwy w użyciu, obejmującego wiele platform, skalowalna infrastrukturę do wysyłania powiadomień wypychanych do urządzeń przenośnych. Aby uzyskać szczegółowe informacje na infrastrukturę usług, zobacz [usługi Azure Notification Hubs](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) strony.

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji Node.js
Pierwszym krokiem w tym samouczku jest utworzenie nowej aplikacji Node.js puste. Aby uzyskać instrukcje dotyczące tworzenia aplikacji Node.js, zobacz [tworzenie i wdrażanie aplikacji Node.js do witryny sieci Web Azure][nodejswebsite], [Node.js usługi w chmurze] [ Node.js Cloud Service] przy użyciu programu Windows PowerShell lub [witryny sieci Web za pomocą programu WebMatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Skonfigurować aplikację tak, aby używać usługi Notification Hubs
Aby korzystać z usługi Azure Notification Hubs, konieczne pobranie i użycie Node.js [pakiet azure](https://www.npmjs.com/package/azure), która zawiera zestaw wbudowanych pomocnika bibliotek, które komunikują się z usługami REST powiadomień wypychanych.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Umożliwia uzyskanie pakietu węzeł Menedżera pakietów (NPM)
1. Użyj interfejsu wiersza polecenia, takich jak **PowerShell** (system Windows), **terminali** (Mac), lub **Bash** (Linux) i przejdź do folderu, w którym została utworzona pusta aplikacja.
2. Typ **azure sb instalacji narzędzia npm** w oknie wiersza polecenia.
3. Możesz ręcznie uruchomić **ls** lub **dir** polecenie, aby sprawdzić, czy **węzła\_modułów** folder został utworzony. W tym folderze, Znajdź **azure** pakiet, który zawiera biblioteki, musisz mieć dostęp do Centrum powiadomień.

> [!NOTE]
> Dowiedz się więcej na temat instalowania NPM w oficjalnym [NPM blog](http://blog.npmjs.org/post/85484771375/how-to-install-npm). 
> 
> 

### <a name="import-the-module"></a>Zaimportuj moduł
Za pomocą edytora tekstu, Dodaj następujący element do góry **server.js** pliku aplikacji:

    var azure = require('azure');

### <a name="set-up-an-azure-notification-hub-connection"></a>Skonfiguruj połączenie z Centrum powiadomień Azure
**NotificationHubService** obiektu umożliwia pracę z usługą notification hubs. Poniższy kod tworzy **NotificationHubService** obiektu dla Centrum powiadomień o nazwie **hubname**. Dodaj ją w górnej części **server.js** pliku po instrukcji, aby zaimportować moduł azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Połączenie **connectionstring** wartość można uzyskać z [portalu Azure] , wykonując następujące czynności:

1. W okienku nawigacji po lewej stronie kliknij **Przeglądaj**.
2. Wybierz **usługi Notification Hubs**, a następnie znajdź chcesz użyć przykładowej koncentratora. Można to sprawdzić [samouczka systemu Windows wprowadzenie magazynu](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) Jeśli potrzebujesz, aby uzyskać pomoc przy tworzeniu nowego centrum powiadomień.
3. Wybierz **ustawienia**.
4. Polecenie **zasady dostępu**. Możesz sprawdzić parametry połączenia udostępnionego i pełny dostęp.

![Portal Azure — centra powiadomień](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Można również pobrać parametry połączenia za pomocą **Get-AzureSbNamespace** udostępniane przez polecenia cmdlet [programu Azure PowerShell](/powershell/azureps-cmdlets-docs) lub **Pokaż przestrzeni nazw azure sb** z [(Azure CLI) interfejsu wiersza polecenia azure](../cli-install-nodejs.md).
> 
> 

## <a name="general-architecture"></a>Architektura ogólna
**NotificationHubService** obiekt udostępnia następujące wystąpienia obiektu do wysyłania powiadomień wypychanych do konkretnych urządzeń i aplikacji:

* **Android** -użyj **GcmService** obiektu, który jest dostępny na **notificationHubService.gcm**
* **iOS** -użyj **ApnsService** obiektu, który jest dostępny pod **notificationHubService.apns**
* **Windows Phone** -użyj **MpnsService** obiektu, który jest dostępny na **notificationHubService.mpns**
* **Platforma uniwersalna systemu Windows** -użyj **WnsService** obiektu, który jest dostępny na **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Porady: wysyłanie powiadomień wypychanych do aplikacji systemu Android
**GcmService** zawiera obiekt **wysyłania** metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji systemu Android. **Wysyłania** metoda przyjmuje następujące parametry:

* **Tagi** — identyfikator tagu. Jeśli znacznik nie jest podany, powiadomienie jest wysyłane do wszystkich klientów.
* **Ładunek** -JSON lub nieprzetworzony ciąg ładunek komunikatu.
* **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Aby uzyskać więcej informacji na format ładunku, zobacz **ładunku** sekcji [Implementowanie serwera GCM](http://developer.android.com/google/gcm/server.html#payload) dokumentu.

Poniższy kod używa **GcmService** wystąpienia udostępnianych przez **NotificationHubService** do wysyłania powiadomień wypychanych do wszystkich klientów w zarejestrowany.

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

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Porady: wysyłanie powiadomień wypychanych do aplikacji systemu iOS
Taki sam jak z aplikacji systemu Android opisano powyżej, **ApnsService** zawiera obiekt **wysyłania** metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji systemu iOS. **Wysyłania** metoda przyjmuje następujące parametry:

* **Tagi** — identyfikator tagu. Jeśli znacznik nie jest podany, powiadomienie jest wysyłane do wszystkich klientów.
* **Ładunek** -ładunek JSON lub ciąg komunikatu.
* **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Aby uzyskać więcej informacji na format ładunku, zobacz **ładunek powiadomienia** sekcji [lokalnych i wypychanych Podręcznik programowania powiadomień](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) dokumentu.

Poniższy kod używa **ApnsService** wystąpienia udostępnianych przez **NotificationHubService** do wysyłania alertów wiadomości do wszystkich klientów:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
         // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Porady: wysyłanie powiadomień wypychanych do aplikacji Windows Phone
**MpnsService** zawiera obiekt **wysyłania** metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji Windows Phone. **Wysyłania** metoda przyjmuje następujące parametry:

* **Tagi** — identyfikator tagu. Jeśli znacznik nie jest podany, powiadomienie jest wysyłane do wszystkich klientów.
* **Ładunek** -ładunek XML wiadomości.
* **TargetName**  -  `toast` dla wyskakujące powiadomienia. `token` Kafelek powiadomień.
* **NotificationClass** -priorytet powiadomienia. Zobacz **elementów nagłówka HTTP** sekcji [powiadomienia wypychane z serwera](http://msdn.microsoft.com/library/hh221551.aspx) dokumentu prawidłowe wartości.
* **Opcje** — opcjonalne nagłówki żądań.
* **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Lista prawidłową **TargetName**, **NotificationClass** i opcje nagłówka, zapoznaj się z [powiadomienia wypychane z serwera](http://msdn.microsoft.com/library/hh221551.aspx) strony.

Poniższy przykładowy kod używa **MpnsService** wystąpienia udostępnianych przez **NotificationHubService** do wysyłania wyskakujących powiadomień wypychanych:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Porady: wysyłanie powiadomień wypychanych do aplikacji uniwersalnych platformy systemu Windows (UWP)
**WnsService** zawiera obiekt **wysyłania** metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji platformy uniwersalnej systemu Windows.  **Wysyłania** metoda przyjmuje następujące parametry:

* **Tagi** — identyfikator tagu. Jeśli znacznik nie jest podany, powiadomienie jest wysyłane do wszystkich klientów w zarejestrowany.
* **Ładunek** -XML ładunek komunikatu.
* **Typ** — typ powiadomienia.
* **Opcje** — opcjonalne nagłówki żądań.
* **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Aby uzyskać listę prawidłowych typów i nagłówków żądań, zobacz [nagłówki żądań i odpowiedzi usługi powiadomień wypychanych](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Poniższy kod używa **WnsService** wystąpienia udostępnianych przez **NotificationHubService** do wysyłania wyskakujących powiadomień wypychanych do aplikacji platformy uniwersalnej systemu Windows:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
         // notification sent
      }
    });

## <a name="next-steps"></a>Następne kroki
Przykładowe fragmenty kodu powyżej umożliwiają łatwe tworzenie infrastruktury usługi dostarczać powiadomienia wypychane do szerokiej gamy urządzeń. Teraz, kiedy znasz już podstawy przy użyciu usługi Notification Hubs za pomocą języka node.js, skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat sposobu można rozszerzyć te dodatkowe możliwości.

* Zobacz odwołanie MSDN [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
* Odwiedź stronę [zestawu Azure SDK dla węzła] repozytorium w usłudze GitHub więcej przykładów i szczegóły implementacji.

[zestawu Azure SDK dla węzła]: https://github.com/WindowsAzure/azure-sdk-for-node
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
[SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[witryny sieci Web za pomocą programu WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
