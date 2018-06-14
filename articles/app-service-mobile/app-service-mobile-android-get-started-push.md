---
title: Dodawanie powiadomień wypychanych do aplikacji systemu Android z usługą Mobile Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z aplikacji mobilnej do wysyłania powiadomień wypychanych do aplikacji systemu Android.
services: app-service\mobile
documentationcenter: android
manager: crdun
editor: ''
author: conceptdev
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/17/2017
ms.author: crdun
ms.openlocfilehash: 9e9f7aba49c53a1a6fcc611ed771f266eb49c883
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593032"
---
# <a name="add-push-notifications-to-your-android-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Przegląd
W tym samouczku, dodawanie powiadomień wypychanych do [Android szybki start] projektu, aby powiadomienia wypychane są wysyłane do urządzenia, za każdym razem, gdy wstawieniu rekordu.

Szybki start pobrany Projekt serwera nie jest używany, należy najpierw pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
Potrzebne są następujące elementy:

* IDE, w zależności od projektu zaplecza:

  * [Android Studio](https://developer.android.com/sdk/index.html) Jeśli ta aplikacja ma zaplecza Node.js.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) lub nowszym, jeśli ta aplikacja ma zaplecze .NET firmy Microsoft.
* Android 2.3 i nowszy, poprawki repozytorium Google 27 lub nowszym i usług Google Play 9.0.2 lub nowszy Firebase Cloud Messaging.
* Zakończenie [Android szybki start].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Tworzenie projektu obsługującego usługę Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurowanie Centrum powiadomień
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurowanie usługi Azure do wysyłania powiadomień wypychanych
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Włącz powiadomienia wypychane dla server project
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Dodawanie powiadomień wypychanych do aplikacji
W tej sekcji należy zaktualizować klienta aplikacji systemu Android do obsługi powiadomień wypychanych.

### <a name="verify-android-sdk-version"></a>Sprawdź wersję zestawu SDK systemu Android
[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Następnym krokiem jest Zainstaluj usługi Google Play. Firebase Cloud Messaging ma niektóre minimalne interfejsu API poziomu wymagania dotyczące projektowania i testowania, który **minSdkVersion** musi odpowiadać właściwości w manifeście.

Jeśli testujesz starsze urządzenia, należy skontaktować się [Firebase Dodaj swój projekt Android] ustalenie, jak niska można ustawić tej wartości i odpowiednio ją ustawić.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Dodaj Firebase Cloud Messaging do projektu
[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Dodawanie kodu
[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testowanie aplikacji opublikowanych usługi mobilnej
Można przetestować aplikację, dołączając bezpośrednio telefonie z systemem Android za pomocą kabla USB lub za pomocą urządzenia wirtualnego w emulatorze.

## <a name="next-steps"></a>Kolejne kroki
Po ukończeniu tego samouczka, rozważ kontynuowanie na jedno z następujących samouczków:

* [Dodawanie uwierzytelniania do aplikacji systemu Android](app-service-mobile-android-get-started-users.md).
  Dowiedz się, jak dodawanie uwierzytelniania do listy zadań projektu szybkiego startu w programie Android przy użyciu dostawcy tożsamości obsługiwane.
* [Włączanie synchronizacji w trybie offline dla aplikacji systemu Android](app-service-mobile-android-get-started-offline-data.md).
  Dowiedz się, jak dodać obsługę w trybie offline do aplikacji przy użyciu zaplecza aplikacji mobilnej. Z synchronizacji w trybie offline, użytkownicy mogą korzystać z aplikacji mobilnej&mdash;przeglądanie, dodawanie lub modyfikowanie danych&mdash;nawet wtedy, gdy istnieje połączenie sieciowe.

<!-- URLs -->
[Android szybki start]: app-service-mobile-android-get-started.md
[Firebase Dodaj swój projekt Android]:https://firebase.google.com/docs/android/setup
