---
title: Konfigurowanie usługi powiadomień wypychanych firmy Apple w centrach powiadomień platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować centrum powiadomień platformy Azure za pomocą ustawień usługi apns (Apple Push Notification Service).
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
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127516"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurowanie ustawień usługi powiadomień wypychanych firmy Apple dla centrum powiadomień w witrynie Azure portal

W tym artykule pokazano, jak skonfigurować ustawienia usługi APNS (Apple Push Notification Service) dla centrum powiadomień platformy Azure przy użyciu witryny Azure portal. 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli centrum powiadomień nie zostało jeszcze utworzone, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Konfigurowanie usługi powiadomień push firmy Apple

Poniższa procedura umożliwia skonfigurowanie ustawień usługi powiadomień wypychanych (APNS) firmy Apple dla centrum powiadomień:

1. W witrynie Azure portal na stronie **Centrum powiadomień** wybierz pozycję **Apple (APNS)** w menu po lewej stronie.

1. W **trybie uwierzytelniania**wybierz **certyfikat** lub **token**.

   a. W przypadku **wybrania opcji Certyfikat**:
   * Wybierz ikonę pliku, a następnie wybierz plik *p12,* który chcesz przekazać.
   * Wprowadź hasło.
   * Wybierz tryb **Piaskownica**. Aby wysłać powiadomienia wypychane do użytkowników, którzy zakupili aplikację ze sklepu, wybierz tryb **produkcji.**

     ![Zrzut ekranu przedstawiający konfigurację certyfikatu APNS w witrynie Azure portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Jeśli wybierzesz **Token:**

   * Wprowadź wartości **identyfikatora klucza,** **identyfikatora pakietu,** **identyfikatora zespołu**i **tokenu.**
   * Wybierz tryb **Piaskownica**. Aby wysłać powiadomienia wypychane do użytkowników, którzy zakupili aplikację ze sklepu, wybierz tryb **produkcji.**

     ![Zrzut ekranu przedstawiający konfigurację tokenu APNS w witrynie Azure portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać samouczek z instrukcjami krok po kroku dotyczącymi przekazywania powiadomień na urządzenia z systemem iOS, zobacz następujący artykuł: [Powiadomienia wypychania do urządzeń z systemem iOS przy użyciu centrów powiadomień i usługi APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
