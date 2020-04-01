---
title: Samouczek - Praca awaryjna StorSimple urządzenia fizycznego do tego samego urządzenia
description: Dowiedz się, jak przewinąć urządzenie StorSimple w trybie fail over na tym samym urządzeniu.
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: dde01f9b91ff5a04ddb3fcc8d5f0c535278b0539
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398074"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Praca awaryjna nad urządzeniem fizycznym StorSimple na tym samym urządzeniu

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do awaryjnego nad urządzeniem fizycznym z serii StorSimple 8000 dla siebie w przypadku awarii. StorSimple używa funkcji pracy awaryjnej urządzenia do migracji danych ze źródłowego urządzenia fizycznego w centrum danych do innego urządzenia fizycznego. Wskazówki zawarte w tym samouczku dotyczą urządzeń fizycznych z serii StorSimple 8000 z systemem aktualizacji 3 i nowszych wersji oprogramowania.

Aby dowiedzieć się więcej o pracy awaryjnej urządzenia i sposobie jego użycia w celu odzyskania danych po awarii, przejdź do [trybu failover i odzyskiwania po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Aby przewinąć urządzenie w tryb fail over na inne urządzenie fizyczne, przejdź do [funkcji Praca w trybie fail over do tego samego urządzenia fizycznego StorSimple](storsimple-8000-device-failover-physical-device.md). Aby przejść w tryb fail over urządzenia fizycznego StorSimple do urządzenia StorSimple Cloud Appliance, przejdź do [pracy w trybie fail over do StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zostały sprawdzone zagadnienia dotyczące pracy awaryjnej urządzenia. Aby uzyskać więcej informacji, przejdź do [typowych zagadnień dotyczących pracy awaryjnej urządzenia](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Kroki, aby przejść awaryjnie na tym samym urządzeniu

Wykonaj następujące kroki, jeśli musisz przejść awaryjnie na tym samym urządzeniu.

1. Rób migawki w chmurze wszystkich woluminów w urządzeniu. Aby uzyskać więcej informacji, przejdź do [usługi StorSimple Device Manager w celu utworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).
2. Zresetuj urządzenie do ustawień fabrycznych. Postępuj zgodnie ze szczegółowymi instrukcjami [dotyczącymi resetowania urządzenia StorSimple do ustawień domyślnych ustawień fabrycznych](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Przejdź do usługi StorSimple Device Manager, a następnie wybierz pozycję **Urządzenia**. W bloku **Urządzenia** stare urządzenie powinno być wyświetlane w **trybie offline**.

    ![Urządzenie źródłowe w trybie offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Skonfiguruj urządzenie i zarejestruj je ponownie w usłudze StorSimple Device Manager. Nowo zarejestrowane urządzenie powinno być wyświetlane jako **Gotowe do skonfigurowania**. Nazwa urządzenia dla nowego urządzenia jest taka sama jak stare urządzenie, ale jest dołączona cyfrą wskazującą, że urządzenie zostało zresetowane do ustawień fabrycznych i ponownie zarejestrowane.

    ![Nowo zarejestrowane urządzenie gotowe do skonfigurowania](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. W przypadku nowego urządzenia zakończ konfigurację urządzenia. Aby uzyskać więcej informacji, przejdź do [kroku 4: Zakończ minimalną konfigurację urządzenia](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Na bloku **Urządzenia** stan urządzenia zmienia się na **Online**.

   > [!IMPORTANT]
   > **Najpierw wykonaj minimalną konfigurację lub dr może zakończyć się niepowodzeniem.**

    ![Nowo zarejestrowane urządzenie online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Wybierz stare urządzenie (stan w trybie offline) i na pasku poleceń kliknij pozycję **Tryb fail over**. W **bloku Przełącz awaryjne** wybierz stare urządzenie jako źródło i określ urządzenie docelowe jako nowo zarejestrowane urządzenie.

    ![Podsumowanie trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Aby uzyskać szczegółowe instrukcje, zobacz Praca w pracy na innym urządzeniu fizycznym.

7. Zostanie utworzone zadanie przywracania urządzenia, które można monitorować z **bloku Zadania.**

8. Po pomyślnym zakończeniu zadania, dostęp do nowego urządzenia i przejdź do **bloku kontenerów woluminu.** Sprawdź, czy wszystkie kontenery woluminów ze starego urządzenia zostały przeniesione na nowe urządzenie.

   ![Zmigrowane kontenery woluminów](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Po zakończeniu pracy awaryjnej można dezaktywować i usunąć stare urządzenie z portalu. Wybierz stare urządzenie (w trybie offline), kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Dezaktywuj**. Po dezaktywacji urządzenia stan urządzenia zostanie zaktualizowany.

     ![Urządzenie źródłowe wyłączone](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Wybierz dezaktywowane urządzenie, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Usuń**. Spowoduje to usunięcie urządzenia z listy urządzeń.

    ![Usunięto urządzenie źródłowe](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Następne kroki

* Po wykonaniu pracy awaryjnej może być konieczne [dezaktywację lub usunięcie urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Aby uzyskać informacje dotyczące korzystania z usługi StorSimple Device Manager, zobacz [Korzystanie z usługi StorSimple Device Manager do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

