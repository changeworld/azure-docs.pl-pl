---
title: Routing i wyrażenia tagów
description: W tym temacie objaśniono wyrażenia routingu i znaczników dla centrów powiadomień platformy Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: 66388f139b63c63e1f0f8ee8ee063e0ddd0f9da5
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213037"
---
# <a name="routing-and-tag-expressions"></a>Routing i wyrażenia tagów

## <a name="overview"></a>Przegląd

Wyrażenia tagów umożliwiają kierowanie określonych zestawów urządzeń lub bardziej szczegółowych rejestracji podczas wysyłania powiadomień wypychanych za pomocą Notification Hubs.

## <a name="targeting-specific-registrations"></a>Kierowanie określonych rejestracji

Jedynym sposobem na kierowanie określonych rejestracji powiadomień jest skojarzenie z nimi tagów, a następnie określenie ich jako docelowej. Zgodnie z opisem w temacie [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)w celu otrzymywania powiadomień wypychanych aplikacja musi zarejestrować dojście urządzenia w centrum powiadomień. Po utworzeniu rejestracji w centrum powiadomień zaplecza aplikacji może wysyłać do niej powiadomienia wypychane. Zaplecze aplikacji może wybrać rejestracje docelowe dla określonego powiadomienia w następujący sposób:

1. **Broadcast**: Wszystkie rejestracje w centrum powiadomień otrzymują powiadomienie.
2. **Tag**: Wszystkie rejestracje zawierające określony tag otrzymują powiadomienie.
3. **Wyrażenie tagu**: Wszystkie rejestracje, których zestaw tagów odpowiada określonemu wyrażeniu, otrzymuje powiadomienie.

## <a name="tags"></a>`Tags`

Tag może być dowolnym ciągiem, do 120 znaków, zawierający alfanumeryczne i następujące znaki inne niż alfanumeryczne: "_", "@", "#", ".", ":", "-". W poniższym przykładzie przedstawiono aplikację, z której można otrzymywać wyskakujące powiadomienia dotyczące określonych grup utworów muzycznych. W tym scenariuszu prostym sposobem na kierowanie powiadomień jest etykietowanie rejestracji ze znacznikami reprezentującymi różne grupy, jak na poniższej ilustracji:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Na tym rysunku komunikat oznaczony **Beatles** osiągnie tylko tablet zarejestrowany z tagiem **Beatles**.

Aby uzyskać więcej informacji o tworzeniu rejestracji dla tagów, zobacz [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md).

Powiadomienia można wysyłać do tagów przy użyciu metod `Microsoft.Azure.NotificationHubs.NotificationHubClient` wysyłania powiadomień klasy w zestawie SDK [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) . Można również używać interfejsów API REST powiadomień wypychanych.  Oto przykład użycia zestawu SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

Tagi nie muszą być wstępnie udostępniane i mogą odwoływać się do wielu koncepcji specyficznych dla aplikacji. Na przykład użytkownicy tej przykładowej aplikacji mogą komentować paski i chcą otrzymywać wyskakujące okienka, nie tylko w przypadku komentarzy do ich ulubionych grup, ale także dla wszystkich komentarzy od ich znajomych, niezależnie od pasma, do którego są komentowane. Na poniższej ilustracji przedstawiono przykład tego scenariusza:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Na tym obrazie Alicja chce mieć aktualizacje dla Beatles, a Robert interesuje aktualizacje dla Wailers. Robert jest również zainteresowany komentarzami Charlie, a Charlie jest zainteresowane Wailers. Po wysłaniu powiadomienia dla komentarza Charlie na Beatles, zarówno Alicja, jak i Robert otrzymują tę wiadomość.

Chociaż można kodować wiele obaw w tagach (na przykład "band_Beatles" lub "follows_Charlie"), Tagi są prostymi ciągami i nie są właściwościami z wartościami. Rejestracja jest zgodna tylko z obecnością lub brakiem określonego tagu.

Aby uzyskać pełny samouczek krok po kroku dotyczący sposobu używania tagów do wysyłania do grup zainteresowań, zobacz artykuł dotyczący [najświeższych wiadomości](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Usługa Azure Notification Hubs obsługuje maksymalnie 60 tagów na rejestrację.

## <a name="using-tags-to-target-users"></a>Używanie tagów do użytkowników docelowych

Innym sposobem używania tagów jest zidentyfikowanie wszystkich urządzeń danego użytkownika. Rejestracje mogą być otagowane za pomocą tagu, który zawiera identyfikator użytkownika, jak na poniższej ilustracji:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Na tym obrazie oznaczony identyfikator UID wiadomości: Alicja osiągnie wszystkie rejestracje oznaczone jako "UID: Alicja"; w związku z tym wszystkie urządzenia programu Alicja.

## <a name="tag-expressions"></a>Wyrażenia tagów

Istnieją przypadki, w których powiadomienie musi kierować zestawem rejestracji, które są identyfikowane nie przez pojedynczy tag, ale przez wyrażenie logiczne dla tagów.

Rozważmy aplikację sportową, która wysyła do wszystkich użytkowników w Boston informacje o grze między czerwoną SOX a Kardynalnami. Jeśli aplikacja kliencka rejestruje znaczniki dotyczące zainteresowania w zespołach i w lokalizacji, to powiadomienie powinno być skierowane do wszystkich osób w Boston, które interesują się czerwoną SOX lub Kardynalnami. Ten stan można wyrazić za pomocą następującego wyrażenia logicznego:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Wyrażenia tagów mogą zawierać wszystkie operatory logiczne, takie jak i (& &), lub (| |), a nie (!). Mogą również zawierać nawiasy. Wyrażenia tagów są ograniczone do 20 tagów, jeśli zawierają tylko ORs; w przeciwnym razie są ograniczone do 6 tagów.

Oto przykład wysyłania powiadomień z wyrażeniami tagów przy użyciu zestawu SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
