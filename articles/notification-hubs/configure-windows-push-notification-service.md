---
title: Konfigurowanie systemu Windows Push Notification Service w usłudze Azure Notification Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia Windows Push Notification Service dla usługi Azure notification hub.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488334"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurowanie ustawień usługi powiadomień wypychanych Windows (WNS) dla Centrum powiadomień w witrynie Azure portal
W tym artykule pokazano, jak skonfigurować ustawienia hosta skryptów systemu Windows (WNS, Windows Notification Service) dla usługi Azure notification hub przy użyciu witryny Azure portal.  

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie zostało jeszcze utworzone Centrum powiadomień, utwórz ją teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie Centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurowanie usługi powiadomień wypychanych systemu Windows (WNS)

Poniższa procedura zawiera kroki, aby skonfigurować ustawienia usługi powiadomień wypychanych Windows (WNS) dla Centrum powiadomień: 

1. W witrynie Azure portal na **Centrum powiadomień** wybierz opcję **Windows (WNS)** w menu po lewej stronie.
2. Wprowadź wartości w polach **Identyfikator SID pakietu** i **Klucz zabezpieczeń**.
3. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu pokazujący pola Identyfikator SID pakietu i klucz zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać samouczek z instrukcjami krok po kroku dla wypychanie powiadomień do aplikacji platformy uniwersalnej Windows za pomocą usługi Azure Notification Hubs i usługi powiadomień wypychanych Windows (WNS), zobacz [wysyłać powiadomienia do aplikacji platformy uniwersalnej systemu Windows, korzystając z platformy Azure Usługa Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


