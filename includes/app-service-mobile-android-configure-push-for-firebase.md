---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: bb03e4b5b04a0272d8fa9b032da5adb50878b620
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811627"
---
1. W [witryny Azure portal](https://portal.azure.com/), kliknij przycisk **Przeglądaj wszystkie** > **App Services**, a następnie kliknij przycisk swoje zaplecze funkcji Mobile Apps. W obszarze **ustawienia**, kliknij przycisk **wypychania usługi App Service**, a następnie kliknij nazwę Centrum powiadomień.
2. Przejdź do **Google (GCM)**, wprowadź **klucz serwera** wartość, która uzyskany z usługi Firebase w poprzedniej procedurze, a następnie kliknij przycisk **Zapisz**.

    ![Ustaw klucz interfejsu API w portalu](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Zaplecze funkcji Mobile Apps jest teraz skonfigurowane do użycia usługi Firebase Cloud Messaging. Dzięki temu można wysyłać powiadomienia wypychane do aplikacji uruchomionej na urządzeniu z systemem Android za pomocą Centrum powiadomień.
