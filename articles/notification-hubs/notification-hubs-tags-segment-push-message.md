---
title: Routing i wyrażeń tagów
description: W tym temacie wyjaśniono wyrażeń routingu i tag do usługi Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: ''
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: c3266698a6077e85806286fadf1f48b7194a4d88
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="routing-and-tag-expressions"></a>Wyrażenia routingu i tagów
## <a name="overview"></a>Przegląd
Wyrażeń tagów umożliwia określonych zestawów docelowych urządzeń lub dokładniej rejestracji podczas wysyłania powiadomień wypychanych przy użyciu usługi Notification Hubs.

## <a name="targeting-specific-registrations"></a>Przeznaczonych dla określonych rejestracji
Jedynym sposobem na docelowych określonych powiadomień jest rejestracji, aby skojarzyć tagi z nich, skierować tych tagów. Zgodnie z opisem w [zarządzania rejestracji](notification-hubs-push-notification-registration-management.md), aby otrzymać obsługi powiadomień aplikacja ma zarejestrować urządzenie w Centrum powiadomień wypychanych. Po utworzeniu rejestracji w Centrum powiadomień zaplecza aplikacji może wysyłać powiadomienia wypychane do niej.
Aplikacja wewnętrznej bazy danych można wybrać rejestracji do miejsca docelowego z określonym powiadomień w następujący sposób:

1. **Emisji**: powiadomienie wszystkich rejestracji w Centrum powiadomień.
2. **Tag**: wszystkich rejestracji, które zawierają określonego tagu powiadomienie.
3. **Tag wyrażenie**: powiadomienie wszystkich rejestracji, w których zestaw tagi pasują podanego wyrażenia.

## <a name="tags"></a>Tagi
Znacznik może być dowolny ciąg, maksymalnie 120 znaków, alfanumerycznego i następujące znaki inne niż alfanumeryczne: "_", "@", "#", ".",":", "-". W poniższym przykładzie przedstawiono aplikację, z którego może otrzymywać wyskakujące powiadomienia o muzyka określonych grup. W tym scenariuszu w prosty sposób powiadomienia trasy jest etykieta rejestracji przy użyciu tagów reprezentujących różne pasma, tak jak na poniższej ilustracji.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Na tej ilustracji oznakowane komunikat **Beatles** osiągnie tylko tablet zarejestrowany w tagu **Beatles**.

Aby uzyskać więcej informacji o tworzeniu rejestracji dla tagów, zobacz [zarządzania rejestracji](notification-hubs-push-notification-registration-management.md).

Możesz wysłać powiadomienia do tagów za pomocą metod wysyłania powiadomień z `Microsoft.Azure.NotificationHubs.NotificationHubClient` klasy w [centra powiadomień Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) zestawu SDK. Można również użyć środowiska Node.js lub wypychanie powiadomień interfejsów API REST.  Oto przykład przy użyciu zestawu SDK.

    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Tagów nie trzeba być wstępnie przygotowany i mogą odwoływać się do wielu pojęć specyficzny dla aplikacji. Na przykład użytkownicy tej przykładowej aplikacji można dodać komentarz dotyczący przedziałów i chcesz otrzymywać wyskakujące powiadomienia, nie tylko dla komentarze dotyczące ich ulubionych pasma, ale dla komentarzy do wszystkich swoich znajomych, niezależnie od tego, poza pasmem, na którym są komentarzy. Na poniższej ilustracji przedstawiono przykład tego scenariusza:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Na tej ilustracji Alicja jest zainteresowane w aktualizacjach dla Beatles i Robert jest zainteresowane w aktualizacjach dla Wailers. Robert jest również zainteresowana komentarze na marek i marek znajduje się w zainteresowani Wailers. Jeśli powiadomienie jest wysyłane do firmy marek komentarz na Beatles, zarówno Alicja i Robert odebrane.

Tagi może zakodować wiele problemów w tagów (na przykład "band_Beatles" lub "follows_Charlie"), są proste ciągów nie właściwości i wartości. Rejestracja jest zgodny tylko w obecności lub braku konkretnego znacznika.

Samouczek pełnej krok po kroku dotyczące sposobu użycia znaczników podczas wysyłania do grup zainteresowań, zobacz [fundamentalne wiadomości](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

## <a name="using-tags-to-target-users"></a>Przy użyciu tagów użytkowników docelowych
Innym sposobem użycia znaczników jest zidentyfikować wszystkie urządzenia określonego użytkownika. Rejestracje mogą być oznaczane tagu, który zawiera identyfikator użytkownika, tak jak na poniższej ilustracji:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Na tej ilustracji uid:Alice oznakowane wiadomość osiągnie wszystkich uid:Alice oznakowanych rejestracji; w związku z tym wszystkie urządzenia Alicji.

## <a name="tag-expressions"></a>Wyrażeń tagów
Istnieją przypadki, w których powiadomienie ma docelowy zestaw rejestracji, który jest identyfikowany nie przez jeden tag, ale przez wyrażenie logiczne w tagach.

Należy wziąć pod uwagę aplikacji sportowych, która wysyła przypomnienie wszystkim Boston o gry między czerwony Sox i Cardinals. Jeśli aplikacja kliencka rejestruje tagi o zainteresowanie zespołów i lokalizacji, powiadomienia mają być uwzględniani wszystkim Boston chcący Sox czerwony lub Cardinals. Ten warunek może zostać wyrażona z następującym wyrażeniem logiczna:

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Wyrażeń tagów może zawierać wszystkich operatorów logicznych, takich jak AND (& &), lub (|), a nie (!). Może również zawierać nawiasów. Wyrażeń tagów jest ograniczona do 20 znaczników, jeśli zawierają one tylko ORs; w przeciwnym razie są one ograniczone do 6 tagów.

Oto przykład wysyłania powiadomień przy użyciu wyrażeń tagów za pomocą zestawu SDK.

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
