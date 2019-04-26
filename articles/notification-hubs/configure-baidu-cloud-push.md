---
title: Konfigurowanie usługi Baidu Cloud Push w usłudze Azure Notification Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia usługi Baidu dla usługi Azure notification hub.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9942b1d4d8b5d538f5150a36e596753282039be7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234255"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurowanie ustawień usługi Baidu Cloud Push Centrum powiadomień w witrynie Azure portal
W tym artykule pokazano, jak skonfigurować ustawienia usługi Azure notification hub usługi Baidu Cloud Push przy użyciu witryny Azure portal. 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie zostało jeszcze utworzone Centrum powiadomień, utwórz ją teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie Centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Konfigurowanie usługi Baidu Cloud Push
Poniższa procedura zawiera kroki, aby skonfigurować ustawienia usługi Baidu Cloud Push dla Centrum powiadomień:

1. W witrynie Azure portal na **Centrum powiadomień** wybierz opcję **Baidu (Android China)** w menu po lewej stronie. 
2. Wprowadź **klucz interfejsu Api** uzyskany w konsoli Baidu w projektu powiadomień wypychanych w chmurze Baidu. 
3. Wprowadź **klucz tajny** uzyskany w konsoli Baidu w projektu powiadomień wypychanych w chmurze Baidu. 
4. Wybierz pozycję **Zapisz**. 

    ![Zrzut ekranu z usługi Notification Hubs przedstawiający konfigurację powiadomień wypychanych Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać samouczek z instrukcjami krok po kroku dla wypychanie powiadomień do usługi Baidu, za pomocą usługi Azure Notification Hubs i usługi Baidu Cloud Push, zobacz [Rozpoczynanie pracy z usługą Notification Hubs przy użyciu usługi Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
