---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140235"
---
Funkcja Mobile Apps w usłudze Azure App Service używa [Azure Notification Hubs] wysyłać wypchnięciom, dzięki czemu będą konfigurowane Centrum powiadomień dla aplikacji mobilnej.

1. W [Azure Portal], przejdź do **App Services**, a następnie wybierz pozycję w zapleczu aplikacji. W obszarze **ustawienia**, wybierz opcję **wypychania**.
2. Aby dodać zasób Centrum powiadomień do aplikacji, wybierz **Connect**. Można utworzyć Centrum lub nawiązać połączenie z istniejącą grupę.

    ![Konfigurowanie Centrum](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Teraz nawiązano połączenie Centrum powiadomień do projektu zaplecza funkcji Mobile Apps. Później można skonfigurować tego Centrum powiadomień, aby nawiązać połączenie z systemu powiadomień platformy (PNS), aby wypchnąć do urządzeń.

[Azure Portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
