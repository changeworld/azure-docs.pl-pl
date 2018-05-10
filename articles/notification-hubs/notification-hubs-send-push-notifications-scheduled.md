---
title: Jak wysyłać powiadomienia zaplanowane | Dokumentacja firmy Microsoft
description: W tym temacie opisano, w usłudze Azure Notification Hubs przy użyciu powiadomienia zaplanowane.
services: notification-hubs
documentationcenter: .net
keywords: powiadomienia wypychane, powiadomień wypychanych, planowania powiadomień wypychanych
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0f4055a11d22604c0936685a7a2be3d56b259a5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-send-scheduled-notifications"></a>Porady: Wysyłanie powiadomień według harmonogramu
## <a name="overview"></a>Przegląd
Jeśli scenariusz, w którym chcesz wysłać powiadomienie w pewnym momencie w przyszłości, ale nie mają w prosty sposób wake zapasowej swój kod zaplecza, aby wysłać powiadomienie. Centra powiadomień w warstwie standardowa obsługuje funkcja, która umożliwia planowanie powiadomienia się na 7 dni w przyszłości.

Podczas wysyłania powiadomienia, po prostu użyć [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) klasy w zestaw SDK usługi Notification Hubs, jak pokazano w poniższym przykładzie:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Ponadto można anulować wcześniej zaplanowanym powiadomienia za pomocą jego notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Nie ma żadnych limitów liczby zaplanowanych powiadomienia, który można wysłać.

