---
title: Routing i wyrażenia tagów w centrach powiadomień platformy Azure
description: Dowiedz się, jak rozsyłać i oznaczać wyrażenia dla centrów powiadomień platformy Azure.
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
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 2432ac41645e373ea3a87ff7e69ef02a4e30c81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062310"
---
# <a name="routing-and-tag-expressions"></a>Routing i wyrażenia znaczników

## <a name="overview"></a>Omówienie

Wyrażenia znaczników umożliwiają kierowanie określonych zestawów urządzeń, a dokładniej rejestracji, podczas wysyłania powiadomień wypychanych za pośrednictwem Centrum powiadomień.

## <a name="targeting-specific-registrations"></a>Kierowanie na określone rejestracje

Jedynym sposobem kierowania określonych rejestracji powiadomień jest skojarzenie z nimi tagów, a następnie kierowanie na te tagi. Jak wspomniano w [zarządzania rejestracją](notification-hubs-push-notification-registration-management.md), aby otrzymywać powiadomienia wypychane, aplikacja musi zarejestrować dojście urządzenia w centrum powiadomień. Gdy aplikacja tworzy rejestrację w centrum powiadomień, wewnętrznej bazy danych aplikacji można wysyłać powiadomienia wypychanych do niego. Wewnętrznej bazy danych aplikacji można wybrać rejestracje do kierowania z określonego powiadomienia w następujący sposób:

1. **Emisja:** wszystkie rejestracje w centrum powiadomień otrzymują powiadomienie.
2. **Tag:** wszystkie rejestracje, które zawierają określony tag, otrzymują powiadomienie.
3. **Wyrażenie znacznika:** wszystkie rejestracje, których zestaw znaczników jest zgodny z określonym wyrażeniem, otrzymują powiadomienie.

## <a name="tags"></a>Tagi

Znacznik może być dowolny ciąg, do 120 znaków, zawierający alfanumeryczne i następujące znaki`_`niealummeryczne: ' ', '`@`' ' ' ' '`#`' , ' ,`.`' ' , ', ',`:`', ', '.`-` W poniższym przykładzie przedstawiono aplikację, z której można otrzymywać wyskakujące powiadomienia o określonych grupach muzycznych. W tym scenariuszu prosty sposób na kierowanie powiadomień jest oznaczenie rejestracji tagami reprezentującymi różne pasma, jak na poniższym rysunku:

![Omówienie tagów](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Na rysunku wiadomość z tagiem **Beatles** dociera tylko do tabletu, który zarejestrował się pod tagiem **Beatles.**

Aby uzyskać więcej informacji na temat tworzenia rejestracji tagów, zobacz [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md).

Powiadomienia można wysyłać do tagów przy użyciu `Microsoft.Azure.NotificationHubs.NotificationHubClient` metod wysyłania powiadomień klasy w zestawie SDK [centrów powiadomień platformy Microsoft Azure.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) Można również użyć node.js lub wypychanych powiadomień REST API.  Oto przykład przy użyciu SDK.

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

Tagi nie mogą być wstępnie aprowizacji i może odnosić się do wielu pojęć specyficznych dla aplikacji. Na przykład użytkownicy tej przykładowej aplikacji mogą komentować zespoły i chcą otrzymywać toasty, nie tylko za komentarze na temat swoich ulubionych zespołów, ale także za wszystkie komentarze od swoich znajomych, niezależnie od zespołu, na którym komentują. Na poniższym rysunku przedstawiono przykład tego scenariusza:

![Tagi znajomych](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

W tym przykładzie Alicja jest zainteresowana aktualizacjami dla Beatlesów, a Robert jest zainteresowany aktualizacjami dla Wailers. Bob jest również zainteresowany komentarzami Charliego, a Charlie jest zainteresowany Wailers. Gdy zostanie wysłane powiadomienie o komentarzu Charliego na temat Beatlesów, Centra powiadomień wysyłają je zarówno do Alicji, jak i Boba.

Chociaż można zakodować wiele problemów w `band_Beatles` tagach (na przykład lub `follows_Charlie`), tagi są prostymi ciągami, a nie właściwościami z wartościami. Rejestracja jest zgodna tylko z obecnością lub brakiem określonego tagu.

Aby uzyskać pełny samouczek krok po kroku na temat używania tagów do wysyłania do grup interesu, zobacz [Breaking News](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Usługa Azure Notification Hubs obsługuje maksymalnie 60 tagów na rejestrację.

## <a name="using-tags-to-target-users"></a>Używanie tagów do kierowania reklam do użytkowników

Innym sposobem używania tagów jest zidentyfikowanie wszystkich urządzeń skojarzonych z określonym użytkownikiem. Rejestrację można oznaczyć tagiem zawierającym identyfikator użytkownika, tak jak na poniższym rysunku:

![Oznaczanie użytkowników](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Na rysunku oznaczona `user_Alice` wiadomość dociera do wszystkich `user_Alice`urządzeń oznaczonych tagiem .

## <a name="tag-expressions"></a>Tagwy wyrażenia

Istnieją przypadki, w których powiadomienia muszą być kierowane na zestaw rejestracji identyfikowanych nie przez pojedynczy tag, ale przez wyrażenie logiczne przy użyciu tagów.

Rozważmy aplikację sportową, która wysyła przypomnienie do wszystkich w Bostonie o grze pomiędzy Red Sox i Cardinals. Jeśli aplikacja kliencka rejestruje tagi dotyczące zainteresowania zespołami i lokalizacją, powiadomienie powinno być kierowane do wszystkich w Bostonie, którzy są zainteresowani Red Sox lub Cardinals. Warunek ten można wyrazić za pomocą następującego wyrażenia logicznego:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Tagwy wyrażenia](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Wyrażenia znaczników obsługują typowe operatory logiczne, takie jak `AND` (`&&` `OR` ), (`||`), i `NOT` (`!`); mogą również zawierać nawiasy. Tag wyrażeń `OR` przy użyciu tylko operatorów można odwoływać się do 20 tagów; wyrażenie `AND` z operatorami, ale żadne operatory nie `OR` mogą odwoływać się do 10 tagów; w przeciwnym razie wyrażenia tagów są ograniczone do 6 tagów.

Oto przykład wysyłania powiadomień z wyrażeniami tagów przy użyciu SDK:

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
