---
title: Usługa StorSimple przejścia w tryb failover odzyskiwania po awarii na urządzeniu fizycznym StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Dowiedz się, jak urządzenie fizyczne StorSimple 8000 series na inne urządzenie fizyczne w trybie Failover.
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
ms.openlocfilehash: 5fcf95a1a3033a5150945dbd841f12d50ebb023b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60577245"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Pracy awaryjnej na urządzeniu fizycznym StorSimple 8000 series

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do pracy awaryjnej urządzeniem fizycznym StorSimple 8000 series do innego urządzenia fizycznego StorSimple w przypadku awarii. Usługa StorSimple używa funkcji trybu failover urządzenia do migracji danych z urządzenia fizycznego źródła w centrum danych do innego urządzenia fizycznego. Wskazówki zawarte w tym samouczku ma zastosowanie do serii StorSimple 8000 fizycznych urządzeń z systemem oprogramowania wersji Update 3 i nowszych.

Aby dowiedzieć się więcej na temat trybu failover urządzenia i sposobie ich użycia do odzyskania po awarii, przejdź do [trybu Failover i odzyskiwanie po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Do trybu failover urządzenia fizycznego StorSimple do urządzenia StorSimple w chmurze, przejdź do [Failover urządzenia StorSimple w chmurze](storsimple-8000-device-failover-cloud-appliance.md). Do trybu failover urządzenia fizycznego do samego siebie, przejdź do [awaryjnej w tym samym urządzeniem fizycznym StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, przejrzeniu uwagi do trybu failover urządzenia. Aby uzyskać więcej informacji, przejdź do [typowe kwestie dotyczące pracy w trybie Failover urządzenia](storsimple-8000-device-failover-disaster-recovery.md).

- Konieczne jest posiadanie StorSimple 8000 series urządzenie fizyczne, wdrożonych w centrum danych. Na urządzeniu musi działać z aktualizacją Update 3 lub nowszej wersji oprogramowania. Aby uzyskać więcej informacji, przejdź do [wdrażanie urządzenia StorSimple w środowisku lokalnym](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Kroki do trybu failover na urządzeniu fizycznym

Wykonaj poniższe kroki, aby przywrócić urządzenie fizyczne urządzenie docelowe.

1. Upewnij się, że kontener woluminów, które w trybie Failover ma skojarzone migawki w chmurze. Aby uzyskać więcej informacji, przejdź do [usługi Menedżer urządzeń StorSimple użycia do tworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).
2. Przejdź do Menedżera urządzeń StorSimple, a następnie kliknij przycisk **urządzeń**. W **urządzeń** bloku, przejdź do listy urządzeń połączonych z Twoją usługą.
    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Wybierz i kliknij swoje urządzenie źródłowe. Urządzenie źródłowe zawiera kontenery woluminów, które mają być w trybie Failover. Przejdź do **Ustawienia > kontenery woluminów**.
4. Wybierz kontener woluminów, które chcieliby do pracy awaryjnej na innym urządzeniu. Kliknij kontener woluminów, aby wyświetlić listę woluminów należących do tego kontenera. Wybierz wolumin, kliknij prawym przyciskiem myszy i kliknij **Przełącz do trybu Offline** do przełączenia woluminu w tryb offline. Powtórz ten proces, wszystkie woluminy w kontenerze woluminów.
5. Powtórz poprzedni krok dla wszystkich kontenerów woluminów, które Twoim zdaniem do pracy awaryjnej na innym urządzeniu.
6. Wróć do **urządzeń** bloku. Na pasku poleceń kliknij **w trybie Failover**.
    ![Kliknij opcję niepowodzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. W **w trybie Failover** blok, wykonaj następujące czynności:
   
   1. Kliknij przycisk **źródła**. Kontenery woluminów z woluminów skojarzonych z migawkami w chmurze są wyświetlane. Kontenery, wyświetlane kwalifikują się do trybu failover. Na liście kontenerów woluminów wybrać kontenery woluminów, które Twoim zdaniem do trybu failover. **Wyświetlane są tylko kontenery woluminów z migawkami w chmurze skojarzone i woluminy w trybie offline.**

       ![Wybierz źródło](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Kliknij przycisk **docelowej**. Dla kontenerów woluminów wybrane w poprzednim kroku wybierz urządzenie docelowe z listy rozwijanej dostępnych urządzeń. Tylko urządzenia, które ma wystarczającej wydajności, aby pomieścić kontenery woluminów źródłowych są wyświetlane na liście.

        ![Wybieranie obiektu docelowego](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Na koniec sprawdź wszystkie ustawienia trybu failover w ramach **Podsumowanie**. Po przejrzeniu ustawienia, zaznacz pole wyboru wskazujący, że woluminy w wybranych kontenerach woluminów są dostępne w trybie offline. Kliknij przycisk **OK**.

       ![Przejrzyj ustawienia trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. Usługa StorSimple tworzy zadanie trybu failover. Kliknij powiadomienie zadania, aby monitorować zadanie trybu failover za pomocą **zadań** bloku.

    Jeśli kontener woluminów, która przeszła w tryb failover ma woluminy lokalne, zobaczysz zadania indywidulane przywracanie dla każdego woluminu lokalnego (a nie dla woluminów warstwowych) w kontenerze. Przywracanie, te zadania może potrwać jakiś czas, aby zakończyć. Istnieje prawdopodobieństwo, że zadanie trybu failover może zakończyć się wcześniej. Te woluminy mają lokalnych gwarancji, tylko po wykonaniu zadania przywracania.

    ![Monitorowanie zadania trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Po zakończeniu pracy w trybie failover, przejdź do **urządzeń** bloku.
   
   1. Wybierz urządzenie, używanym jako urządzenie docelowe dla procesu pracy awaryjnej.

       ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Przejdź do **kontenery woluminów** bloku. Powinny zostać wyświetlone wszystkie kontenery woluminów, wraz z woluminami ze starym urządzeniem.

       ![Kontenery woluminów docelowego widoku](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Kolejne kroki

* Po wykonaniu przejścia w tryb failover, konieczne może być [dezaktywacji lub usunięcia urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Aby uzyskać informacje o sposobie używania usługi Menedżer urządzeń StorSimple, przejdź do [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

