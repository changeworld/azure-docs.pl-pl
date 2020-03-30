---
title: Jak wysyłać zaplanowane powiadomienia | Dokumenty firmy Microsoft
description: W tym temacie opisano przy użyciu zaplanowanych powiadomień z centrum powiadomień platformy Azure.
services: notification-hubs
documentationcenter: .net
keywords: powiadomienia wypychania,powiadomienia wypychające,planowanie powiadomień wypychanych
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ef5eedaa903480ae670f9bc48d0af89744a99d22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71213005"
---
# <a name="how-to-send-scheduled-notifications"></a>Jak: Wysyłanie zaplanowanych powiadomień

Jeśli masz scenariusz, w którym chcesz wysłać powiadomienie w pewnym momencie w przyszłości, ale nie mają łatwy sposób, aby wybudzić kod zaplecza, aby wysłać powiadomienie. Centra powiadomień warstwy standardowej obsługują funkcję, która umożliwia planowanie powiadomień do siedmiu dni w przyszłości.


## <a name="schedule-your-notifications"></a>Planowanie powiadomień
Podczas wysyłania powiadomienia, po prostu użyj [ `ScheduledNotification` klasy](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) w centrum powiadomień SDK, jak pokazano w poniższym przykładzie:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Anulowanie zaplanowanych powiadomień
Ponadto można anulować wcześniej zaplanowane powiadomienie za pomocą jego notificationId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Nie ma ograniczeń co do liczby zaplanowanych powiadomień, które możesz wysłać.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące samouczki:

 - [Powiadomienia wypychane do wszystkich zarejestrowanych urządzeń](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Wypychanie powiadomień do konkretnych urządzeń](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Wypychanie powiadomień zlokalizowanych](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Wypychanie powiadomień do konkretnych użytkowników](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Wypychanie powiadomień na podstawie lokalizacji](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
