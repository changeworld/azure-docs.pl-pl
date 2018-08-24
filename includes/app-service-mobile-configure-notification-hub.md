---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 0a74079677a084b2d4e8221cf5a74b356126811d
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42817455"
---
Funkcja Mobile Apps w usłudze Azure App Service używa usługi [Azure Notification Hubs] wysyłać wypchnięciom, dzięki czemu będą konfigurowane Centrum powiadomień dla aplikacji mobilnej.

1. W [Azure Portal], przejdź do **App Services**, a następnie wybierz pozycję w zapleczu aplikacji. W obszarze **ustawienia**, wybierz opcję **wypychania**.
2. Aby dodać zasób Centrum powiadomień do aplikacji, wybierz **Connect**. Można utworzyć Centrum lub nawiązać połączenie z istniejącą grupę.

    ![Konfigurowanie Centrum](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Teraz nawiązano połączenie Centrum powiadomień do projektu zaplecza funkcji Mobile Apps. Później można skonfigurować tego Centrum powiadomień, aby nawiązać połączenie z systemu powiadomień platformy (PNS), aby wypchnąć do urządzeń.

[Azure Portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
