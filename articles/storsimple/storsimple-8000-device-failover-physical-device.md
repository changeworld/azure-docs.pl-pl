---
title: Tryb failover, odzyskiwanie po awarii na innym urządzeniu StorSimple 8000
description: Dowiedz się, jak przewinąć urządzenie fizyczne storSimple serii 8000 na inne urządzenie fizyczne.
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
ms.openlocfilehash: 9e4e890ab5491e46ffe5ea0e1c168d168f9cc729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468613"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Praca awaryjna na urządzeniu fizycznym storsimple serii 8000

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do pracy w trybie fail over storsimple 8000 urządzenia fizycznego do innego urządzenia fizycznego StorSimple, jeśli występuje awaria. StorSimple używa funkcji pracy awaryjnej urządzenia do migracji danych ze źródłowego urządzenia fizycznego w centrum danych do innego urządzenia fizycznego. Wskazówki zawarte w tym samouczku dotyczą urządzeń fizycznych z serii StorSimple 8000 z systemem aktualizacji 3 i nowszych wersji oprogramowania.

Aby dowiedzieć się więcej o pracy awaryjnej urządzenia i sposobie jego użycia w celu odzyskania danych po awarii, przejdź do [trybu failover i odzyskiwania po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Aby przejść w tryb fail over urządzenia fizycznego StorSimple do urządzenia StorSimple Cloud Appliance, przejdź do [pracy w trybie fail over do StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Aby przewinąć urządzenie w tryb fail over fizyczne do siebie, przejdź do [pracy w trybie fail over do tego samego urządzenia fizycznego StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zostały sprawdzone zagadnienia dotyczące pracy awaryjnej urządzenia. Aby uzyskać więcej informacji, przejdź do [typowych zagadnień dotyczących pracy awaryjnej urządzenia](storsimple-8000-device-failover-disaster-recovery.md).

- Urządzenie fizyczne z serii StorSimple 8000 musi zostać wdrożone w centrum danych. Urządzenie musi działać w wersji aktualizacji 3 lub nowszej. Aby uzyskać więcej informacji, przejdź do [witryny Lokalne urządzenie StorSimple](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Kroki, aby przejść awaryjnie do urządzenia fizycznego

Wykonaj następujące czynności, aby przywrócić urządzenie do docelowego urządzenia fizycznego.

1. Sprawdź, czy kontener woluminu, który ma zostać przejęty awaryjnie, ma skojarzone migawki w chmurze. Aby uzyskać więcej informacji, przejdź do [usługi StorSimple Device Manager w celu utworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).
2. Przejdź do Menedżera urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. W bloku **Urządzenia** przejdź do listy urządzeń połączonych z usługą.
    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Wybierz i kliknij urządzenie źródłowe. Urządzenie źródłowe ma kontenery woluminów, które mają zostać przejęte w awaryjnie. Przejdź do **ustawień > kontenerów woluminów**.
4. Wybierz kontener woluminu, który ma być awaryjny na innym urządzeniu. Kliknij kontener woluminów, aby wyświetlić listę woluminów w tym kontenerze. Wybierz wolumin, kliknij prawym przyciskiem myszy i kliknij polecenie **Przejmij do trybu offline,** aby przetraktować wolumin w tryb offline. Powtórz ten proces dla wszystkich woluminów w kontenerze woluminów.
5. Powtórz poprzedni krok dla wszystkich kontenerów woluminów, które chcesz przewinąć awaryjnie na innym urządzeniu.
6. Wróć do bloku **Urządzenia.** Na pasku poleceń kliknij pozycję **Przewiduj powiększanie**.
    ![Kliknij przycisk trybu fail over](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. W **bloku Przepełnienie awaryjnego** wykonaj następujące czynności:
   
   1. Kliknij **pozycję Źródło**. Zostaną wyświetlone kontenery woluminów z woluminami skojarzonymi z migawkami w chmurze. Tylko wyświetlane kontenery kwalifikują się do pracy awaryjnej. Na liście kontenerów woluminów wybierz kontenery woluminów, które mają być awaryjne. **Wyświetlane są tylko kontenery woluminów ze skojarzonymi migawkami w chmurze i woluminami offline.**

       ![Wybierz źródło](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Kliknij **pozycję Cel**. W przypadku kontenerów woluminów wybranych w poprzednim kroku wybierz urządzenie docelowe z listy rozwijanej dostępnych urządzeń. Na liście są wyświetlane tylko urządzenia, które mają wystarczającą pojemność, aby pomieścić kontenery woluminów źródłowych.

        ![Wybieranie obiektu docelowego](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Na koniec przejrzyj wszystkie ustawienia pracy awaryjnej w obszarze **Podsumowanie**. Po przejrzeniu ustawień zaznacz pole wyboru wskazujące, że woluminy w wybranych kontenerach woluminów są w trybie offline. Kliknij przycisk **OK**.

       ![Przeglądanie ustawień trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple tworzy zadanie pracy awaryjnej. Kliknij powiadomienie o zadaniu, aby monitorować zadanie pracy awaryjnej za pośrednictwem **zadania** bloku.

    Jeśli kontener woluminu, który został przejęty awaryjnie, ma woluminy lokalne, w kontenerze są widoczne poszczególne zadania przywracania dla każdego woluminu lokalnego (a nie woluminów warstwowych). Te zadania przywracania może zająć sporo czasu, aby zakończyć. Jest prawdopodobne, że zadanie pracy awaryjnej może zakończyć się wcześniej. Te woluminy będą miały gwarancje lokalne dopiero po zakończeniu zadań przywracania.

    ![Monitorowanie zadania pracy awaryjnej](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Po zakończeniu pracy awaryjnej przejdź do **urządzenia** bloku.
   
   1. Wybierz urządzenie, które było używane jako urządzenie docelowe dla procesu pracy awaryjnej.

       ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Przejdź do bloku **Pojemniki woluminów.** Wszystkie pojemniki woluminów, wraz z woluminami ze starego urządzenia, powinny być wymienione.

       ![Wyświetlanie kontenerów woluminów docelowych](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Następne kroki

* Po wykonaniu pracy awaryjnej może być konieczne [dezaktywację lub usunięcie urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Aby uzyskać informacje dotyczące korzystania z usługi StorSimple Device Manager, zobacz [Korzystanie z usługi StorSimple Device Manager do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

