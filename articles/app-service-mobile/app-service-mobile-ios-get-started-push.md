---
title: Dodawanie powiadomień wypychanych do aplikacji za pomocą usługi Azure Mobile Apps dla systemu iOS
description: Dowiedz się, jak wysyłać powiadomienia wypychane do aplikacji systemu iOS za pomocą usługi Azure Mobile Apps.
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: ''
author: conceptdev
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/10/2016
ms.author: crdun
ms.openlocfilehash: 4d56453eb05e88c3558b66250439d6211e8069fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122578"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Dodawanie powiadomień wypychanych do aplikacji dla systemu iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W ramach tego samouczka, możesz dodać powiadomienia wypychane do [Przewodnik Szybki Start dla systemu iOS] projektu, dzięki czemu jest wysyłane powiadomienie push do urządzenia, za każdym razem, gdy rekord zostanie wstawiona.

Jeśli nie używasz pobrany projekt szybkiego startu server, konieczne będzie pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) przewodnik.

[Symulatora systemu iOS nie obsługuje powiadomienia wypychane](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Potrzebujesz urządzenia fizyczne z systemem iOS i [członkostwa w programie dla deweloperów firmy Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Konfigurowanie Centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Rejestrowanie aplikacji na potrzeby obsługi powiadomień wypychanych

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurowanie platformy Azure, jak wysyłać powiadomienia wypychane

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Aktualizuj zaplecza służącego do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Dodawanie powiadomień wypychanych do aplikacji

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Powiadomienia wypychane testu

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Więcej

* Szablony zapewniają elastyczność i możliwość wysyłania wypchnięć dla wielu platform i zlokalizowane wypchnięć. [Sposób użycia biblioteki klienta usługi Azure Mobile Apps-iOS](app-service-mobile-ios-how-to-use-client-library.md#templates) dowiesz się, jak zarejestrować szablony.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Przewodnik Szybki Start dla systemu iOS]: app-service-mobile-ios-get-started.md
