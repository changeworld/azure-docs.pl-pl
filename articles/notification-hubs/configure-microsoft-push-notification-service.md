---
title: Konfigurowanie usługi powiadomień wypychanych firmy Microsoft w usłudze Azure Notification Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia usługi powiadomień wypychanych firmy Microsoft dla usługi Azure notification hub.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488353"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurowanie ustawień usługi powiadomień wypychanych firmy Microsoft (MPNS) dla Centrum powiadomień w witrynie Azure portal
W tym artykule pokazano, jak skonfigurować ustawienia usługi powiadomień wypychanych firmy Microsoft (MPNS) dla usługi Azure notification hub przy użyciu witryny Azure portal. 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie zostało jeszcze utworzone Centrum powiadomień, utwórz ją teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie Centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Konfigurowanie usługi powiadomień wypychanych firmy Microsoft (MPNS)

Poniższa procedura zawiera kroki, aby skonfigurować ustawienia usługi powiadomień wypychanych firmy Microsoft (MPNS) dla Centrum powiadomień: 

1. W witrynie Azure portal na **Centrum powiadomień** wybierz opcję **Windows Phone (MPNS)** w menu po lewej stronie.
1. Włącz nieuwierzytelnione lub uwierzytelniony powiadomień wypychanych:

   a. Aby włączyć nieuwierzytelnione wypychanie powiadomień, wybierz **Włącz nieuwierzytelnione wypychanie** > **Zapisz**.

      ![Zrzut ekranu pokazujący sposób Włącz nieuwierzytelnione wypychanie powiadomień](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Aby włączyć powiadomienia wypychane uwierzytelnionego:
      * Na pasku narzędzi wybierz **Przekaż certyfikat**.
      * Wybierz ikonę pliku, a następnie wybierz plik certyfikatu.
      * Wprowadź hasło certyfikatu.
      * Kliknij przycisk **OK**.
      * Na **Windows Phone (MPNS)** wybierz opcję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać samouczek z instrukcjami krok po kroku do wypychania powiadomień do urządzeń Windows Phone przy użyciu usługi Azure Notification Hubs i usługi powiadomień wypychanych firmy Microsoft (MPNS), zobacz [powiadomienia wypychane do aplikacji Windows Phone za pomocą powiadomień Koncentratory](notification-hubs-windows-mobile-push-notifications-mpns.md).

