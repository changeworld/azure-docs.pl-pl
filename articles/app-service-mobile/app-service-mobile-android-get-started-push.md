---
title: Dodawanie powiadomień wypychanych do aplikacji systemu Android
description: Dowiedz się, jak wysyłać powiadomienia wypychane do aplikacji systemu Android za pomocą Mobile Apps.
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ce4ebe9e8874e779b8da16e9c30fcfc3ca46754e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668609"
---
# <a name="add-push-notifications-to-your-android-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd

W tym samouczku dowiesz się, jak dodać powiadomienia wypychane do projektu [Android — szybki start] , aby Powiadomienie wypychane było wysyłane do urządzenia za każdym razem, gdy rekord zostanie wstawiony.

Jeśli nie używasz pobranego projektu szybkiego startu serwera, potrzebujesz pakietu rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz temat [współpraca z zestawem SDK serwera zaplecza platformy .NET dla platformy Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące elementy:

* IDE, w zależności od zaplecza projektu:

  * [Android Studio](https://developer.android.com/sdk/index.html) , jeśli ta aplikacja ma zaplecze Node. js.
  * [Program Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) lub nowszy, jeśli ta aplikacja ma Microsoft .NET zapleczem.
* System Android 2,3 lub nowszy, poprawka do usługi Google Repository 27 lub nowsza, a Usługi Google Play 9.0.2 lub nowsza dla usługi Firebase Cloud Messaging.
* Zakończ pracę z [Android — szybki start].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Tworzenie projektu obsługującego usługę Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurowanie centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurowanie platformy Azure do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Włącz powiadomienia wypychane dla projektu serwera

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Dodawanie powiadomień wypychanych do aplikacji

Ta sekcja umożliwia zaktualizowanie aplikacji klienckiej systemu Android w celu obsługi powiadomień wypychanych.

### <a name="verify-android-sdk-version"></a>Sprawdź wersję Android SDK

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Następnym krokiem jest zainstalowanie Google Play usług. Obsługa komunikatów w chmurze Firebase ma pewne minimalne wymagania dotyczące poziomu interfejsu API na potrzeby tworzenia i testowania, które Właściwość **minSdkVersion** w manifeście musi być zgodna.

Jeśli testujesz się przy użyciu starszego urządzenia, zapoznaj się z tematem [Dodawanie Firebase do projektu systemu Android] , aby określić, jak niski można ustawić tę wartość i ustawić ją odpowiednio.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Dodawanie obsługi komunikatów w chmurze Firebase do projektu

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Dodaj kod

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testowanie aplikacji względem opublikowanej usługi mobilnej

Możesz przetestować aplikację przez bezpośrednie dołączenie telefonu z systemem Android przy użyciu kabla USB lub przy użyciu urządzenia wirtualnego w emulatorze.

## <a name="next-steps"></a>Następne kroki

Teraz po ukończeniu tego samouczka Rozważ przejście do jednego z następujących samouczków:

* [Dodawanie uwierzytelniania do aplikacji systemu Android](app-service-mobile-android-get-started-users.md).
  Dowiedz się, jak dodać uwierzytelnianie do projektu szybkiego startu todolist w systemie Android przy użyciu obsługiwanego dostawcy tożsamości.
* [Włącz synchronizację w trybie offline dla aplikacji systemu Android](app-service-mobile-android-get-started-offline-data.md).
  Dowiedz się, jak dodać obsługę offline do aplikacji przy użyciu zaplecza Mobile Apps. Dzięki synchronizacji w trybie offline użytkownicy mogą korzystać z aplikacji mobilnej&mdash;przeglądania, dodawania i modyfikowania danych&mdash;nawet w przypadku braku połączenia sieciowego.

<!-- URLs -->
[Android — szybki start]: app-service-mobile-android-get-started.md
[Dodawanie Firebase do projektu systemu Android]: https://firebase.google.com/docs/android/setup
