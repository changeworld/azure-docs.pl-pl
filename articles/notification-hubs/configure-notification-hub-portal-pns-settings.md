---
title: Konfigurowanie powiadomień wypychanych w usłudze Azure Notification Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi Azure Notification Hubs w witrynie Azure portal za pomocą ustawień systemu (powiadomień platformy PNS) powiadomień platformy.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: ee627a168e6ca9bb758d994a3f75cc6185976971
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203690"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Konfigurowanie powiadomień wypychanych w Centrum powiadomień, w witrynie Azure portal

Usługa Azure Notification Hubs zapewnia, jest łatwa w użyciu i skalowania, który aparat wypychania. Usługa Notification Hubs umożliwia wysyłanie powiadomień na dowolną platformę (iOS, Android, Windows, Kindle, Baidu) i z dowolnego zaplecza (w chmurze lub lokalnie). Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

W tym przewodniku Szybki Start użyjesz systemu (powiadomień platformy PNS) ustawienia powiadomień platformy w usłudze Notification Hubs do skonfigurowania powiadomień wypychanych na wielu platformach. Samouczek Szybki Start przedstawiono kroki do wykonania w witrynie Azure portal.

Jeśli nie zostało jeszcze utworzone Centrum powiadomień, utwórz ją teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie Centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md). 

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Aby ustawić się Apple Push Notification Service (APNS):

1. W witrynie Azure portal na **Centrum powiadomień** wybierz opcję **Apple (APNS)** menu po lewej stronie.

1. Aby uzyskać **tryb uwierzytelniania**, wybierz opcję **certyfikatu** lub **tokenu**.

   a. Jeśli wybierzesz **certyfikatu**:
   * Wybierz ikonę pliku, a następnie wybierz *.p12* pliku, który chcesz przekazać.
   * Wprowadź hasło.
   * Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili twoją aplikację ze sklepu, zaznacz **produkcji** trybu.

     ![Zrzut ekranu przedstawiający APNS certyfikatu konfiguracji w witrynie Azure portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Jeśli wybierzesz **tokenu**:

   * Wprowadź wartości w polach **identyfikator klucza**, **identyfikator pakietu**, **identyfikator zespołu**, i **tokenu**.
   * Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili twoją aplikację ze sklepu, zaznacz **produkcji** trybu.

     ![Zrzut ekranu przedstawiający APNS token konfiguracji w witrynie Azure portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Aby uzyskać więcej informacji, zobacz [powiadomienia wypychane do systemu iOS przy użyciu usługi Azure Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Usługi Google Firebase Cloud Messaging

Aby skonfigurować powiadomienia wypychane dla Google Firebase Cloud Messaging (FCM):

1. W witrynie Azure portal na **Centrum powiadomień** wybierz opcję **Google (GCM/FCM)** menu po lewej stronie. 
2. Wklej **klucz interfejsu API** w projekcie usługi FCM, który został wcześniej zapisany. 
3. Wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu pokazujący sposób konfigurowania usługi Notification Hubs dla usługi FCM Google](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Po wykonaniu tych kroków, alert oznacza pomyślnie zaktualizowano Centrum powiadomień. Przycisk **Save** (Zapisz) będzie wyłączony. 

Aby uzyskać więcej informacji, zobacz [wysyłać powiadomienia wypychane do urządzeń z systemem Android przy użyciu usługi Notification Hubs i Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Windows Push Notification Service

Aby ustawić się Windows wypychanie powiadomień WNS (Service):

1. W witrynie Azure portal na **Centrum powiadomień** wybierz opcję **Windows (WNS)** menu po lewej stronie.
2. Wprowadź wartości w polach **Identyfikator SID pakietu** i **Klucz zabezpieczeń**.
3. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu pokazujący pola Identyfikator SID pakietu i klucz zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Aby uzyskać informacje, zobacz [wysyłać powiadomienia do aplikacji platformy uniwersalnej systemu Windows za pomocą usługi Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Usługi powiadomień wypychanych firmy Microsoft dla Windows Phone

Aby ustawić się Microsoft Push Notification usługi (MPNS) dla Windows Phone: 

1. W witrynie Azure portal na **Centrum powiadomień** wybierz opcję **Windows Phone (MPNS)** menu po lewej stronie.
1. Włącz nieuwierzytelnione lub uwierzytelniony powiadomień wypychanych:

   a. Aby włączyć nieuwierzytelnione wypychanie powiadomień, wybierz **Włącz nieuwierzytelnione wypychanie** > **Zapisz**.

      ![Zrzut ekranu pokazujący sposób Włącz nieuwierzytelnione wypychanie powiadomień](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Aby włączyć powiadomienia wypychane uwierzytelnionego:
      * Na pasku narzędzi wybierz **Przekaż certyfikat**.
      * Wybierz ikonę pliku, a następnie wybierz plik certyfikatu.
      * Wprowadź hasło certyfikatu.
      * Kliknij przycisk **OK**.
      * Na **Windows Phone (MPNS)** wybierz opcję **Zapisz**.

Aby uzyskać więcej informacji, zobacz [powiadomienia wypychane do aplikacji Windows Phone przy użyciu usługi Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).
      
## <a name="amazon-device-messaging"></a>Amazon Device Messaging

Aby skonfigurować powiadomienia wypychane dla Amazon Device Messaging (ADM):

1. W witrynie Azure portal na **Centrum powiadomień** wybierz opcję **Amazon (ADM)** menu po lewej stronie.
2. Wprowadź wartości w polach **identyfikator klienta** i **klucz tajny klienta**.
3. Wybierz pozycję **Zapisz**.
    
   ![Zrzut ekranu usługi ADM ustawienia w witrynie Azure portal](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Notification Hubs dla aplikacji na urządzenie Kindle](notification-hubs-kindle-amazon-adm-push-notification.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Aby skonfigurować powiadomienia wypychane dla usługi Baidu:

1. W witrynie Azure portal na **Centrum powiadomień** wybierz opcję **Baidu (Android China)** menu po lewej stronie. 
2. Wprowadź **klucz interfejsu Api** uzyskany w konsoli Baidu w projektu powiadomień wypychanych w chmurze Baidu. 
3. Wprowadź **klucz tajny** uzyskany w konsoli Baidu w projektu powiadomień wypychanych w chmurze Baidu. 
4. Wybierz pozycję **Zapisz**. 

    ![Zrzut ekranu z usługi Notification Hubs przedstawiający konfigurację powiadomień wypychanych Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Po wykonaniu tych kroków, alert oznacza pomyślnie zaktualizowano Centrum powiadomień. Przycisk **Save** (Zapisz) będzie wyłączony. 

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Notification Hubs przy użyciu usługi Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki Start przedstawiono sposób konfigurowania ustawień systemu powiadomień platformy Centrum powiadomień w witrynie Azure portal. 

Aby dowiedzieć się więcej o tym, jak wypychać powiadomienia do różnych platform, zobacz następujące samouczki:

- [Wypychanie powiadomień do urządzeń z systemem iOS przy użyciu usługi Notification Hubs i usługi APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Wysyłanie powiadomień wypychanych do urządzeń z systemem Android przy użyciu usługi Notification Hubs i Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Powiadomienia wypychane do aplikacji platformy uniwersalnej systemu Windows uruchomione na urządzeniu z systemem Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Powiadomienia wypychane do aplikacji Windows Phone 8 przy użyciu usługi MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Powiadomienia wypychane do aplikacji dla urządzeń Kindle](notification-hubs-kindle-amazon-adm-push-notification.md)
- [Powiadomienia wypychane przy użyciu usługi Notification Hubs i Baidu wypychane w chmurze](notification-hubs-baidu-china-android-notifications-get-started.md)
