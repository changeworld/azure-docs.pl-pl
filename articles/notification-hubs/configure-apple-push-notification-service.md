---
title: Konfigurowanie Apple Push Notification Service w usłudze Azure Notification Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi Azure notification hub przy użyciu ustawień firmy Apple Push Notification Service (APNS).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9a9db9f05895569b050e56cdeec1ee2ee25af0df
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237817"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurowanie ustawień firmy Apple Push Notification Service (APNS) dla Centrum powiadomień w witrynie Azure portal
W tym artykule pokazano, jak skonfigurować ustawienia Apple Push Notification Service (APNS) dla usługi Azure notification hub przy użyciu witryny Azure portal. 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie zostało jeszcze utworzone Centrum powiadomień, utwórz ją teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie Centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Konfigurowanie Apple Push Notification Service

Poniższa procedura zawiera kroki, aby skonfigurować ustawienia Apple Push Notification Service (APNS) dla Centrum powiadomień:

1. W witrynie Azure portal na **Centrum powiadomień** wybierz opcję **Apple (APNS)** w menu po lewej stronie.

1. Aby uzyskać **tryb uwierzytelniania**, wybierz opcję **certyfikatu** lub **tokenu**.

   a. Jeśli wybierzesz **certyfikatu**:
   * Wybierz ikonę pliku, a następnie wybierz *.p12* pliku, który chcesz przekazać.
   * Wprowadź hasło.
   * Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili twoją aplikację ze sklepu, zaznacz **produkcji** trybu.

     ![Zrzut ekranu przedstawiający APNS certyfikatu konfiguracji w witrynie Azure portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Jeśli wybierzesz **tokenu**:

   * Wprowadź wartości w polach **identyfikator klucza**, **identyfikator pakietu**, **identyfikator zespołu**, i **tokenu**.
   * Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili twoją aplikację ze sklepu, zaznacz **produkcji** trybu.

     ![Zrzut ekranu przedstawiający APNS token konfiguracji w witrynie Azure portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Kolejne kroki
Samouczek z instrukcjami krok po kroku dla wypychanie powiadomień do urządzeń z systemem iOS zobacz następujący artykuł: [Wypychanie powiadomień do urządzeń z systemem iOS przy użyciu usługi Notification Hubs i usługi APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
