---
title: Konfigurowanie usługi powiadomień wypychanych systemu Windows na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia usługi powiadomień wypychanych systemu Windows dla centrum powiadomień platformy Azure.
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
ms.openlocfilehash: a7f7734d97cd67c133ff0cedc3ef2376967bcdf4
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212413"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Skonfiguruj ustawienia usługi Windows Push Notification Service (WNS) dla centrum powiadomień w Azure Portal
W tym artykule opisano sposób konfigurowania ustawień usługi powiadomień systemu Windows (WNS) dla centrum powiadomień platformy Azure przy użyciu Azure Portal.  

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurowanie usługi powiadomień WNS (WNS)

Poniższa procedura zawiera instrukcje dotyczące konfigurowania ustawień usługi Windows Push Notification Service (WNS) dla centrum powiadomień: 

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Windows (WNS)** w menu po lewej stronie.
2. Wprowadź wartości w polach **Identyfikator SID pakietu** i **Klucz zabezpieczeń**.
3. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający pola identyfikatora SID pakietu i klucza zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Następne kroki
Samouczek z instrukcjami krok po kroku dotyczącymi wypychania powiadomień do aplikacji platforma uniwersalna systemu Windows przy użyciu usługi Azure Notification Hubs i Windows Push Notification Service (WNS), zobacz [wysyłanie powiadomień do aplikacji platformy UWP przy użyciu powiadomień platformy Azure Centra](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


