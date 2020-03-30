---
title: Dodawanie powiadomień wypychanych do systemu iOS
description: Dowiedz się, jak używać aplikacji Azure Mobile Apps do wysyłania powiadomień wypychanych do aplikacji na iOS.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ad58ee158a2e1842c1b41db281bdd9f04b9b2ca4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461508"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Dodawanie powiadomień wypychanych do aplikacji na iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W tym samouczku dodasz powiadomienia wypychane do projektu [szybkiego startu systemu iOS,] aby powiadomienie wypychane było wysyłane do urządzenia za każdym razem, gdy wstawiany jest rekord.

Jeśli nie korzystasz z pobranego projektu serwera szybki start, potrzebny będzie pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji mobilnych platformy Azure.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

[Symulator systemu iOS nie obsługuje powiadomień wypychanych](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Potrzebujesz fizycznego urządzenia z systemem iOS i członkostwa w [programie Dewelopera Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-notification-hub"></a><a name="configure-hub"></a>Konfigurowanie Centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-app-for-push-notifications"></a><a id="register"></a>Rejestrowanie aplikacji dla powiadomień wypychanych

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurowanie platformy Azure do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-backend-to-send-push-notifications"></a><a id="update-server"></a>Aktualizowanie zaplecza w celu wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="add-push-notifications-to-app"></a><a id="add-push"></a>Dodawanie powiadomień wypychanych do aplikacji

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a name="test-push-notifications"></a><a id="test"></a>Testowanie powiadomień wypychanych

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a name="more"></a><a id="more"></a>Więcej

* Szablony zapewniają elastyczność wysyłania wieloplatformowych wypycheń i zlokalizowanych wypycheń. [Jak korzystać z biblioteki klienta systemu iOS dla aplikacji mobilnych platformy Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) pokazuje, jak zarejestrować szablony.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Szybki start systemu iOS]: app-service-mobile-ios-get-started.md
