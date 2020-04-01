---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67509116"
---
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, a następnie wybierz pozycję Centra powiadomień w sekcji **Mobilne.** **Mobile** Wybierz ikonę gwiazdki obok nazwy usługi, aby dodać usługę do sekcji **ULUBIONE** w menu po lewej stronie. Po dodaniu **centrów powiadomień** do **ulubionych**wybierz je w menu po lewej stronie.

      ![Azure Portal — wybieranie usługi Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Na stronie **Notification Hubs** wybierz pozycję **Dodaj** na pasku narzędzi.

      ![Notification Hubs — przycisk Dodaj na pasku narzędzi](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Na stronie **Centrum powiadomień** wykonaj następujące kroki:

    1. Wprowadź nazwę w **Centrum powiadomień**.  

    1. Wprowadź nazwę w **obszarze Tworzenie nowego obszaru nazw**. Przestrzeń nazw zawiera co najmniej jedno centrum.

    1. Wybierz wartość z listy rozwijanej **Lokalizacja.** Ta wartość określa lokalizację, w której chcesz utworzyć koncentrator.

    1. Wybierz istniejącą grupę zasobów w **grupie zasobów**lub utwórz nazwę nowej grupy zasobów.

    1. Wybierz **pozycję Utwórz**.

        ![Azure Portal — ustawianie właściwości centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Wybierz **pozycję Powiadomienia** (ikona dzwonka), a następnie wybierz pozycję Przejdź do **zasobu**. Listę można również odświeżyć na stronie **Centra powiadomień** i wybrać centrum.

      ![Witryna Azure Portal -> Powiadomienia -> Przejdź do zasobu](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Wybierz z listy pozycję **Zasady dostępu**. Należy zauważyć, że dwa parametry połączenia są dostępne dla Ciebie. Będziesz ich potrzebować później do obsługi powiadomień wypychanych.

      >[!IMPORTANT]
      >*Nie* należy używać zasad **DefaultFullSharedAccessSignature** w aplikacji. To jest przeznaczone do użycia tylko w zapleczu.
      >

      ![Azure Portal — parametry połączenia centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
