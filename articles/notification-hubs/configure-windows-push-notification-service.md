---
title: Konfigurowanie usługi powiadomień wypychanych systemu Windows w centrach powiadomień platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia usługi powiadomień wypychanych systemu Windows dla Centrum powiadomień platformy Azure.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127311"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Konfigurowanie ustawień usługi powiadomień wypychanych systemu Windows w portalu Azure

W tym artykule pokazano, jak skonfigurować ustawienia usługi powiadomień systemu Windows (WNS) dla centrum powiadomień platformy Azure przy użyciu witryny Azure portal.  

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli centrum powiadomień nie zostało jeszcze utworzone, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurowanie usługi powiadomień wypychanych systemu Windows (WNS)

Poniższa procedura umożliwia skonfigurowanie ustawień usługi powiadomień wypychanych systemu Windows (WNS) dla centrum powiadomień: 

1. W witrynie Azure portal na stronie **Centrum powiadomień** wybierz pozycję **Windows (WNS)** w menu po lewej stronie.
2. Wprowadź wartości w polach **Identyfikator SID pakietu** i **Klucz zabezpieczeń**.
3. Wybierz **pozycję Zapisz**.

   ![Zrzut ekranu przedstawiający pola Identyfikator SID pakietu i klucz zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z samouczkiem zawierającym instrukcje krok po kroku dotyczące przekazywania powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs i usługi powiadomień wypychanych systemu Windows (WNS), zobacz Wysyłanie powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs i Usługi powiadomień wypychanych systemu Windows (WNS), zobacz [Wysyłanie powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


