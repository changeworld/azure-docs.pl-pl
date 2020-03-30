---
title: Konfigurowanie usługi Baidu Cloud Push w centrach powiadomień platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia Baidu dla centrum powiadomień platformy Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 759e35ba353f470ea3abc5f5d4182fa2b2ea0e73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212519"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Przestarzałe: konfigurowanie ustawień baidu cloud push dla centrum powiadomień w witrynie Azure portal

W tym artykule pokazano, jak skonfigurować ustawienia usługi Baidu Cloud Push dla centrum powiadomień platformy Azure przy użyciu witryny Azure portal.

> [!IMPORTANT]
> Ten samouczek jest przestarzały. 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli centrum powiadomień nie zostało jeszcze utworzone, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Konfigurowanie usługi Baidu Cloud Push
Poniższa procedura umożliwia skonfigurowanie ustawień Baidu Cloud Push dla centrum powiadomień:

1. W witrynie Azure portal na stronie **Centrum powiadomień** wybierz polecenie **Baidu (Android China)** w menu po lewej stronie. 
2. Wprowadź **klucz api uzyskany** z konsoli Baidu w projekcie wypychania w chmurze Baidu. 
3. Wprowadź **tajny klucz** uzyskany z konsoli Baidu w projekcie wypychania w chmurze Baidu. 
4. Wybierz **pozycję Zapisz**. 

    ![Zrzut ekranu przedstawiający centra powiadomień, który pokazuje konfigurację Baidu (Android China) dla powiadomień wypychanych](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać samouczek z instrukcjami krok po kroku dotyczącymi przekazywania powiadomień do Baidu przy użyciu usługi Azure Notification Hubs i Baidu Cloud Push, zobacz [Wprowadzenie do centrów powiadomień przy użyciu baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
