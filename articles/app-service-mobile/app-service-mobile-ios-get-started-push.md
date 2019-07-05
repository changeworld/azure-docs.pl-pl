---
title: Dodawanie powiadomień wypychanych do aplikacji za pomocą usługi Azure Mobile Apps dla systemu iOS
description: Dowiedz się, jak wysyłać powiadomienia wypychane do aplikacji systemu iOS za pomocą usługi Azure Mobile Apps.
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
ms.openlocfilehash: 5ab968e88331f888dfcecd2cc30a658b0b0f53ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445355"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Dodawanie powiadomień wypychanych do aplikacji dla systemu iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center jest inwestujemy w nowe i zintegrowane usługi decydujące znaczenie dla aplikacji mobilnych. Deweloperzy mogą używać **kompilacji**, **testu** i **dystrybucji** usług do konfigurowania potoku ciągłej integracji i ciągłego dostarczania. Gdy aplikacja jest wdrażana, deweloperzy mogą monitorować stan i użycie ich przy użyciu aplikacji **Analytics** i **diagnostyki** usług i angażuj użytkowników za pomocą **wypychania** Usługa. Deweloperzy mogą również wykorzystać **uwierzytelniania** do uwierzytelniania użytkowników i **danych** usługę, aby utrwalić i synchronizowanie danych aplikacji w chmurze. Zapoznaj się z [platformy App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started-push) już dziś.
>

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
