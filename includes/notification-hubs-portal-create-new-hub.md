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
ms.openlocfilehash: 244a4ebe20863945bfc3b6236e70e786387c8909
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116678"
---
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie i wybierz pozycję **Notification Hubs** w sekcji **Urządzenia przenośne**. Wybierz ikonę gwiazdki obok nazwy usługi, aby dodać usługę do **ulubione** sekcji, w menu po lewej stronie. Po dodaniu **usługi Notification Hubs** do **ulubione**, wybierz go z menu po lewej stronie.

      ![Azure Portal — wybieranie usługi Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Na stronie **Notification Hubs** wybierz pozycję **Dodaj** na pasku narzędzi.

      ![Notification Hubs — przycisk Dodaj na pasku narzędzi](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Na stronie **Centrum powiadomień** wykonaj następujące kroki:

    1. Wprowadź nazwę w **Centrum powiadomień**.  

    1. Wprowadź nazwę w **Utwórz nową przestrzeń nazw**. Przestrzeń nazw zawiera co najmniej jedno centrum.

    1. Wybierz wartość z zakresu od **lokalizacji** pole listy rozwijanej. Ta wartość określa lokalizację, w której chcesz utworzyć Centrum powiadomień.

    1. Wybierz istniejącą grupę zasobów w **grupy zasobów**, lub Utwórz nazwę nowej grupy zasobów.

    1. Wybierz pozycję **Utwórz**.

        ![Azure Portal — ustawianie właściwości centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Wybierz **powiadomienia** (ikonę dzwonka) i wybierz **przejdź do zasobu**. Możesz też odświeżyć listy na **usługi Notification Hubs** strony i wybierz Centrum powiadomień.

      ![Witryna Azure Portal -> Powiadomienia -> Przejdź do zasobu](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Wybierz z listy pozycję **Zasady dostępu**. Zanotuj dwa parametry połączenia, które są dla Ciebie dostępne. Będą później potrzebne do obsługi powiadomień wypychanych.

      >[!IMPORTANT]
      >Czy *nie* użyj **DefaultFullSharedAccessSignature** zasady w aplikacji. Jest on przeznaczony do użycia w sieci wewnętrznej tylko.
      >

      ![Azure Portal — parametry połączenia centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
