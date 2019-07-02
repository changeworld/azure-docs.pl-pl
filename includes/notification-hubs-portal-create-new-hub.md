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
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509116"
---
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **wszystkich usług** w menu po lewej stronie, a następnie wybierz **usługi Notification Hubs** w **Mobile** sekcji. Wybierz ikonę gwiazdki obok nazwy usługi, aby dodać usługę do **ulubione** sekcji, w menu po lewej stronie. Po dodaniu **usługi Notification Hubs** do **ulubione**, wybierz go z menu po lewej stronie.

      ![Azure Portal — wybieranie usługi Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Na stronie **Notification Hubs** wybierz pozycję **Dodaj** na pasku narzędzi.

      ![Notification Hubs — przycisk Dodaj na pasku narzędzi](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Na stronie **Centrum powiadomień** wykonaj następujące kroki:

    1. Wprowadź nazwę w **Centrum powiadomień**.  

    1. Wprowadź nazwę w **Utwórz nową przestrzeń nazw**. Przestrzeń nazw zawiera co najmniej jedno centrum.

    1. Wybierz wartość z zakresu od **lokalizacji** pole listy rozwijanej. Ta wartość określa lokalizację, w której chcesz utworzyć Centrum.

    1. Wybierz istniejącą grupę zasobów w **grupy zasobów**, lub Utwórz nazwę nowej grupy zasobów.

    1. Wybierz pozycję **Utwórz**.

        ![Azure Portal — ustawianie właściwości centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Wybierz **powiadomienia** (ikonę dzwonka), a następnie wybierz pozycję **przejdź do zasobu**. Możesz też odświeżyć listy na **usługi Notification Hubs** strony i wybierz swoje Centrum.

      ![Witryna Azure Portal -> Powiadomienia -> Przejdź do zasobu](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Wybierz z listy pozycję **Zasady dostępu**. Należy pamiętać, że dostępne są dwa parametry połączenia. Będą potrzebne później do obsługi powiadomień wypychanych.

      >[!IMPORTANT]
      >Czy *nie* użyj **DefaultFullSharedAccessSignature** zasady w aplikacji. Jest on przeznaczony do użycia w sieci wewnętrznej tylko.
      >

      ![Azure Portal — parametry połączenia centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
