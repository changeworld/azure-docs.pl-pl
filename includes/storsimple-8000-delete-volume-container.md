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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60724736"
---
Aby usunąć kontener woluminów, musisz mieć
 - Usuń woluminy w kontenerze woluminów. Jeśli kontener woluminów ma skojarzone woluminy, Przełącz te woluminy trybu offline najpierw. Postępuj zgodnie z instrukcjami w [przełączyć wolumin w tryb offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Po woluminy są w trybie offline, możesz je usunąć. 
 - Usuń skojarzone zasady tworzenia kopii zapasowej, a migawki w chmurze. Sprawdź, jeśli kontener woluminów ma skojarzone zasady tworzenia kopii zapasowych i migawki w chmurze. Jeśli tak, następnie [usuwanie zasady tworzenia kopii zapasowych](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Spowoduje to również usunięcie migawki w chmurze. 
 
Gdy kontener woluminów ma nie skojarzone woluminy, zasadami tworzenia kopii zapasowych i migawki w chmurze, można ją usunąć. Wykonaj następującą procedurę, aby usunąć kontener woluminów.

#### <a name="to-delete-a-volume-container"></a>Można usunąć kontenera woluminów
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. Wybierz i kliknij urządzenie, a następnie przejdź do **Ustawienia > Zarządzaj > kontenery woluminów**.

    ![Blok kontenery woluminów](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Z tabelarycznej listy kontenery woluminów, wybierz kontener woluminów, które chcesz usunąć, kliknij prawym przyciskiem myszy **...**  , a następnie wybierz **Usuń**.

    ![Usuwanie kontenera woluminów](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Jeśli kontener woluminów ma nie skojarzone woluminy, zasadami tworzenia kopii zapasowych i migawki w chmurze, można je usunąć. Po wyświetleniu monitu o potwierdzenie Przejrzyj i zaznacz pole wyboru, podając konsekwencje usunięcia kontenera woluminów. Kliknij przycisk **Usuń** można następnie usunąć kontenera woluminów.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Lista kontenerów woluminów jest aktualizowany w celu odzwierciedlenia kontenera woluminów usunięte.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


