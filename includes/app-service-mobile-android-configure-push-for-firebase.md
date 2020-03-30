---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183736"
---
1. W [witrynie Azure portal](https://portal.azure.com/)kliknij pozycję **Przeglądaj wszystkie** > **usługi aplikacji,** a następnie kliknij zaplecze aplikacji mobilnych. W obszarze **Ustawienia**kliknij pozycję **Wypychanie usługi app service**, a następnie kliknij nazwę centrum powiadomień.
2. Przejdź do **google (GCM)**, wprowadź wartość **klucza serwera** uzyskaną z Firebase w poprzedniej procedurze, a następnie kliknij przycisk **Zapisz**.

    ![Ustawianie klucza interfejsu API w portalu](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Zaplecze aplikacji mobilnych jest teraz skonfigurowane do korzystania z firebase cloud messaging. Dzięki temu można wysyłać powiadomienia wypychane do aplikacji uruchomionej na urządzeniu z systemem Android, za pomocą centrum powiadomień.
