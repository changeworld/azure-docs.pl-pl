---
title: Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.Android
description: Dowiedz się, jak używać usługi Azure App Service i usługi Azure Notification Hubs do wysyłania powiadomień wypychanych do aplikacji Xamarin.Android.
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5657be0dbaeb46f8f899a9b4a2f8ba9b4fe9ebaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249310"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W tym samouczku dodasz powiadomienia wypychane do projektu szybki start platformy [Xamarin.Android,](app-service-mobile-windows-store-dotnet-get-started.md) aby powiadomienie wypychane było wysyłane do urządzenia za każdym razem, gdy zostanie wstawiony rekord.

Jeśli nie korzystasz z pobranego projektu serwera szybki start, potrzebny będzie pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji mobilnych platformy Azure.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga konfiguracji:

* Aktywne konto Google. Możesz założyć konto Google w [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Składnik klienta google cloud messaging](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurowanie Centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="enable-firebase-cloud-messaging"></a><a id="register"></a>Włączanie usługi Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Konfigurowanie platformy Azure do wysyłania żądań wypychania

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a><a id="update-server"></a>Aktualizowanie projektu serwera w celu wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-the-client-project-for-push-notifications"></a><a id="configure-app"></a>Konfigurowanie projektu klienta dla powiadomień wypychanych

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a name="add-push-notifications-code-to-your-app"></a><a id="add-push"></a>Dodawanie kodu powiadomień wypychanych do aplikacji

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test-push-notifications-in-your-app"></a><a name="test"></a>Testowanie powiadomień wypychanych w aplikacji

Aplikację można przetestować za pomocą urządzenia wirtualnego w emulatorze. Istnieją dodatkowe kroki konfiguracji wymagane podczas uruchamiania na emulatorze.

1. Urządzenie wirtualne musi mieć interfejsy API Google ustawione jako miejsce docelowe w menedżerze urządzenia wirtualnego androida (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Dodaj konto Google do urządzenia z Androidem, klikając**pozycję Ustawienia** >  **aplikacji** > **Dodaj konto,** a następnie postępuj zgodnie z instrukcjami.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Uruchom aplikację todolist jak poprzednio i wstaw nowy element do wykonania. Tym razem w obszarze powiadomień zostanie wyświetlona ikona powiadomienia. Możesz otworzyć szufladę powiadomień, aby wyświetlić pełną treść powiadomienia.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
