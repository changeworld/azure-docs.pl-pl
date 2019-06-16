---
title: Routing i wyrażenia tagu
description: W tym temacie wyjaśniono wyrażeń routingu i znaczników dla usługi Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2019
ms.author: jowargo
ms.openlocfilehash: 31a22aabc7b0f1d51a673ef8642037103badcc02
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61457808"
---
# <a name="routing-and-tag-expressions"></a>Wyrażenia routingu i znaczników

## <a name="overview"></a>Omówienie

Wyrażenia tagu umożliwiają konkretnych zestawów docelowych urządzeń lub dokładniej rejestracji podczas wysyłania powiadomień wypychanych przy użyciu usługi Notification Hubs.

## <a name="targeting-specific-registrations"></a>Przeznaczone dla określonych rejestracji

Jedynym sposobem na docelowym określonym powiadomień jest rejestracji, aby skojarzyć tagi z nich, następnie wskazać tych znaczników. Zgodnie z opisem w [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md), aby otrzymywać powiadomienia, aplikacja musi zarejestrować urządzenie, obsługa w Centrum powiadomień wypychanych. Po utworzeniu rejestracji w Centrum powiadomień aplikacji wewnętrznej bazy danych umożliwia wysyłanie powiadomień wypychanych do niego. Zaplecza aplikacji można wybrać rejestracje docelowo z określonych powiadomień w następujący sposób:

1. **Emisja**: powiadomienie wszystkich rejestracji w Centrum powiadomień.
2. **Tag**: wszystkie rejestracje zawierające określony tag otrzymać powiadomienia.
3. **Wyrażenia tagu**: powiadomienie wszystkich rejestracji, w których zestaw tagi pasują do określonego wyrażenia.

## <a name="tags"></a>`Tags`

Znacznik może być dowolny ciąg, maksymalnie 120 znaków, zawierającą alfanumeryczne i następujące znaki inne niż alfanumeryczne: "_", "@", "#", ".",":", "-". Poniższy przykład pokazuje aplikację, z którego może odbierać powiadomienia wyskakującego powiadomienia dotyczące muzyka określonych grup. W tym scenariuszu jest to prosty sposób kierowane powiadomienia etykiety rejestracji przy użyciu tagów, które reprezentują różne pasma, tak jak na poniższej ilustracji:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Na tym rysunku oznakowane komunikat **Beatles** osiągnie tylko zarejestrowane przy użyciu tagu tablet **Beatles**.

Aby uzyskać więcej informacji na temat tworzenia rejestracji dla tagów, zobacz [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md).

Powiadomienia można wysyłać do tagów przy użyciu metod wysyłania powiadomień z `Microsoft.Azure.NotificationHubs.NotificationHubClient` klasy w [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) zestawu SDK. Można również użyć Node.js lub wypychanie powiadomień interfejsów API REST.  Oto przykład korzystający z zestawu SDK.

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

Tagi nie trzeba być wstępnie przygotowany i mogą odwoływać się do wielu koncepcji specyficzny dla aplikacji. Na przykład użytkownicy tej przykładowej aplikacji można dodać komentarz dotyczący paski i chce otrzymywać wyskakujące powiadomienia, a nie tylko dla komentarzy na ich ulubionych pasma, ale także do wszystkich komentarzy z jego znajomymi, niezależnie od tego, poza pasmem, na którym są komentowania. Na poniższej ilustracji przedstawiono przykład tego scenariusza:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Na tym rysunku Alicja jest zainteresowane w aktualizacjach dla Beatles, a zainteresowane w aktualizacjach dla Wailers to Robert. Robert jest również zainteresowana komentarze firmy marek i marek znajduje się w są Państwo zainteresowani Wailers. Po wysłaniu powiadomienia dla firmy marek skomentować Beatles, Alice i Bob otrzymasz go.

Chociaż możesz zakodować wiele uwagi w znacznikach (na przykład "band_Beatles" lub "follows_Charlie"), tagi to zwykłe ciągi i nie właściwości z wartościami. Rejestracja jest takie samo tylko w obecności lub braku określony tag.

Aby uzyskać pełne instrukcje krok po kroku samouczek dotyczący sposobu użycia znaczników do wysyłania do grup zainteresowań, zobacz [istotne wiadomości](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Usługa Azure Notification Hubs obsługuje maksymalnie 60 tagów na rejestracji.

## <a name="using-tags-to-target-users"></a>Przy użyciu tagów użytkowników docelowych

Innym sposobem użycia znaczników jest aby zidentyfikować wszystkie urządzenia określonego użytkownika. Rejestracje mogą być oznaczane za pomocą znacznika, który zawiera identyfikator użytkownika, tak jak na poniższej ilustracji:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Na tym rysunku komunikat oznakowane uid: Alicja osiągnie wszystkie rejestracje oznakowane "uid:Alice"; Dzięki temu wszystkie urządzenia Alicji.

## <a name="tag-expressions"></a>Wyrażenia tagu

Istnieją przypadki, w których powiadomienie ma pod kątem zestaw rejestracji, który jest identyfikowany nie przez pojedynczy tag, ale przez wyrażenia logicznego w tagach.

Należy wziąć pod uwagę aplikacji sportowych, która wysyła przypomnienia dla wszystkich użytkowników w Bostonie o gra między Red Sox i Cardinals. Jeśli aplikacja kliencka rejestruje tagi o zainteresowania w tej lokalizacji i zespołów, następnie powiadomienie powinna być aplikacją docelową dla wszystkich użytkowników w Bostonie, kto jest zainteresowany Sox czerwony lub Cardinals. Ten stan może być wyrażona za pomocą następujących wyrażenie logiczne:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Wyrażenia tagu może zawierać wszystkich operatorów logicznych, takich jak AND (& &), lub (|) i NOT (!). Może również zawierać nawiasów. Wyrażenia tagu jest ograniczona do 20 znaczników, jeśli zawierają one tylko ORs; w przeciwnym razie są one ograniczone do 6 tagów.

Oto przykład wysyłania powiadomień przy użyciu wyrażeń tagów za pomocą zestawu SDK.

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
