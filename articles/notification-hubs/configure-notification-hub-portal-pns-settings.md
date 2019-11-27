---
title: Konfigurowanie powiadomień wypychanych na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować usługę Azure Notification Hubs w Azure Portal za pomocą ustawień systemu powiadomień platformy (PNS).
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 951f03f581906e45946ef75742421ba27d405267
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406974"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Konfigurowanie powiadomień wypychanych w centrum powiadomień w Azure Portal

Usługa Azure Notification Hubs udostępnia aparat wypychania, który jest łatwy w użyciu i skaluje się. Użyj Notification Hubs, aby wysyłać powiadomienia do dowolnej platformy (iOS, Android, Windows, Baidu) i z dowolnego zaplecza (w chmurze lub lokalnie). Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

W tym przewodniku szybki start użyjesz ustawień systemu powiadomień platformy (PNS) w Notification Hubs, aby skonfigurować powiadomienia wypychane na wielu platformach. W tym przewodniku szybki start przedstawiono kroki, które należy wykonać w Azure Portal.

Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md). 

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Aby skonfigurować Apple Push Notification Service (APNS):

1. W Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Apple (APNs)** z menu po lewej stronie.

1. W obszarze **tryb uwierzytelniania**wybierz pozycję **certyfikat** lub **token**.

   a. W przypadku wybrania opcji **certyfikat**:
   * Wybierz ikonę pliku, a następnie wybierz plik *. p12* , który chcesz przekazać.
   * Wprowadź hasło.
   * Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili aplikację ze sklepu, wybierz pozycję Tryb **produkcyjny** .

     ![Zrzut ekranu przedstawiający konfigurację certyfikatu APNS w Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. W przypadku wybrania opcji **token**:

   * Wprowadź wartości dla **identyfikatora klucza**, **identyfikatora pakietu**, **identyfikatora zespołu**i **tokenu**.
   * Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili aplikację ze sklepu, wybierz pozycję Tryb **produkcyjny** .

     ![Zrzut ekranu przedstawiający konfigurację tokenu usługi APNS w Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Aby uzyskać więcej informacji, zobacz [wypychanie powiadomień do systemu iOS przy użyciu usługi Azure Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Usługa Google Firebase Cloud Messaging

Aby skonfigurować powiadomienia wypychane dla usługi Google Firebase Cloud Messaging (FCM):

1. W Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Google (GCM/FCM)** z menu po lewej stronie. 
2. Wklej **klucz interfejsu API** dla projektu FCM, który został wcześniej zapisany. 
3. Wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu pokazujący sposób konfigurowania Notification Hubs dla usługi Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Po wykonaniu tych kroków alert wskazuje, że centrum powiadomień zostało pomyślnie zaktualizowane. Przycisk **Save** (Zapisz) będzie wyłączony. 

Aby uzyskać więcej informacji, zobacz [wysyłanie powiadomień wypychanych do urządzeń z systemem Android przy użyciu usług Notification Hubs i Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Usługa powiadomień wypychanych systemu Windows

Aby skonfigurować usługę powiadomień WNS (WNS):

1. W Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Windows (WNS)** z menu po lewej stronie.
2. Wprowadź wartości w polach **Identyfikator SID pakietu** i **Klucz zabezpieczeń**.
3. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający pola identyfikatora SID pakietu i klucza zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Aby uzyskać więcej informacji, zobacz [wysyłanie powiadomień do aplikacji platformy UWP przy użyciu usługi Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Usługa powiadomień wypychanych firmy Microsoft dla Windows Phone

Aby skonfigurować usługę powiadomień wypychanych firmy Microsoft (usługi MPNS) dla Windows Phone: 

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Windows Phone (usługi MPNS)** z menu po lewej stronie.
1. Włącz nieuwierzytelnione lub uwierzytelnione powiadomienia wypychane:

   a. Aby włączyć nieuwierzytelnione powiadomienia wypychane, zaznacz opcję **Włącz nieuwierzytelnione wypychanie** > **Zapisz**.

      ![Zrzut ekranu pokazujący sposób włączania nieuwierzytelnionych powiadomień wypychanych](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Aby włączyć uwierzytelnione powiadomienia wypychane:
      * Na pasku narzędzi wybierz pozycję **Przekaż certyfikat**.
      * Wybierz ikonę pliku, a następnie wybierz plik certyfikatu.
      * Wprowadź hasło certyfikatu.
      * Kliknij przycisk **OK**.
      * Na stronie **Windows Phone (usługi MPNS)** wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji, zobacz [wysyłanie powiadomień wypychanych do Windows Phone aplikacji przy użyciu Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).
      

## <a name="baidu-android-china"></a>Baidu (Android China)

Aby skonfigurować powiadomienia wypychane dla Baidu:

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Baidu (Android China)** z menu po lewej stronie. 
2. Wprowadź **klucz interfejsu API** uzyskany z konsoli Baidu w projekcie powiadomienia wypychane w chmurze Baidu. 
3. Wprowadź **klucz tajny** uzyskany z konsoli Baidu w projekcie powiadomienia wypychane w chmurze Baidu. 
4. Wybierz pozycję **Zapisz**. 

    ![Zrzut ekranu przedstawiający Notification Hubs, w którym jest wyświetlana konfiguracja Baidu (Android China) dla powiadomień wypychanych](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Po wykonaniu tych kroków alert wskazuje, że centrum powiadomień zostało pomyślnie zaktualizowane. Przycisk **Save** (Zapisz) będzie wyłączony. 

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z Notification Hubs przy użyciu Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start przedstawiono sposób konfigurowania ustawień systemowych powiadomień platformy dla centrum powiadomień w Azure Portal. 

Aby dowiedzieć się więcej o sposobie wypychania powiadomień do różnych platform, zobacz następujące samouczki:

- [Powiadomienia wypychane do urządzeń z systemem iOS przy użyciu Notification Hubs i usługi APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Powiadomienia wypychane do urządzeń z systemem Android przy użyciu usług Notification Hubs i Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Powiadomienia wypychane do aplikacji platformy UWP działającej na urządzeniu z systemem Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Powiadomienia wypychane do aplikacji Windows Phone 8 przy użyciu usługi MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Powiadomienia wypychane przy użyciu Notification Hubs i powiadomienia wypychane w chmurze Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)
