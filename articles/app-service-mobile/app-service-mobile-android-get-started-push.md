---
title: Dodawanie powiadomień wypychanych do aplikacji na Androida
description: Dowiedz się, jak używać aplikacji mobilnych do wysyłania powiadomień wypychanych do aplikacji na Androida.
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6fec85c028e992c15fb9503ffb599023e668c58f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459943"
---
# <a name="add-push-notifications-to-your-android-app"></a>Dodawanie powiadomień wypychanych do aplikacji na Androida

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W tym samouczku dodasz powiadomienia wypychane do projektu [szybkiego startu systemu Android,] aby powiadomienie wypychane było wysyłane do urządzenia za każdym razem, gdy wstawiany jest rekord.

Jeśli nie korzystasz z pobranego projektu serwera szybkiego startu, potrzebujesz pakietu rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji mobilnych platformy Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące elementy:

* IDE, w zależności od zaplecza projektu:

  * [Android Studio,](https://developer.android.com/sdk/index.html) jeśli ta aplikacja ma Node.js back end.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) lub nowszej, jeśli ta aplikacja ma microsoft .NET zaplecza.
* Android 2.3 lub nowszy, Wersja repozytorium Google 27 lub nowsza oraz Usługi Google Play 9.0.2 lub nowsze dla Firebase Cloud Messaging.
* Ukończ [szybki start systemu Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Tworzenie projektu obsługującego usługę Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurowanie centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurowanie platformy Azure do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Włączanie powiadomień wypychanych dla projektu serwera

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Dodawanie powiadomień wypychanych do aplikacji

W tej sekcji zaktualizuj aplikację klienta systemu Android do obsługi powiadomień wypychanych.

### <a name="verify-android-sdk-version"></a>Weryfikowanie wersji SDK systemu Android

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Następnym krokiem jest zainstalowanie usług Google Play. Firebase Cloud Messaging ma pewne minimalne wymagania poziomu interfejsu API dla rozwoju i testowania, które **minSdkVersion** właściwość w manifeście musi być zgodna.

Jeśli testujesz ze starszym urządzeniem, skonsultuj się z [dodaj Firebase do projektu systemu Android,] aby ustalić, jak niski można ustawić tę wartość i ustawić ją odpowiednio.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Dodawanie firebase cloud messaging do projektu

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Dodaj kod

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testowanie aplikacji pod względem opublikowanej usługi mobilnej

Aplikację można przetestować, podłączając bezpośrednio telefon z systemem Android za pomocą kabla USB lub używając wirtualnego urządzenia w emulatorze.

## <a name="next-steps"></a>Następne kroki

Teraz, po wykonaniu tego samouczka, należy rozważyć kontynuowanie jednego z następujących samouczków:

* [Dodaj uwierzytelnianie do aplikacji na Androida](app-service-mobile-android-get-started-users.md).
  Dowiedz się, jak dodać uwierzytelnianie do projektu szybkiego startu listy todolist w systemie Android przy użyciu obsługiwanego dostawcy tożsamości.
* [Włącz synchronizację offline dla aplikacji na Androida](app-service-mobile-android-get-started-offline-data.md).
  Dowiedz się, jak dodać obsługę offline do aplikacji za pomocą zaplecza aplikacji mobilnych. Dzięki synchronizacji w trybie offline&mdash;użytkownicy mogą wchodzić&mdash;w interakcje z przeglądaniem, dodawaniem lub modyfikowaniem danych aplikacji mobilnej, nawet jeśli nie ma połączenia sieciowego.

<!-- URLs -->
[Szybki start na Androida]: app-service-mobile-android-get-started.md
[Dodaj Firebase do swojego projektu Android]:https://firebase.google.com/docs/android/setup
