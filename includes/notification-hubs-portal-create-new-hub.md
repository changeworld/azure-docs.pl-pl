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
ms.openlocfilehash: a5d29e77f6ba10ed3069cb3f5a3c8089f49c237d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60423297"
---
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie i wybierz pozycję **Notification Hubs** w sekcji **Urządzenia przenośne**. Wybierz gwiazdkę (`*`) obok nazwy usługi, aby dodać ją do sekcji **ULUBIONE** w menu po lewej stronie. Po dodaniu usługi **Notification Hubs** do sekcji **ULUBIONE** wybierz ją z menu po lewej stronie. 

      ![Azure Portal — wybieranie usługi Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. Na stronie **Notification Hubs** wybierz pozycję **Dodaj** na pasku narzędzi. 

      ![Notification Hubs — przycisk Dodaj na pasku narzędzi](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. Na stronie **Centrum powiadomień** wykonaj następujące kroki: 
    1. Podaj **nazwę** **centrum** powiadomień.  
    2. Podaj **nazwę** **przestrzeni nazw**. Przestrzeń nazw zawiera co najmniej jedno centrum. 
    3. Wybierz **lokalizację** , w której ma zostać utworzone centrum powiadomień. 
    4. Wybierz istniejącą grupę zasobów lub wprowadź nazwę nowej **grupy zasobów**.
    5. Wybierz pozycję **Utwórz**. 

        ![Azure Portal — ustawianie właściwości centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Wybierz pozycję **Powiadomienia** (ikonę dzwonka), a następnie polecenie **Przejdź do zasobu**. Możesz również odświeżyć listę na stronie usługi **Notification Hubs** i wybrać centrum powiadomień. 

      ![Witryna Azure Portal -> Powiadomienia -> Przejdź do zasobu](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. Wybierz z listy pozycję **Zasady dostępu**. Zanotuj dwa parametry połączenia, które są dla Ciebie dostępne. Będą one potrzebne później do obsługi powiadomień push.

      >[!IMPORTANT]
      >**NIE UŻYWAJ** elementu DefaultFullSharedAccessSignature w aplikacji. Jest on przeznaczony tylko do użycia w zapleczu.
      >

      ![Azure Portal — parametry połączenia centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
