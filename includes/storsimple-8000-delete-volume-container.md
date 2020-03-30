---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: e683d17422321b780a1c01b3011292f2e2c631cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183278"
---
Aby usunąć kontener woluminu, należy
 - usuwać woluminy w kontenerze woluminów. Jeśli kontener woluminów ma skojarzone woluminy, najpierw przełącz te woluminy w tryb offline. Wykonaj kroki opisane w [obszarze Przejmuj wolumin w tryb offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Po zakończeniu pracy woluminów można je usunąć. 
 - usuwanie powiązanych zasad tworzenia kopii zapasowych i migawek w chmurze. Sprawdź, czy kontener woluminów ma skojarzone zasady tworzenia kopii zapasowych i migawki w chmurze. Jeśli tak, [usuń zasady tworzenia kopii zapasowych](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Spowoduje to również usunięcie migawek w chmurze. 
 
Jeśli kontener woluminu nie ma skojarzonych woluminów, zasad tworzenia kopii zapasowych i migawek w chmurze, można go usunąć. Wykonaj poniższą procedurę, aby usunąć kontener woluminu.

#### <a name="to-delete-a-volume-container"></a>Aby usunąć kontener woluminu
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. Zaznacz i kliknij urządzenie, a następnie przejdź do **pozycji Ustawienia > Zarządzaj kontenerami woluminów >**.

    ![Ostrze pojemników objętościowych](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Z tabelarycznej listy kontenerów woluminów wybierz kontener woluminu, który chcesz usunąć, kliknij prawym przyciskiem myszy **...** a następnie wybierz polecenie **Usuń**.

    ![Usuwanie kontenera woluminu](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Jeśli kontener woluminu nie ma skojarzonych woluminów, zasad tworzenia kopii zapasowych i migawek w chmurze, można go usunąć. Po wyświetleniu monitu o potwierdzenie przejrzyj i zaznacz pole wyboru z informacją o wpływie usunięcia kontenera woluminu. Kliknij **przycisk Usuń,** aby następnie usunąć kontener woluminu.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Lista kontenerów woluminów jest aktualizowana w celu odzwierciedlenia usuniętego kontenera woluminu.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


