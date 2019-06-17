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
ms.openlocfilehash: b84b0a8e09bf739ce62dee167ff751b491765c66
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66154563"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Tworzenie konta magazynu dla hosta procesora zdarzeń
Host procesora zdarzeń to inteligentny agent, który upraszcza odbieranie zdarzeń z usługi Event Hubs przez zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami. W przypadku obsługi punktów kontrolnych host procesora zdarzeń wymaga konta magazynu. Poniższy przykład pokazuje, jak utworzyć konto magazynu oraz uzyskać jego klucze na potrzeby dostępu:

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** w lewym górnym rogu ekranu.

2. Wybierz pozycję **Storage**, a następnie wybierz pozycję **Konto usługi Storage — Blob, File, Table, Queue**.
   
    ![Wybieranie konta magazynu](./media/event-hubs-create-storage/create-storage1.png)

3. Na stronie **Utwórz konto magazynu** wykonaj następujące kroki: 

   1. Wprowadź nazwę konta magazynu. 
   2. Wybierz subskrypcję platformy Azure zawierającą centrum zdarzeń.
   3. Wybierz grupę zasobów zawierającą centrum zdarzeń.
   4. Wybierz lokalizację, w której chcesz utworzyć zasób. 
   5. Następnie kliknij pozycję **Przeglądanie + tworzenie**.
   
      ![Utwórz konto magazynu — strona](./media/event-hubs-create-storage/create-storage2.png)

4. Na stronie **Przeglądanie + tworzenie** sprawdź wartości i wybierz pozycję **Utwórz**. 

    ![Przeglądanie ustawień konta magazynu i tworzenie](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Po wyświetleniu **wdrożenia zakończone pomyślnie** komunikatu wybierz **przejdź do zasobu** w górnej części strony. Na stronie konta magazynu można również uruchomić, wybierając konto magazynu z listy zasobów.  

    ![Wybieranie konta magazynu z wdrożenia](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. W oknie **Podstawowe elementy** wybierz pozycję **Obiekty blob**. 

    ![Wybierz usługę obiektów blob](./media/event-hubs-create-storage/select-blobs-service.png)
1. Wybierz **+ kontener** u góry strony, wprowadź **nazwa** dla kontenera, a następnie wybierz **OK**. 

    ![Tworzenie kontenera obiektów blob](./media/event-hubs-create-storage/create-blob-container.png)
1. Wybierz **klucze dostępu** w menu po lewej stronie, a następnie skopiuj wartość **klucz1**. 

    Zapisz następujące wartości do Notatnika lub innej tymczasowej lokalizacji.
    - Nazwa konta magazynu
    - Klucz dostępu dla konta magazynu
    - Nazwa kontenera
