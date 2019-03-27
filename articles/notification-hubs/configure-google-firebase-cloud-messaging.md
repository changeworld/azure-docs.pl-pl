---
title: Konfigurowanie usługi Google Firebase Cloud komunikatów w usłudze Azure Notification Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi Azure notification hub przy użyciu ustawień usługi Google Firebase Cloud Messaging.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: d200f5746d5f7c62ff3a52873d5d5d6c3e1322dc
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488367"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurowanie ustawień Google Firebase Cloud Messaging (FCM) dla Centrum powiadomień w witrynie Azure portal
W tym artykule pokazano, jak skonfigurować ustawienia Google Firebase Cloud Messaging (FCM) dla usługi Azure notification hub przy użyciu witryny Azure portal.  

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie zostało jeszcze utworzone Centrum powiadomień, utwórz ją teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie Centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Konfigurowanie usługi Google Firebase Cloud Messaging (FCM)

Poniższa procedura zawiera kroki, aby skonfigurować ustawienia Google Firebase Cloud Messaging (FCM) dla Centrum powiadomień: 

1. W witrynie Azure portal na **Centrum powiadomień** wybierz opcję **Google (GCM/FCM)** w menu po lewej stronie. 
2. Wklej **klucz interfejsu API** w projekcie usługi FCM, który został wcześniej zapisany. 
3. Wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu pokazujący sposób konfigurowania usługi Notification Hubs dla usługi FCM Google](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Kolejne kroki
Samouczek z instrukcjami krok po kroku dla wypychanie powiadomień do urządzeń z systemem Android przy użyciu usługi Azure Notification Hubs i Google Firebase Cloud Messaging [wysyłać powiadomienia wypychane do urządzeń z systemem Android przy użyciu usługi Notification Hubs i Google FCM ](notification-hubs-android-push-notification-google-fcm-get-started.md).

