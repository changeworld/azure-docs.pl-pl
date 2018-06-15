---
title: Tryb failover StorSimple, odzyskiwania po awarii dla urządzeń z serii 8000 | Dokumentacja firmy Microsoft
description: Informacje o sposobie pracy awaryjnej urządzenia StorSimple na tym samym urządzeniu.
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
ms.openlocfilehash: acc8929dc3476e9590e8e4d9526b38b7c0719570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874822"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Tryb failover urządzenia fizycznego StorSimple na tym samym urządzeniu

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do pracy awaryjnej urządzenia fizycznego serii StorSimple 8000 do samego siebie w przypadku awarii. StorSimple korzysta z funkcji trybu failover urządzenia do migracji danych z urządzenia fizycznego źródła w centrum danych do innego urządzenia fizycznego. Wskazówki zawarte w tym samouczku dotyczą z serii StorSimple 8000 fizyczne urządzenia z wersjami oprogramowania aktualizacji 3 lub nowszym.

Aby dowiedzieć się więcej o pracy awaryjnej urządzenia i sposobu ich wykorzystywania odzyskiwania po awarii, przejdź do [trybu Failover i odzyskiwania po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Aby tryb failover na inne urządzenie fizyczne urządzenia fizycznego, przejdź do [awaryjnej w tym samym urządzeniu fizycznym StorSimple](storsimple-8000-device-failover-physical-device.md). Aby przełączyć urządzenia fizycznego StorSimple na urządzeniu chmury StorSimple, przejdź do [funkcjonować w trybie awaryjnym urządzenia chmury StorSimple](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, należy przejrzeć zagadnienia dotyczące urządzenia trybu failover. Aby uzyskać więcej informacji, przejdź do [typowe kwestie dotyczące pracy w trybie failover urządzenia](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Kroki, aby przełączyć się na tym samym urządzeniu

Wykonaj następujące kroki, aby przełączyć się na tym samym urządzeniu.

1. Twórz migawki chmurze wszystkie woluminy w urządzeniu. Aby uzyskać więcej informacji, przejdź do [usługi StorSimple za pomocą Menedżera urządzeń do tworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).
2. Resetować urządzenie do ustawień fabrycznych. Postępuj zgodnie z instrukcjami szczegółowe [sposób resetowania urządzenia StorSimple do domyślnych ustawień fabrycznych](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Przejdź do usługi Menedżer StorSimple urządzenia, a następnie wybierz **urządzeń**. W **urządzeń** bloku starego urządzenia powinny być widoczne jako **Offline**.

    ![Urządzenia źródłowego w trybie offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Skonfiguruj urządzenie i zarejestruj go ponownie przy użyciu usługi Menedżer StorSimple urządzenia. Nowo zarejestrowane urządzenia powinny być widoczne jako **przejść do konfigurowania**. Nazwę urządzenia dla nowego urządzenia jest taka sama jak starym urządzeniem, ale dołączony liczb, aby wskazać, czy przywrócić ustawienia fabryczne i ponownie zarejestrować urządzenie.

    ![Nowo zarejestrowanym urządzeniu przejść do konfigurowania](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Nowe urządzenia należy przeprowadzić konfigurację urządzenia. Aby uzyskać więcej informacji, przejdź do [krok 4: przeprowadzenie minimalnej konfiguracji urządzenia](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Na **urządzeń** bloku zmiany stanu urządzenia **Online**.

   > [!IMPORTANT]
   > **Najpierw Zakończ minimalną konfigurację lub programu DR może zakończyć się niepowodzeniem.**

    ![Nowo zarejestrowanym urządzeniem w trybie online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Wybierz urządzenie stary (stan w trybie offline), a następnie na pasku poleceń kliknij **awaryjnie**. W **awaryjnie** bloku, wybierz starym urządzeniem jako źródło i określ urządzenie docelowe jako nowo zarejestrowanym urządzeniu.

    ![Podsumowanie trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Aby uzyskać szczegółowe instrukcje, zapoznaj się [przełączyć do innego urządzenia fizycznego](#fail-over-to-another-physical-device).

7. Tworzone jest zadanie przywracania urządzenia można monitorować z **zadania** bloku.

8. Po pomyślnym ukończeniu zadania dostępu do nowego urządzenia i przejdź do **kontenery woluminów** bloku. Sprawdź, czy wszystkie kontenery woluminów z urządzenia starego zostały zmigrowane do nowych urządzeń.

   ![Kontenery woluminów migracji](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Po zakończeniu pracy awaryjnej można zdezaktywować i usunąć stary urządzenie z portalu. Wybierz starego urządzenia (w trybie offline), kliknij prawym przyciskiem myszy, a następnie wybierz **Dezaktywuj**. Po dezaktywacji urządzenia, stan urządzenia jest aktualizowana.

     ![Urządzenia źródłowego dezaktywowany](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Wybierz dezaktywować urządzenie, kliknij prawym przyciskiem myszy, a następnie wybierz **usunąć**. Spowoduje to usunięcie urządzenia z listy urządzeń.

    ![Urządzenia źródłowego usunięte](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Następne kroki

* Po wykonaniu trybu failover, konieczne może być [dezaktywację lub usunięcie urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Aby uzyskać informacje o sposobie używania usługi Menedżer StorSimple urządzeń, przejdź do [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

