---
title: Konfigurowanie usługi powiadomień wypychanych firmy Microsoft w centrach powiadomień platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia usługi powiadomień wypychanych firmy Microsoft dla centrum powiadomień platformy Azure.
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
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127339"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Konfigurowanie ustawień usługi mpns (Microsoft Push Notification Service) w witrynie Azure portal

W tym artykule pokazano, jak skonfigurować ustawienia usługi microsoft push notification service (MPNS) dla centrum powiadomień platformy Azure przy użyciu witryny Azure portal. 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli centrum powiadomień nie zostało jeszcze utworzone, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Konfigurowanie usługi powiadomień wypychanych firmy Microsoft (MPNS)

Poniższa procedura umożliwia skonfigurowanie ustawień usługi mpns (Push Notification Service) firmy Microsoft dla centrum powiadomień: 

1. W witrynie Azure portal na stronie **Centrum powiadomień** wybierz pozycję **Windows Phone (MPNS)** w menu po lewej stronie.
1. Włącz nieuwierzyciowane lub uwierzytelnione powiadomienia wypychane:

   a. Aby włączyć nieuwierzyte powiadomienia wypychane, wybierz **pozycję Włącz nieuwierzytyny wypychacz** > **Zapisz**.

      ![Zrzut ekranu przedstawiający sposób włączania nieuwierzywalszych powiadomień wypychanych](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Aby włączyć uwierzytelnione powiadomienia wypychane:
      * Na pasku narzędzi wybierz pozycję **Przekaż certyfikat**.
      * Wybierz ikonę pliku, a następnie wybierz plik certyfikatu.
      * Wprowadź hasło certyfikatu.
      * Kliknij przycisk **OK**.
      * Na stronie **Windows Phone (MPNS)** wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać samouczek z instrukcjami krok po kroku dotyczącymi przekazywania powiadomień do urządzeń z systemem Windows Phone przy użyciu usługi Azure Notification Hubs i Microsoft Push Notification Service (MPNS), zobacz [Powiadomienia wypychane do aplikacji systemu Windows Phone przy użyciu Centrów powiadomień](notification-hubs-windows-mobile-push-notifications-mpns.md).

