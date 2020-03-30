---
title: Tryb failover i odzyskiwanie po awarii w urządzeniu StorSimple Cloud Appliance
description: Dowiedz się, jak przewinąć urządzenie fizyczne storSimple serii 8000 w urządzeniu w chmurze.
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
ms.openlocfilehash: 347b899608d4322a7873b9f80f38ca1c767194d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468749"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Przełączenie awaryjne do urządzenia StorSimple Cloud Appliance

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do pracy w trybie fail over StorSimple 8000 urządzenia fizycznego do StorSimple Cloud Appliance w przypadku awarii. StorSimple używa funkcji pracy awaryjnej urządzenia do migracji danych ze źródłowego urządzenia fizycznego w centrum danych do urządzenia w chmurze uruchomionego na platformie Azure. Wskazówki zawarte w tym samouczku dotyczą urządzeń fizycznych serii StorSimple serii 8000 i urządzeń w chmurze z systemem aktualizacji 3 i nowszych.

Aby dowiedzieć się więcej o pracy awaryjnej urządzenia i sposobie jego użycia w celu odzyskania danych po awarii, przejdź do [trybu failover i odzyskiwania po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Aby przewinąć urządzenie fizyczne StorSimple w tryb fail over na inne urządzenie fizyczne, przejdź do [funkcji Praca w trybie fail over do urządzenia fizycznego StorSimple](storsimple-8000-device-failover-physical-device.md). Aby przewinąć urządzenie w tryb fail over do siebie, przejdź do [pracy w trybie fail over do tego samego urządzenia fizycznego StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zostały sprawdzone zagadnienia dotyczące pracy awaryjnej urządzenia. Aby uzyskać więcej informacji, przejdź do [typowych zagadnień dotyczących pracy awaryjnej urządzenia](storsimple-8000-device-failover-disaster-recovery.md).

- Przed uruchomieniem tej procedury musi zostać utworzone i skonfigurowane urządzenie StorSimple Cloud Appliance. Jeśli jest uruchomiona wersja oprogramowania aktualizacji 3 lub nowsza, należy rozważyć użycie urządzenia w chmurze 8020 dla odzyskiwania po awarii. Model 8020 ma 64 TB i korzysta z magazynu premium. Aby uzyskać więcej informacji, przejdź do [wdrażania urządzenia StorSimple Cloud Appliance i zarządzania nim.](storsimple-8000-cloud-appliance-u2.md)

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Kroki, aby przejść awaryjnie do urządzenia w chmurze

Wykonaj następujące kroki, aby przywrócić urządzenie do docelowego urządzenia StorSimple Cloud Appliance.

1.  Sprawdź, czy kontener woluminu, który ma zostać przejęty awaryjnie, ma skojarzone migawki w chmurze. Aby uzyskać więcej informacji, przejdź do [usługi StorSimple Device Manager w celu utworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).
2. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W bloku **Urządzenia** przejdź do listy urządzeń połączonych z usługą.
    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Wybierz i kliknij urządzenie źródłowe. Urządzenie źródłowe ma kontenery woluminów, które mają zostać przejęte w awaryjnie. Przejdź do **ustawień > kontenerów woluminów**.

    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Wybierz kontener woluminu, który ma być awaryjny na innym urządzeniu. Kliknij kontener woluminów, aby wyświetlić listę woluminów w tym kontenerze. Wybierz wolumin, kliknij prawym przyciskiem myszy i kliknij polecenie **Przejmij do trybu offline,** aby przetraktować wolumin w tryb offline.

    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Powtórz ten proces dla wszystkich woluminów w kontenerze woluminów.

     ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Powtórz poprzedni krok dla wszystkich kontenerów woluminów, które chcesz przewinąć awaryjnie na innym urządzeniu.

7. Wróć do bloku **Urządzenia.** Na pasku poleceń kliknij pozycję **Przewiduj powiększanie**.

    ![Kliknij przycisk trybu fail over](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. W **bloku Przepełnienie awaryjnego** wykonaj następujące czynności:
   
    1. Kliknij **pozycję Źródło**. Wybierz kontenery woluminów do przeładowania awaryjnego. **Wyświetlane są tylko kontenery woluminów ze skojarzonymi migawkami w chmurze i woluminami offline.**
        ![Wybierz źródło](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Kliknij **pozycję Cel**. Wybierz docelowe urządzenie w chmurze z listy rozwijanej dostępnych urządzeń. **Na liście są wyświetlane tylko urządzenia, które mają wystarczającą pojemność, aby pomieścić kontenery woluminów źródłowych.**

        ![Wybieranie obiektu docelowego](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Przejrzyj ustawienia pracy awaryjnej w obszarze **Podsumowanie** i zaznacz pole wyboru wskazujące, że woluminy w wybranych kontenerach woluminów są w trybie offline. 

        ![Przeglądanie ustawień trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Zostanie utworzone zadanie pracy awaryjnej. Aby monitorować zadanie pracy awaryjnej, kliknij powiadomienie o zadaniu.

    ![Monitorowanie zadania pracy awaryjnej](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Po zakończeniu pracy awaryjnej wróć do **bloku Urządzenia.**

    1. Wybierz urządzenie, które zostało użyte jako miejsce docelowe dla pracy awaryjnej.

       ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Kliknij pozycję **Kontenery woluminów**. Wszystkie pojemniki woluminów, wraz z woluminami ze starego urządzenia, powinny być wymienione.

       Jeśli kontener woluminu, który został przejęty awaryjnie, ma woluminy przypięte lokalnie, woluminy te są przekonywały w trybie fail, jako woluminy warstwowe. Woluminy przypięte lokalnie nie są obsługiwane w urządzeniu StorSimple Cloud Appliance.

       ![Wyświetlanie kontenerów woluminów docelowych](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Następne kroki

* Po wykonaniu pracy awaryjnej może być konieczne [dezaktywację lub usunięcie urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Aby uzyskać informacje dotyczące korzystania z usługi StorSimple Device Manager, zobacz [Korzystanie z usługi StorSimple Device Manager do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

