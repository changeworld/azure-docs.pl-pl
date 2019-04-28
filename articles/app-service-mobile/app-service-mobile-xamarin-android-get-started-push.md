---
title: Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.Android | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure App Service i usługi Azure Notification Hubs wysyłać powiadomienia wypychane do aplikacji platformy Xamarin.Android
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: f2c58d64a662a6e2be355c1f672c01e5a679b3f8
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127948"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W ramach tego samouczka, możesz dodać powiadomienia wypychane do [Szybki Start platformy Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md) projektu, dzięki czemu jest wysyłane powiadomienie push do urządzenia, za każdym razem, gdy rekord zostanie wstawiona.

Jeśli nie używasz pobrany projekt szybkiego startu server, konieczne będzie pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) przewodnik.

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga instalacji:

* Aktywne konto Google. Możesz zasubskrybować konto Google na [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Usługa Google Cloud Messaging składnik klienta](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Konfigurowanie Centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Włączanie usługi Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Konfigurowanie platformy Azure na potrzeby wysyłania żądań wypychania

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Aktualizuj projekt serwera do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Konfigurowanie projektu klienta dla powiadomień wypychanych

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Dodaj kod powiadomienia wypychane do aplikacji

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Testowych powiadomień push w aplikacji

Aby przetestować aplikację, należy za pomocą urządzenia wirtualnego w emulatorze. Istnieją dodatkowe czynności konfiguracyjne wymagane podczas uruchamiania w emulatorze.

1. Wirtualne urządzenie musi mieć ustawioną jako docelową w programie Android Virtual Device (AVD) manager interfejsy API Google.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Dodaj konto Google na urządzeniu z systemem Android, klikając **aplikacje** > **ustawienia** > **Dodaj konto**, następnie postępuj zgodnie z monitami.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Uruchom aplikację listy zadań jako przed i Wstaw nowy element todo. Tym razem ikonę powiadomień jest wyświetlany w obszarze powiadomień. Możesz otworzyć menu powiadomień, aby wyświetlić pełny tekst powiadomienia.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
