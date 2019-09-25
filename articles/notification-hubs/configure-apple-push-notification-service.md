---
title: Konfigurowanie Apple Push Notification Service na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować centrum powiadomień platformy Azure przy użyciu ustawień Apple Push Notification Service (APNS).
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
ms.openlocfilehash: 74e7e3c74934e292b668b8bff594a5efbca19716
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212550"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Skonfiguruj ustawienia usługi Apple Push Notification Service (APNS) dla centrum powiadomień w Azure Portal
W tym artykule opisano sposób konfigurowania ustawień usług Apple Push Notification Service (APNS) dla centrum powiadomień platformy Azure przy użyciu Azure Portal. 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Konfigurowanie Apple Push Notification Service

Poniższa procedura zawiera instrukcje dotyczące konfigurowania ustawień usługi Apple Push Notification Service (APNS) dla centrum powiadomień:

1. W Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Apple (APNs)** w menu po lewej stronie.

1. W obszarze **tryb uwierzytelniania**wybierz pozycję **certyfikat** lub **token**.

   a. W przypadku wybrania opcji **certyfikat**:
   * Wybierz ikonę pliku, a następnie wybierz plik *. p12* , który chcesz przekazać.
   * Wprowadź hasło.
   * Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili aplikację ze sklepu, wybierz pozycję tryb produkcyjny.

     ![Zrzut ekranu przedstawiający konfigurację certyfikatu APNS w Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. W przypadku wybrania opcji **token**:

   * Wprowadź wartości dla **identyfikatora klucza**, **identyfikatora pakietu**, **identyfikatora zespołu**i **tokenu**.
   * Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili aplikację ze sklepu, wybierz pozycję tryb produkcyjny.

     ![Zrzut ekranu przedstawiający konfigurację tokenu usługi APNS w Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z samouczkiem zawierającym instrukcje krok po kroku dotyczące wypychania powiadomień do urządzeń z systemem iOS, zobacz następujący artykuł: [Powiadomienia wypychane do urządzeń z systemem iOS przy użyciu Notification Hubs i usługi APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
