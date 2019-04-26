---
title: Usługa StorSimple przejścia w tryb failover odzyskiwania po awarii dla urządzeń z serii 8000 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak urządzenia StorSimple na tym samym urządzeniu w trybie Failover.
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
ms.openlocfilehash: dd207eaad1a3e821724d51a890d0882bfffda131
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577381"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Urządzenie fizyczne StorSimple na tym samym urządzeniu w trybie Failover

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do pracy awaryjnej urządzeniem fizycznym StorSimple 8000 series do samego siebie w przypadku awarii. Usługa StorSimple używa funkcji trybu failover urządzenia do migracji danych z urządzenia fizycznego źródła w centrum danych do innego urządzenia fizycznego. Wskazówki zawarte w tym samouczku ma zastosowanie do serii StorSimple 8000 fizycznych urządzeń z systemem oprogramowania wersji Update 3 i nowszych.

Aby dowiedzieć się więcej na temat trybu failover urządzenia i sposobie ich użycia do odzyskania po awarii, przejdź do [trybu Failover i odzyskiwanie po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Do trybu failover na inne urządzenie fizyczne urządzenia fizycznego, przejdź do [awaryjnej w tym samym urządzeniem fizycznym StorSimple](storsimple-8000-device-failover-physical-device.md). Do trybu failover urządzenia fizycznego StorSimple do urządzenia StorSimple w chmurze, przejdź do [Failover urządzenia StorSimple w chmurze](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, przejrzeniu uwagi do trybu failover urządzenia. Aby uzyskać więcej informacji, przejdź do [typowe kwestie dotyczące pracy w trybie Failover urządzenia](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Kroki, aby przełączyć tryb failover do tego samego urządzenia

Jeśli potrzebujesz do pracy awaryjnej w tym samym urządzeniu, należy wykonać następujące czynności.

1. Wykonaj migawki w chmurze wszystkich woluminów na urządzeniu. Aby uzyskać więcej informacji, przejdź do [usługi Menedżer urządzeń StorSimple użycia do tworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).
2. Resetować urządzenie do ustawień fabrycznych. Wykonaj szczegółowe instrukcje [sposób resetowania urządzenia StorSimple do domyślnych ustawień fabrycznych](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie wybierz pozycję **urządzeń**. W **urządzeń** bloku starym urządzeniem powinien być wyświetlony jako **Offline**.

    ![Urządzenie źródłowe w trybie offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Skonfiguruj urządzenie, a następnie ponownie zarejestrować przy użyciu usługi Menedżer urządzeń StorSimple. Nowo zarejestrowanym urządzeniu powinien być wyświetlony jako **gotowe do skonfigurowania**. Nazwa urządzenia dla nowego urządzenia jest taka sama jak starym urządzeniem, ale dołączany wraz z liczb, aby wskazać, czy przywrócić ustawienia fabryczne i ponownie zarejestrować urządzenie.

    ![Gotowe do skonfigurowania nowo zarejestrowanych urządzeń](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Dla nowego urządzenia wykonaj konfigurację urządzenia. Aby uzyskać więcej informacji, przejdź do [krok 4: Przeprowadzenie minimalnej konfiguracji urządzenia](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Na **urządzeń** bloku zmiany stanu urządzenia **Online**.

   > [!IMPORTANT]
   > **Najpierw przeprowadzić minimalną konfigurację, lub danym odzyskiwaniem po awarii może zakończyć się niepowodzeniem.**

    ![Nowo zarejestrowane urządzenie w tryb online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Wybierz starym urządzeniem (stan w trybie offline), a następnie na pasku poleceń kliknij **w trybie Failover**. W **w trybie Failover** bloku wybierz starym urządzeniem jako źródło i określić urządzenie docelowe jako nowo zarejestrowanych urządzeń.

    ![Podsumowanie pracy awaryjnej](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Szczegółowe instrukcje można znaleźć błąd za pośrednictwem innego urządzenia fizycznego.

7. Zostanie utworzone zadanie przywracania urządzenia, czy można monitorować z **zadań** bloku.

8. Po pomyślnym ukończeniu zadania, dostęp do nowego urządzenia i przejdź do **kontenery woluminów** bloku. Sprawdź, czy wszystkie kontenery woluminów ze starym urządzeniem zostały zmigrowane do nowego urządzenia.

   ![Zmigrowane kontenery woluminów](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Po zakończeniu pracy w trybie failover można zdezaktywować i usunąć stare urządzenia z poziomu portalu. Wybierz stare urządzenia (w trybie offline), kliknij prawym przyciskiem myszy, a następnie wybierz **Dezaktywuj**. Po dezaktywacji urządzenia, stan urządzenia jest aktualizowana.

     ![Dezaktywowano urządzenie źródła](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Wybierz dezaktywowanego urządzenia, kliknij prawym przyciskiem myszy, a następnie wybierz **Usuń**. Spowoduje to usunięcie urządzenia z listy urządzeń.

    ![Urządzenie źródłowe usunięte](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Kolejne kroki

* Po wykonaniu przejścia w tryb failover, konieczne może być [dezaktywacji lub usunięcia urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Aby uzyskać informacje o sposobie używania usługi Menedżer urządzeń StorSimple, przejdź do [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

