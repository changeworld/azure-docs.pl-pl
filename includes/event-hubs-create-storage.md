---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: e499a0f7bec47e672c599c729a15cc3e3d04a28a
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471666"
---
## <a name="create-a-storage-account-for-event-processor-host"></a>Tworzenie konta magazynu dla hosta procesora zdarzeń
Host procesora zdarzeń to inteligentny agent, który upraszcza odbieranie zdarzeń z usługi Event Hubs przez zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami. W przypadku obsługi punktów kontrolnych host procesora zdarzeń wymaga konta magazynu. Poniższy przykład pokazuje, jak utworzyć konto magazynu oraz uzyskać jego klucze na potrzeby dostępu:

1. W witrynie Azure portal i wybierz pozycję **Utwórz zasób** w lewym górnym rogu ekranu.

2. Wybierz pozycję **Storage**, a następnie wybierz pozycję **Konto usługi Storage — Blob, File, Table, Queue**.
   
    ![Wybierz konto magazynu](./media/event-hubs-create-storage/create-storage1.png)

3. Na **Tworzenie konta magazynu** strony, wykonaj następujące czynności: 

    1. Wprowadź nazwę konta magazynu. 
    2. Wybierz subskrypcję platformy Azure, która zawiera Centrum zdarzeń.
    3. Wybierz grupę zasobów, która ma Centrum zdarzeń.
    4. Wybierz lokalizację, w której chcesz utworzyć zasób. 
    5. Następnie kliknij przycisk **przeglądu + Utwórz**.
   
    ![Utwórz konto magazynu — strony](./media/event-hubs-create-storage/create-storage2.png)

4. Na **Przejrzyj + Utwórz** strony, sprawdź wartości i wybierz **Utwórz**. 

    ![Przejrzyj ustawienia konta magazynu i Utwórz](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Po wyświetleniu **wdrożenia zakończone pomyślnie** komunikatu wybierz **stało się do zasobu** w górnej części strony. Na stronie konta magazynu można również uruchomić, wybierając konto magazynu z listy zasobów.  

    ![Wybierz konto magazynu z wdrożenia](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. W **Essentials** wybierz **obiektów blob**. 

    ![Wybierz usługę obiektów blob](./media/event-hubs-create-storage/select-blobs-service.png)
1. Wybierz **+ kontener** u góry strony, wprowadź **nazwa** dla kontenera, a następnie wybierz **OK**. 

    ![Tworzenie kontenera obiektów blob](./media/event-hubs-create-storage/create-blob-container.png)
1. Wybierz **klucze dostępu** w menu po lewej stronie, a następnie skopiuj wartość **klucz1**. 

    Zapisz następujące wartości do Notatnika lub innej tymczasowej lokalizacji.
    - Nazwa konta magazynu
    - Klucz dostępu dla konta magazynu
    - Nazwa kontenera