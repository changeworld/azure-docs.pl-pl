---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183734"
---
Funkcja Aplikacji mobilnych usługi Azure App Service używa [usługi Azure Notification Hubs] do wysyłania wypycheń, więc będziesz konfigurować centrum powiadomień dla aplikacji mobilnej.

1. W [witrynie Azure portal]przejdź do **usługi App Services,** a następnie wybierz zaplecze aplikacji. W obszarze **Ustawienia**wybierz pozycję **Wypychaj**.
2. Aby dodać zasób centrum powiadomień do aplikacji, wybierz pozycję **Połącz**. Można utworzyć koncentrator lub połączyć się z istniejącym.

    ![Konfigurowanie koncentratora](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Teraz masz połączenie centrum powiadomień z projektem zaplecza aplikacji mobilnych. Później można skonfigurować tego centrum powiadomień, aby połączyć się z systemem powiadomień platformy (PNS) do wypychania do urządzeń.

[Portal Azure]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
