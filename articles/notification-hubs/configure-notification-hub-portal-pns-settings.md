---
title: Konfigurowanie centrum powiadomień platformy Azure przy użyciu ustawień systemu powiadomień platformy | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak skonfigurować centrum powiadomień w witrynie Azure Portal przy użyciu ustawień systemu powiadomień platformy.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 7f7e4a4d75a8e118da6f026817bc4ecfcc7a60db
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314026"
---
# <a name="configure-an-azure-notification-hub-with-platform-notification-system-settings-in-the-azure-portal"></a>Konfigurowanie centrum powiadomień platformy Azure przy użyciu ustawień systemu powiadomień platformy w witrynie Azure Portal 
Usługa Azure Notification Hubs oferuje łatwy w użyciu, skalowany w poziomie mechanizm, który umożliwia wysyłanie powiadomień do dowolnej platformy (iOS, Android, Windows, Kindle, Baidu, itp.) z poziomu dowolnego zaplecza (w chmurze lub w środowisku lokalnym). Aby uzyskać więcej informacji na temat usługi, zobacz [Co to jest usługa Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

[Utwórz centrum powiadomień platformy Azure przy użyciu witryny Azure Portal](create-notification-hub-portal.md), jeśli ta czynność nie została jeszcze wykonana. Z tego przewodnika Szybki start dowiesz się, jak skonfigurować centrum powiadomień w witrynie Azure Portal przy użyciu ustawień systemu powiadomień platformy.

## <a name="apple-push-notification-service-apns"></a>Apple Push Notification Service (APNS)
1. Na stronie **Centrum powiadomień** w witrynie Azure Portal wybierz pozycję **Apple (APNS)** w menu **Ustawienia** po lewej stronie.
2. Jeśli wybierzesz pozycję **Certyfikat**, wykonaj następujące czynności:
    1. Wybierz **ikonę pliku** i wybierz plik w formacie **p12** do przekazania. 
    2. Podaj **hasło**.
    3. Wybierz tryb **Piaskownica**. Tryb **Produkcja** należy wybrać wyłącznie wówczas, gdy chcesz wysyłać powiadomienia wypychane do użytkowników, którzy kupili Twoją aplikację w sklepie.

        ![Konfigurowanie certyfikacji APNs w witrynie Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)
3. Jeśli wybierzesz pozycję **Token**, wykonaj następujące czynności: 
    1. Wprowadź wartości w polach **Identyfikator klucza**, **Identyfikator pakietu**, **Identyfikator zespołu** i **Token**.
    2. Wybierz tryb **Piaskownica**. Tryb **Produkcja** należy wybrać wyłącznie wówczas, gdy chcesz wysyłać powiadomienia wypychane do użytkowników, którzy kupili Twoją aplikację w sklepie.

        ![Konfigurowanie tokenu usługi APNS w witrynie Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Aby uzyskać kompletny opis wysyłania powiadomień push do urządzeń z systemem iOS przy użyciu usług Azure Notification Hubs i Apple Push Notification Service (APNS), zapoznaj się z [tym samouczkiem](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)
1. Na stronie **Centrum powiadomień** w witrynie Azure Portal wybierz pozycję **Google (GCM/FCM)** w menu **Ustawienia** po lewej stronie. 
2. Wklej **klucz serwera** dla projektu usługi FCM, który został zapisany wcześniej. 
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Azure Notification Hubs — Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Wśród alertów pojawi się komunikat o pomyślnym zaktualizowaniu centrum powiadomień. Przycisk **Save** (Zapisz) będzie wyłączony. 

Aby uzyskać kompletny opis wysyłania powiadomień push do urządzeń z systemem Android przy użyciu usług Azure Notification Hubs and Google Firebase Cloud Messaging, zapoznaj się z [tym samouczkiem](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service-wns"></a>Usługa powiadomień WNS
1. Na stronie **Centrum powiadomień** w witrynie Azure Portal wybierz pozycję **Windows (WNS)** w menu **Ustawienia** po lewej stronie.
2. Wprowadź wartości w polach **Identyfikator SID pakietu** i **Klucz zabezpieczeń**.
3. Wybierz pozycję **Zapisz** na pasku narzędzi.

    ![Pola Identyfikator SID pakietu i Klucz zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


Aby uzyskać kompletny opis wysyłania powiadomień push do aplikacji platformy uniwersalnej systemu Windows (UWP) działającej na urządzeniu z systemem Windows, zapoznaj się z [tym samouczkiem](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="windows-phone---microsoft-push-notification-service"></a>Windows Phone — usługa powiadomień wypychanych firmy Microsoft
1. Na stronie **Centrum powiadomień** w witrynie Azure Portal wybierz pozycję **Windows Phone (MPNS)** w obszarze **Ustawienia**.
2. Aby włączyć nieuwierzytelnione wypychanie, wybierz pozycję **Włącz nieuwierzytelnione wypychanie**, a następnie wybierz pozycję **Zapisz** na pasku narzędzi.

    ![Witryna Azure Portal — włączanie nieuwierzytelnionych powiadomień push](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)
3. Jeśli chcesz używać **nieuwierzytelnionego** wypychania, wykonaj następujące kroki:
    1. Wybierz pozycję **Przekaż certyfikat** na pasku narzędzi.
    2. Wybierz **ikonę pliku** i wybierz plik certyfikatu.
    3. Wprowadź **hasło** certyfikatu. 
    4. Wybierz przycisk **OK**, aby zamknąć stronę **Przekazywanie certyfikatu**. 
    5. Na stronie **Windows Phone(MPNS)** wybierz pozycję **Zapisz** na pasku narzędzi.

Aby uzyskać kompletny opis wysyłania powiadomień push do aplikacji systemu Windows Phone 8 przy użyciu usługi wypychania powiadomień firmy Microsoft (MPNS), zapoznaj się z [tym samouczkiem](notification-hubs-windows-mobile-push-notifications-mpns.md).
      
## <a name="amazon-device-messaging-adm"></a>Amazon Device Messaging (ADM)
1. Na stronie **Centrum powiadomień** w witrynie Azure Portal wybierz pozycję **Amazon (ADM)** w menu **Ustawienia** po lewej stronie.
2. Wprowadź wartości w polach **Identyfikator klienta** i **Klucz tajny klienta**.
3. Wybierz pozycję **Zapisz** na pasku narzędzi.
    
    ![Azure Notification Hubs — ustawienia usługi ADM](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

Aby uzyskać kompletny opis sposobu używania powiadomień wypychanych usługi Azure Notification Hubs do aplikacji na urządzenia Kindle, zapoznaj się z [tym samouczkiem](notification-hubs-kindle-amazon-adm-push-notification.md).

## <a name="baidu-android-china"></a>Baidu (Android China)
1. Na stronie **Centrum powiadomień** w witrynie Azure Portal wybierz pozycję **Baidu (Android China)** w menu **Ustawienia** po lewej stronie. 
2. Wprowadź **klucz interfejsu API** uzyskany w konsoli Baidu dla projektu powiadomień push w chmurze Baidu. 
3. Wprowadź **klucz tajny** uzyskany w konsoli Baidu dla projektu powiadomień push w chmurze Baidu. 
4. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Azure Notification Hubs — Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)
4. Wśród alertów pojawi się komunikat o pomyślnym zaktualizowaniu centrum powiadomień. Przycisk **Save** (Zapisz) będzie wyłączony. 

Aby uzyskać kompletny opis wysyłania powiadomień push za pomocą powiadomień push w usłudze Azure Notification Hubs i chmurze Baidu, zapoznaj się z [tym samouczkiem](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start pokazano, jak konfigurować inne systemy powiadomień platformy dla centrum powiadomień w witrynie Azure Portal. 

Aby uzyskać pełne instrukcje krok po kroku dotyczące wysyłania powiadomień push do różnych platform, zapoznaj się z samouczkami w sesji **Samouczki**.

- [Wysyłanie powiadomień push do urządzeń z systemem iOS przy użyciu usług Azure Notification Hubs i Apple Push Notification Service (APNS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
- [Wysyłanie powiadomień push do urządzeń z systemem Android przy użyciu usług Azure Notification Hubs i Google Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md).
- [Wysyłanie powiadomień push do aplikacji platformy uniwersalnej systemu Windows działającej na urządzeniu z systemem Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
- [Wysyłanie powiadomień push do aplikacji systemu Windows Phone 8 przy użyciu usługi wypychania powiadomień firmy (MPNS)](notification-hubs-windows-mobile-push-notifications-mpns.md).
- [Wysyłanie powiadomień push do aplikacji dla urządzenia Kindle](notification-hubs-kindle-amazon-adm-push-notification.md).
- [Wysyłanie powiadomień push za pomocą usługi Azure Notification Hubs i funkcji wypychania do chmury usługi Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
