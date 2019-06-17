---
title: Dodawanie powiadomień wypychanych do aplikacji systemu Android dzięki funkcji Mobile Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Mobile Apps do wysyłania powiadomień wypychanych do aplikacji systemu Android.
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
ms.openlocfilehash: 352e64664e6796fb4e0a7941de91ef4045076aed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104613"
---
# <a name="add-push-notifications-to-your-android-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W ramach tego samouczka, możesz dodać powiadomienia wypychane do [Android — szybki start] projektu, dzięki czemu jest wysyłane powiadomienie push do urządzenia, za każdym razem, gdy rekord zostanie wstawiona.

Jeśli nie używasz projektu serwera pobranego — szybki start, należy pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące elementy:

* Środowisko IDE, w zależności od projektu zaplecza:

  * [Program android Studio](https://developer.android.com/sdk/index.html) Jeśli ta aplikacja ma zaplecza Node.js.
  * [Program Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) lub nowszy, jeśli ta aplikacja ma zaplecza programu Microsoft .NET.
* System android 2.3 lub nowszy, Google Repository w wersji 27 lub nowszej i sklepu Google Play Services 9.0.2 lub nowsze dla usługi Firebase Cloud Messaging.
* Wykonaj [Android — szybki start].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Tworzenie projektu obsługującego usługę Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurowanie centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurowanie platformy Azure, jak wysyłać powiadomienia wypychane

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Włącz powiadomienia wypychane dla server project

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Dodawanie powiadomień wypychanych do aplikacji

W tej sekcji należy zaktualizować klienta aplikacji systemu Android do obsługi powiadomień wypychanych.

### <a name="verify-android-sdk-version"></a>Sprawdź wersję zestawu SDK systemu Android

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Następnym krokiem jest do zainstalowania usług Google Play. Usługi firebase Cloud Messaging ma niektóre minimalne interfejsu API poziomu wymagań dotyczących tworzenia i testowania, która **minSdkVersion** musi odpowiadać właściwości w manifeście.

Jeśli testujesz starsze urządzenia, zapoznaj się z [Dodawanie usługi Firebase do projektu systemu Android] ustalenie, jak mała można ustawić tę wartość i odpowiednio ją ustawić.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Dodawanie usługi Firebase Cloud Messaging dla projektu

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Dodaj kod

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testowanie aplikacji opublikowanej usługi mobilnej

Można przetestować aplikację, dołączając bezpośrednio telefonie z systemem Android za pomocą kabla USB lub za pomocą urządzenia wirtualnego w emulatorze.

## <a name="next-steps"></a>Kolejne kroki

Teraz, w tym samouczku została zakończona, należy wziąć pod uwagę przejściem do jednej z następujących samouczków:

* [Dodawanie uwierzytelniania do aplikacji systemu Android](app-service-mobile-android-get-started-users.md).
  Dowiedz się, jak dodać uwierzytelnianie do projektu quickstart listy zadań na systemu Android przy użyciu dostawcy tożsamości obsługiwanych.
* [Włączanie synchronizacji offline dla aplikacji systemu Android](app-service-mobile-android-get-started-offline-data.md).
  Dowiedz się, jak dodać obsługę trybu offline do aplikacji przy użyciu zaplecze funkcji Mobile Apps. Synchronizacja w trybie offline, użytkownicy mogą wchodzić w interakcje z aplikacją mobilną&mdash;wyświetlanie, dodawanie lub modyfikowanie danych&mdash;nawet w przypadku braku połączenia sieciowego.

<!-- URLs -->
[Android — szybki start]: app-service-mobile-android-get-started.md
[Dodawanie usługi Firebase do projektu systemu Android]: https://firebase.google.com/docs/android/setup
