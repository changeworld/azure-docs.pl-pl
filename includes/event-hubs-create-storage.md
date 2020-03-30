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
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692552"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Tworzenie konta magazynu dla hosta procesora zdarzeń
Host procesora zdarzeń to inteligentny agent, który upraszcza odbieranie zdarzeń z usługi Event Hubs przez zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami. W przypadku obsługi punktów kontrolnych host procesora zdarzeń wymaga konta magazynu. Poniższy przykład pokazuje, jak utworzyć konto magazynu oraz uzyskać jego klucze na potrzeby dostępu:

1. Z menu Portalu platformy Azure wybierz polecenie **Utwórz zasób**.

    ![Tworzenie elementu menu zasobu w portalu Microsoft Azure](./media/event-hubs-create-storage/create-resource.png)

2. Wybierz**konto** **Magazyn** > .
   
    ![Wybieranie konta magazynu, witryna Microsoft Azure portal](./media/event-hubs-create-storage/select-storage-account.png)

3. Na stronie **Utwórz konto magazynu** wykonaj następujące kroki: 

   1. Wprowadź **nazwę konta magazynu**.
   2. Wybierz **subskrypcję** platformy Azure zawierającą centrum zdarzeń.
   3. Wybierz lub utwórz **grupę Zasobów,** która ma centrum zdarzeń.
   4. Wybierz **lokalizację,** w której ma być utworzony zasób. 
   5. Wybierz pozycję **Przegląd + utwórz**.
   
        ![Recenzja + tworzenie, tworzenie konta magazynu, witryna Microsoft Azure portal](./media/event-hubs-create-storage/review-create.png)

4. Na stronie **Przeglądanie + tworzenie** sprawdź wartości i wybierz pozycję **Utwórz**. 

    ![Przeglądanie ustawień konta magazynu i tworzenie portalu Microsoft Azure](./media/event-hubs-create-storage/create-storage-account.png)
5. Po wyświetleniu komunikatu **Pomyślne wdrożenia** w powiadomieniach wybierz pozycję **Przejdź do zasobu,** aby otworzyć stronę Konto magazynu. Alternatywnie można rozwinąć **szczegóły wdrożenia,** a następnie wybrać nowy zasób z listy zasobów.  

    ![Przejdź do usługi Resource, wdrożenie konta magazynu, witryna Microsoft Azure portal](./media/event-hubs-create-storage/go-to-resource.png) 
6. Wybierz **kontenery**.

    ![Wybieranie usługi kontenera obiektów Blobs, kont magazynu, witryny Microsoft Azure portal](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Wybierz **+ Kontener** u góry, wprowadź **nazwę** kontenera i wybierz przycisk **OK**. 

    ![Tworzenie nowego kontenera obiektów blob, kont magazynu, witryny Microsoft Azure](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Wybierz **polecenie Klucze programu Access** z menu strony Konto **magazynu** i skopiuj wartość **key1**.

    Zapisz następujące wartości w Notatniku lub innej lokalizacji tymczasowej.
    - Nazwa konta magazynu
    - Klucz dostępu dla konta magazynu
    - Nazwa kontenera
