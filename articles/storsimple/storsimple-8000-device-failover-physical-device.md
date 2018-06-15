---
title: Tryb failover StorSimple, odzyskiwania po awarii do urządzenia fizycznego serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Informacje o sposobie pracy awaryjnej urządzenia fizycznego StorSimple 8000 serii do innego urządzenia fizycznego.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: f3ac9545a341fc24ca12c9f2547805d6956cd98a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874864"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Przełączyć urządzenia fizycznego StorSimple 8000 serii

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do pracy awaryjnej z serii StorSimple 8000 fizyczne urządzenia do innego urządzenia fizycznego StorSimple w przypadku awarii. StorSimple korzysta z funkcji trybu failover urządzenia do migracji danych z urządzenia fizycznego źródła w centrum danych do innego urządzenia fizycznego. Wskazówki zawarte w tym samouczku dotyczą z serii StorSimple 8000 fizyczne urządzenia z wersjami oprogramowania aktualizacji 3 lub nowszym.

Aby dowiedzieć się więcej o pracy awaryjnej urządzenia i sposobu ich wykorzystywania odzyskiwania po awarii, przejdź do [trybu Failover i odzyskiwania po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Aby przełączyć urządzenia fizycznego StorSimple na urządzeniu chmury StorSimple, przejdź do [funkcjonować w trybie awaryjnym urządzenia chmury StorSimple](storsimple-8000-device-failover-cloud-appliance.md). Aby przełączyć urządzenie fizyczne do samej siebie, przejdź do [awaryjnej w tym samym urządzeniu fizycznym StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, należy przejrzeć zagadnienia dotyczące urządzenia trybu failover. Aby uzyskać więcej informacji, przejdź do [typowe kwestie dotyczące pracy w trybie failover urządzenia](storsimple-8000-device-failover-disaster-recovery.md).

- Muszą mieć urządzenia fizycznego StorSimple 8000 serii wdrożone w centrum danych. Na urządzeniu musi działać aktualizacji 3 lub nowszej wersji oprogramowania. Aby uzyskać więcej informacji, przejdź do [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Kroki, aby przełączyć się urządzenia fizycznego

Wykonaj poniższe kroki, aby przywrócić urządzenia docelowego urządzenia fizycznego.

1. Upewnij się, że kontener woluminów, które chcesz przełączyć skojarzone migawki w chmurze. Aby uzyskać więcej informacji, przejdź do [usługi StorSimple za pomocą Menedżera urządzeń do tworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).
2. Przejdź do Menedżera urządzeń StorSimple, a następnie kliknij przycisk **urządzeń**. W **urządzeń** bloku, przejdź do listy urządzeń połączonych z usługą.
    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Wybierz, a następnie kliknij przycisk urządzenia źródłowego. Urządzenia źródłowego ma kontenery woluminów, które mają być w tryb failover. Przejdź do **Ustawienia > kontenery woluminów**.
4. Wybierz kontener woluminów, które chcesz przełączyć się na innym urządzeniu. Kliknij przycisk kontenera woluminów, aby wyświetlić listę woluminów, w tym kontenerze. Wybierz wolumin, kliknij prawym przyciskiem myszy i kliknij przycisk **Przełącz do trybu Offline** woluminu przełączenia do trybu offline. Powtórz ten proces, wszystkie woluminy w kontenerze woluminów.
5. Powtórz poprzedni krok dla wszystkich kontenerów woluminu, który chcesz przełączyć się na innym urządzeniu.
6. Wróć do **urządzeń** bloku. Na pasku poleceń kliknij **awaryjnie**.
    ![Kliknij opcję niepowodzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. W **awaryjnie** blok, wykonaj następujące czynności:
   
   1. Kliknij przycisk **źródła**. Kontenery woluminów z woluminami skojarzone z migawki w chmurze są wyświetlane. Kontenery wyświetlane kwalifikują się do trybu failover. Na liście kontenery woluminów wybierz kontenery woluminów, które chcesz przełączyć. **Wyświetlane są tylko kontenery woluminów z migawki w chmurze skojarzone i woluminy w trybie offline.**

       ![Wybierz źródło](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Kliknij przycisk **docelowej**. Dla kontenery woluminów wybrane w poprzednim kroku wybierz urządzenie docelowe z listy rozwijanej dostępnych urządzeń. Tylko na urządzeniach, które ma wystarczającej wydajności, aby pomieścić kontenery woluminów źródła są wyświetlane na liście.

        ![Wybierz element docelowy](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Na koniec przejrzeć wszystkie ustawienia trybu failover w **Podsumowanie**. Po przejrzeniu ustawienia, zaznacz pole wyboru wskazujące, że woluminy w kontenerach wybranego woluminu w trybie offline. Kliknij przycisk **OK**.

       ![Przejrzyj ustawienia trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple tworzy zadanie trybu failover. Kliknij powiadomienie zadania, aby monitorować zadanie trybu failover za pomocą **zadania** bloku.

    Jeśli kontener woluminów, które zakończyły się niepowodzeniem na woluminach lokalnych, zostanie wyświetlony indywidualnych zadań przywracania dla każdego woluminu lokalnego (a nie dla woluminów warstwowych) w kontenerze. Te przywracania zadania może potrwać jakiś czas, aby zakończyć. Istnieje prawdopodobieństwo, że zadanie trybu failover może zakończyć się wcześniej. Te woluminy mają lokalnych gwarancji, dopiero po zakończeniu zadania przywracania.

    ![Monitor, zadanie trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Po zakończeniu pracy awaryjnej, przejdź do **urządzeń** bloku.
   
   1. Wybierz urządzenie, która została użyta jako urządzenie docelowe dla procesu pracy awaryjnej.

       ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Przejdź do **kontenery woluminów** bloku. Wszystkie kontenery woluminów, wraz z woluminami ze starym urządzeniem powinien być wyświetlany.

       ![Kontenery woluminów docelowego widoku](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Następne kroki

* Po wykonaniu trybu failover, konieczne może być [dezaktywację lub usunięcie urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Aby uzyskać informacje o sposobie używania usługi Menedżer StorSimple urządzeń, przejdź do [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

