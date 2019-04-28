---
title: Jak wysyłanie powiadomień zaplanowanych | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób używania zaplanowane powiadomienia za pomocą usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: powiadomienia wypychane, powiadomienia wypychane, powiadomienia wypychane planowania
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
origin.date: 04/14/2018
ms.date: 02/25/2019
ms.author: v-biyu
ms.openlocfilehash: af0de9e8c18644f4ae200f6546c0dd0a41320f9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457689"
---
# <a name="how-to-send-scheduled-notifications"></a>Instrukcje: Wysyłanie powiadomień zaplanowanych

Jeśli scenariusz, w którym mają zostać wysłane powiadomienie w pewnym momencie w przyszłości, ale nie mają łatwy sposób wake zapasową swojego kodu zaplecza, aby wysłać powiadomienie. Usługa notification hubs w warstwie standardowa obsługuje funkcja, która umożliwia planowanie powiadomień się maksymalnie siedem dni w przyszłości.


## <a name="schedule-your-notifications"></a>Zaplanuj powiadomienia
Podczas wysyłania powiadomienia, po prostu użyć [ `ScheduledNotification` klasy](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) w SDK centra powiadomień, jak pokazano w poniższym przykładzie:

```c#
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Anuluj zaplanowane powiadomienia
Ponadto można anulować wcześniej zaplanowane powiadomienia za pomocą jego identyfikator powiadomienia:

```c#
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Nie ma ograniczeń liczby powiadomień zaplanowanych, który można wysłać.

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące samouczki:

 - [Powiadomienia wypychane do wszystkich zarejestrowanych urządzeń](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Wysyłanie powiadomień push do konkretnych urządzeń](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Wypychanie powiadomień zlokalizowanych](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Wysyłanie powiadomień push do konkretnych użytkowników](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Wypychanie powiadomień na podstawie lokalizacji](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
