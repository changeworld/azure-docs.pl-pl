---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140287"
---
1. W [witryny Azure portal](https://portal.azure.com/), kliknij przycisk **Przeglądaj wszystkie** > **App Services**, a następnie kliknij przycisk swoje zaplecze funkcji Mobile Apps. W obszarze **ustawienia**, kliknij przycisk **wypychania usługi App Service**, a następnie kliknij nazwę Centrum powiadomień.
2. Przejdź do **Google (GCM)**, wprowadź **klucz serwera** wartość, która uzyskany z usługi Firebase w poprzedniej procedurze, a następnie kliknij przycisk **Zapisz**.

    ![Ustaw klucz interfejsu API w portalu](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Zaplecze funkcji Mobile Apps jest teraz skonfigurowane do użycia usługi Firebase Cloud Messaging. Dzięki temu można wysyłać powiadomienia wypychane do aplikacji uruchomionej na urządzeniu z systemem Android za pomocą Centrum powiadomień.
