---
title: Dodawanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Mobile Apps
description: Dowiedz się, jak za pomocą usługi Azure Mobile Apps wysyłać powiadomienia wypychane do aplikacji dla systemu iOS.
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: ''
author: elamalani
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 3a0b177e1e0fc9575a48c3126d5707bfc02c7d95
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388742"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd

W tym samouczku dowiesz się, jak dodać powiadomienia wypychane do projektu [System iOS — Szybki Start] , aby Powiadomienie wypychane było wysyłane do urządzenia za każdym razem, gdy rekord zostanie wstawiony.

Jeśli nie korzystasz z pobranego projektu szybkiego startu serwera, będzie potrzebny pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [pracy z zestawem SDK serwera zaplecza platformy .NET dla platformy Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

[Symulator systemu iOS nie obsługuje powiadomień wypychanych](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Wymagane jest fizyczne urządzenie z systemem iOS i [członkostwo w programie dla deweloperów firmy Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Konfigurowanie centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Rejestrowanie aplikacji do powiadomień wypychanych

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurowanie platformy Azure do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Aktualizowanie zaplecza w celu wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Dodawanie powiadomień wypychanych do aplikacji

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Testowanie powiadomień wypychanych

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Szczegółowe

* Szablony zapewniają elastyczność wysyłania wypchnięciów międzyplatformowych i zlokalizowanych powiadomień wypychanych. [Jak używać biblioteki klienckiej systemu iOS dla platformy Azure Mobile Apps](app-service-mobile-ios-how-to-use-client-library.md#templates) pokazuje, jak zarejestrować szablony.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[System iOS — Szybki Start]: app-service-mobile-ios-get-started.md
