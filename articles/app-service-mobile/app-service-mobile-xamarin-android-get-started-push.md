---
title: Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin. Android | Microsoft Docs
description: Dowiedz się, jak używać Azure App Service i usługi Azure Notification Hubs do wysyłania powiadomień wypychanych do aplikacji platformy Xamarin. Android
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 25e0196fec94acb363757e74fa71f666a4c9d11c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388487"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd

W tym samouczku dowiesz się, jak dodać powiadomienia wypychane do projektu [szybkiego startu platformy Xamarin. Android](app-service-mobile-windows-store-dotnet-get-started.md) , aby Powiadomienie wypychane było wysyłane do urządzenia za każdym razem, gdy rekord zostanie wstawiony.

Jeśli nie korzystasz z pobranego projektu szybkiego startu serwera, będzie potrzebny pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący [pracy z zestawem SDK serwera zaplecza platformy .NET dla platformy Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga instalacji:

* Aktywne konto Google. Konto Google można zarejestrować pod adresem [accounts.Google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Google Cloud Messaging składnik klienta](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Konfigurowanie centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Włącz obsługę komunikatów w chmurze Firebase

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Skonfiguruj platformę Azure do wysyłania żądań push

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Aktualizowanie projektu serwera w celu wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Konfigurowanie projektu klienta na potrzeby powiadomień wypychanych

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Dodawanie kodu powiadomień wypychanych do aplikacji

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Testowanie powiadomień wypychanych w aplikacji

Aplikację można przetestować przy użyciu urządzenia wirtualnego w emulatorze. W przypadku korzystania z emulatora wymagane są dodatkowe czynności konfiguracyjne.

1. Urządzenie wirtualne musi mieć interfejs API usługi Google ustawiony jako element docelowy w Menedżerze urządzeń wirtualnych z systemem Android (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Dodaj konto Google do urządzenia z systemem Android, klikając pozycję **aplikacje** > **Ustawienia** > **Dodaj konto**, a następnie postępuj zgodnie z monitami.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Uruchom aplikację todolist jako wcześniejszą i Wstaw nowy element do wykonania. Tym razem w obszarze powiadomień zostanie wyświetlona ikona powiadomienia. Możesz otworzyć szufladę powiadomień, aby wyświetlić pełny tekst powiadomienia.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
