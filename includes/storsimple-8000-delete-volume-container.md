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
ms.openlocfilehash: e7f3f80c886f90a8bc3ae8c38e7d101c506439a6
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35250226"
---
Aby usunąć kontener woluminów, musisz
 - Usuń woluminy w kontenerze woluminów. Jeśli kontener woluminów ma skojarzone woluminy, wykonać tych woluminów w trybie offline najpierw. Postępuj zgodnie z instrukcjami [Przełącz do trybu offline wolumin](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Po woluminy są w trybie offline, można je usunąć. 
 - Usuń skojarzone zasady tworzenia kopii zapasowej i migawki w chmurze. Sprawdź, czy kontener woluminów ma skojarzone zasady tworzenia kopii zapasowej i migawki w chmurze. Jeśli tak, następnie [usunąć zasady tworzenia kopii zapasowej](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Spowoduje to również usunięcie migawki w chmurze. 
 
Gdy kontener woluminów nie zawiera skojarzone woluminy, zasad tworzenia kopii zapasowych i migawki w chmurze, można ją usunąć. Wykonaj następującą procedurę, aby usunąć kontener woluminów.

#### <a name="to-delete-a-volume-container"></a>Aby usunąć kontener woluminów
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. Wybierz i kliknij urządzenie, a następnie przejdź do **Ustawienia > Zarządzaj > kontenery woluminów**.

    ![Wolumin kontenerów bloku](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Z listy tabelarycznej kontenery woluminów, wybierz kontener woluminów, które chcesz usunąć, kliknij prawym przyciskiem myszy **...**  , a następnie wybierz **usunąć**.

    ![Usunięcie kontenera woluminów](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Jeśli kontener woluminów nie ma skojarzone woluminy, zasad tworzenia kopii zapasowych i migawki w chmurze, mogą zostać usunięte. Po wyświetleniu monitu o potwierdzenie, przejrzyj i zaznacz pole wyboru, podając wpływ usunięcie kontenera woluminów. Kliknij przycisk **usunąć** aby następnie usunąć kontener woluminów.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Lista kontenery woluminów jest aktualizowana do uwzględnienia kontenera usuniętych woluminów.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


