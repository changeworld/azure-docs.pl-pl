---
title: Konfigurowanie usługi Google Firebase Cloud Messaging w centrach powiadomień platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować centrum powiadomień platformy Azure za pomocą ustawień Google Firebase Cloud Messaging.
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
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127467"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurowanie ustawień Google Firebase dla centrum powiadomień w witrynie Azure portal

W tym artykule pokazano, jak skonfigurować ustawienia google firebase cloud messaging (FCM) dla centrum powiadomień platformy Azure przy użyciu witryny Azure portal.  

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli centrum powiadomień nie zostało jeszcze utworzone, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Konfigurowanie wiadomości w chmurze Google Firebase (FCM)

Poniższa procedura umożliwia skonfigurowanie ustawień google firebase cloud messaging (FCM) dla centrum powiadomień: 

1. W witrynie Azure portal na stronie **Centrum powiadomień** wybierz pozycję **Google (GCM/FCM)** w menu po lewej stronie. 
2. Wklej **klucz interfejsu API** dla projektu FCM, który został zapisany wcześniej. 
3. Wybierz **pozycję Zapisz**. 

   ![Zrzut ekranu przedstawiający sposób konfigurowania centrów powiadomień dla Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z samouczkiem zawierającym instrukcje krok po kroku dotyczące przekazywania powiadomień na urządzenia z systemem Android przy użyciu centrów powiadomień platformy Azure i komunikatów Google Firebase Cloud Messaging, zobacz [Powiadomienia wypychania na urządzenia z systemem Android przy użyciu Centrów powiadomień i Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

