---
title: Usługa StorSimple przejścia w tryb failover odzyskiwania po awarii na urządzenie StorSimple w chmurze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak w trybie Failover urządzenie fizyczne StorSimple 8000 series do urządzenia w chmurze.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 45c521fd044fa258b8052a3f0de48784cf4160e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584442"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Awaryjnie do urządzenia StorSimple w chmurze

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do pracy awaryjnej urządzeniem fizycznym StorSimple 8000 series do urządzenia StorSimple w chmurze, w przypadku awarii. Usługa StorSimple używa funkcji trybu failover urządzenia do migracji danych z urządzenia fizycznego źródła w centrum danych do urządzenia w chmurze działającej na platformie Azure. Wskazówki zawarte w tym samouczku ma zastosowanie do urządzeń fizycznych z serii StorSimple 8000 i urządzeń w chmurze z wersjami oprogramowania Update 3 i nowszych.

Aby dowiedzieć się więcej na temat trybu failover urządzenia i sposobie ich użycia do odzyskania po awarii, przejdź do [trybu Failover i odzyskiwanie po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Do trybu failover urządzenia fizycznego StorSimple na inne urządzenie fizyczne, przejdź do [Failover urządzenia fizycznego StorSimple](storsimple-8000-device-failover-physical-device.md). Do trybu failover urządzenia do samego siebie, przejdź do [awaryjnej w tym samym urządzeniem fizycznym StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, przejrzeniu uwagi do trybu failover urządzenia. Aby uzyskać więcej informacji, przejdź do [typowe kwestie dotyczące pracy w trybie Failover urządzenia](storsimple-8000-device-failover-disaster-recovery.md).

- Musisz mieć urządzenie StorSimple w chmurze utworzone i skonfigurowane przed uruchomieniem tej procedury. W przypadku uruchomienia aktualizacji oprogramowania w wersji 3 lub nowszej, należy rozważyć użycie urządzenia urządzenie 8020 w chmurze do odzyskiwania po awarii. 8020 model ma 64 TB i korzysta z usługi Premium Storage. Aby uzyskać więcej informacji, przejdź do [wdrażanie i zarządzanie nimi urządzenie StorSimple w chmurze](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Kroki, aby przełączyć tryb failover do urządzenia w chmurze

Wykonaj poniższe kroki, aby przywrócić urządzenia docelowego urządzenia StorSimple w chmurze.

1.  Upewnij się, że kontener woluminów, które w trybie Failover ma skojarzone migawki w chmurze. Aby uzyskać więcej informacji, przejdź do [usługi Menedżer urządzeń StorSimple użycia do tworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).
2. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W **urządzeń** bloku, przejdź do listy urządzeń połączonych z Twoją usługą.
    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Wybierz i kliknij swoje urządzenie źródłowe. Urządzenie źródłowe zawiera kontenery woluminów, które mają być w trybie Failover. Przejdź do **Ustawienia > kontenery woluminów**.

    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Wybierz kontener woluminów, które chcieliby do pracy awaryjnej na innym urządzeniu. Kliknij kontener woluminów, aby wyświetlić listę woluminów należących do tego kontenera. Wybierz wolumin, kliknij prawym przyciskiem myszy i kliknij **Przełącz do trybu Offline** do przełączenia woluminu w tryb offline.

    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Powtórz ten proces, wszystkie woluminy w kontenerze woluminów.

     ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Powtórz poprzedni krok dla wszystkich kontenerów woluminów, które Twoim zdaniem do pracy awaryjnej na innym urządzeniu.

7. Wróć do **urządzeń** bloku. Na pasku poleceń kliknij **w trybie Failover**.

    ![Kliknij opcję niepowodzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. W **w trybie Failover** blok, wykonaj następujące czynności:
   
    1. Kliknij przycisk **źródła**. Wybierz kontenery woluminów do trybu failover. **Wyświetlane są tylko kontenery woluminów z migawkami w chmurze skojarzone i woluminy w trybie offline.**
        ![Wybierz źródło](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Kliknij przycisk **docelowej**. Wybierz urządzenie w chmurze docelowej z listy rozwijanej dostępnych urządzeń. **Tylko urządzenia, które ma wystarczającej wydajności, aby pomieścić kontenery woluminów źródłowych są wyświetlane na liście.**

        ![Wybieranie obiektu docelowego](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Przejrzyj ustawienia trybu failover w ramach **Podsumowanie** i zaznacz pole wyboru wskazujący, że woluminy w wybranych kontenerach woluminów są dostępne w trybie offline. 

        ![Przejrzyj ustawienia trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Utworzono zadanie trybu failover. Aby monitorować zadanie trybu failover, kliknij powiadomienie o zadaniu.

    ![Monitorowanie zadania trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Po zakończeniu pracy w trybie failover, wróć do **urządzeń** bloku.

    1. Wybierz urządzenie, który został użyty jako element docelowy w tryb failover.

       ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Kliknij przycisk **kontenery woluminów**. Powinny zostać wyświetlone wszystkie kontenery woluminów, wraz z woluminami ze starym urządzeniem.

       Jeśli kontener woluminów, która przeszła w tryb failover ma lokalnie przypięte woluminy, jako woluminy warstwowe woluminy są trybie Failover. Woluminy przypięte lokalnie nie są obsługiwane na urządzenie StorSimple w chmurze.

       ![Kontenery woluminów docelowego widoku](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Kolejne kroki

* Po wykonaniu przejścia w tryb failover, konieczne może być [dezaktywacji lub usunięcia urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Aby uzyskać informacje o sposobie używania usługi Menedżer urządzeń StorSimple, przejdź do [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

