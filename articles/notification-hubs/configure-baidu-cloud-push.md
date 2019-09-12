---
title: Konfigurowanie wypychania w chmurze Baidu na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia Baidu dla centrum powiadomień platformy Azure.
services: notification-hubs
author: jwargo
manager: patniko
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9feb5f66c5ae979a239c3349b2b1347ad307ce49
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884652"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Przestarzałe Skonfiguruj ustawienia wypychania w chmurze Baidu dla centrum powiadomień w Azure Portal

W tym artykule opisano sposób konfigurowania ustawień wypychania w chmurze Baidu dla centrum powiadomień platformy Azure przy użyciu Azure Portal.

> [!IMPORTANT]
> Ten samouczek jest przestarzały. 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Konfigurowanie usługi Baidu Cloud Push
Poniższa procedura zawiera instrukcje dotyczące konfigurowania ustawień wypychania w chmurze Baidu dla centrum powiadomień:

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Baidu (Android China)** w menu po lewej stronie. 
2. Wprowadź **klucz interfejsu API** uzyskany z konsoli Baidu w projekcie powiadomienia wypychane w chmurze Baidu. 
3. Wprowadź **klucz tajny** uzyskany z konsoli Baidu w projekcie powiadomienia wypychane w chmurze Baidu. 
4. Wybierz pozycję **Zapisz**. 

    ![Zrzut ekranu przedstawiający Notification Hubs, w którym jest wyświetlana konfiguracja Baidu (Android China) dla powiadomień wypychanych](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z samouczkiem zawierającym instrukcje krok po kroku dotyczące wypychania powiadomień do usługi Baidu przy użyciu usług Azure Notification Hubs i Baidu Cloud push, zobacz Wprowadzenie [do Notification Hubs przy użyciu Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
