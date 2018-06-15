---
title: Tryb failover StorSimple, odzyskiwania po awarii do urządzenia chmury StorSimple | Dokumentacja firmy Microsoft
description: Dowiedz się, jak urządzenia fizycznego StorSimple 8000 serii na urządzeniu chmury w tryb failover.
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
ms.openlocfilehash: ec8bebf2854e84a37e84b45564e80fc20b63d8d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874899"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Awaryjnie na urządzeniu StorSimple chmury

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do pracy awaryjnej urządzenia fizycznego serii StorSimple 8000 do urządzenia chmury StorSimple w przypadku awarii. StorSimple korzysta z funkcji trybu failover urządzenia do migracji danych z urządzenia fizycznego źródła w centrum danych do urządzenia chmury, działające na platformie Azure. Wskazówki zawarte w tym samouczku dotyczą fizycznych urządzeń z serii StorSimple 8000 oraz urządzenia chmury wersjami oprogramowania aktualizacji 3 lub nowszym.

Aby dowiedzieć się więcej o pracy awaryjnej urządzenia i sposobu ich wykorzystywania odzyskiwania po awarii, przejdź do [trybu Failover i odzyskiwania po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Aby tryb failover na inne urządzenie fizyczne urządzenia fizycznego StorSimple, przejdź do [funkcjonować w trybie awaryjnym urządzenia fizycznego StorSimple](storsimple-8000-device-failover-physical-device.md). Aby przełączyć urządzenia do samej siebie, przejdź do [awaryjnej w tym samym urządzeniu fizycznym StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, należy przejrzeć zagadnienia dotyczące urządzenia trybu failover. Aby uzyskać więcej informacji, przejdź do [typowe kwestie dotyczące pracy w trybie failover urządzenia](storsimple-8000-device-failover-disaster-recovery.md).

- Muszą mieć urządzenia chmury StorSimple utworzeniu i skonfigurowaniu przed uruchomieniem tej procedury. Jeśli uruchomienia aktualizacji oprogramowania w wersji 3 lub nowszym, należy wziąć pod uwagę przy użyciu urządzenia 8020 chmury DR. 8020 model ma 64 TB i używa magazynu w warstwie Premium. Aby uzyskać więcej informacji, przejdź do [wdrażanie i zarządzanie nimi urządzenia chmury StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Kroki, aby przełączyć się urządzenia chmury

Wykonaj poniższe kroki, aby przywrócić urządzenia docelowego urządzenia chmury StorSimple.

1.  Upewnij się, że kontener woluminów, które chcesz przełączyć skojarzone migawki w chmurze. Aby uzyskać więcej informacji, przejdź do [usługi StorSimple za pomocą Menedżera urządzeń do tworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).
2. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W **urządzeń** bloku, przejdź do listy urządzeń połączonych z usługą.
    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Wybierz, a następnie kliknij przycisk urządzenia źródłowego. Urządzenia źródłowego ma kontenery woluminów, które mają być w tryb failover. Przejdź do **Ustawienia > kontenery woluminów**.

    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Wybierz kontener woluminów, które chcesz przełączyć się na innym urządzeniu. Kliknij przycisk kontenera woluminów, aby wyświetlić listę woluminów, w tym kontenerze. Wybierz wolumin, kliknij prawym przyciskiem myszy i kliknij przycisk **Przełącz do trybu Offline** woluminu przełączenia do trybu offline.

    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Powtórz ten proces, wszystkie woluminy w kontenerze woluminów.

     ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Powtórz poprzedni krok dla wszystkich kontenerów woluminu, który chcesz przełączyć się na innym urządzeniu.

7. Wróć do **urządzeń** bloku. Na pasku poleceń kliknij **awaryjnie**.

    ![Kliknij opcję niepowodzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. W **awaryjnie** blok, wykonaj następujące czynności:
   
    1. Kliknij przycisk **źródła**. Wybierz kontenery woluminów do pracy awaryjnej. **Wyświetlane są tylko kontenery woluminów z migawki w chmurze skojarzone i woluminy w trybie offline.**
        ![Wybierz źródło](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Kliknij przycisk **docelowej**. Wybierz urządzenia chmury docelowy z listy rozwijanej dostępnych urządzeń. **Tylko na urządzeniach, które ma wystarczającej wydajności, aby pomieścić kontenery woluminów źródła są wyświetlane na liście.**

        ![Wybierz element docelowy](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Przejrzyj ustawienia trybu failover w obszarze **Podsumowanie** i zaznacz pole wyboru wskazujące, że woluminy w kontenerach wybranego woluminu w trybie offline. 

        ![Przejrzyj ustawienia trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Tworzone jest zadanie trybu failover. Aby monitorować zadanie trybu failover, kliknij powiadomienie o zadaniu.

    ![Monitor, zadanie trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Po zakończeniu pracy awaryjnej, wróć do **urządzeń** bloku.

    1. Wybierz urządzenie, który został użyty jako element docelowy do pracy awaryjnej.

       ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Kliknij przycisk **kontenery woluminów**. Wszystkie kontenery woluminów, wraz z woluminami ze starym urządzeniem powinien być wyświetlany.

       Kontener woluminów, które zakończyły się niepowodzeniem przez ma przypięty lokalnie woluminów, woluminy są nie powiodła się za pośrednictwem jako woluminy warstwowe. Woluminów przypiętych lokalnie nie są obsługiwane na urządzeniu z StorSimple w chmurze.

       ![Kontenery woluminów docelowego widoku](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Następne kroki

* Po wykonaniu trybu failover, konieczne może być [dezaktywację lub usunięcie urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Aby uzyskać informacje o sposobie używania usługi Menedżer StorSimple urządzeń, przejdź do [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

