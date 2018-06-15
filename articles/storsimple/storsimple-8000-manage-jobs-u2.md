---
title: Wyświetl zadania i zarządzać nimi serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Opisuje bloku zadania usługi Menedżer StorSimple urządzenia oraz używać go do śledzenia ostatnie, bieżących i zaplanowanych zadań tworzenia kopii zapasowej.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: bf8038b7171053b75eeb9aed88bff4246e65a8a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874815"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Użyj usługi Menedżer StorSimple urządzenia, aby wyświetlić zadania i zarządzać nimi (Update 3 i nowsze)

## <a name="overview"></a>Omówienie
**Zadania** bloku zapewnia jednym portalu centralnej do przeglądania i zarządzania zadaniami, które zostały uruchomione na urządzeniach połączona z usługą Menedżera urządzeń StorSimple. Można wyświetlić zadań zaplanowanych, uruchomionych, zakończone, anulowane i nie powiodło się dla wielu urządzeń. Wyniki są prezentowane w formie tabeli.

![Bloku zadań](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Można szybko znaleźć zadania, które są zainteresowani przez filtrowanie w polach, takich jak:

* **Stan** — zadania mogą być w toku, zakończyło się pomyślnie, anulowane, nie powiodło się, anulowanie lub zakończyło się pomyślnie z błędów.
* **Zakres czasu** — zadania mogą być filtrowane w oparciu o zakres dat i godzin. Zakresy, dla których minął godzinę po 24 godzinach, ostatnie 7 dni, po 30 dni, ostatni rok lub Niestandardowa data.
* **Typ** — typ zadania można zaplanowanego tworzenia kopii zapasowej, ręcznego tworzenia kopii zapasowej i przywracania kopii zapasowej, Klonowanie woluminów, awaryjnie kontenery woluminów, utworzyć wolumin przypięty lokalnie, zmodyfikować woluminu, instalowania aktualizacji, zbierz dzienniki pomocy technicznej i utworzyć urządzenia chmury.
* **Urządzenia** — zadań są inicjowane na niektórych urządzeniach połączona z usługą.
  
Następnie wyszczególniono odfiltrowanych zadań na podstawie następujących atrybutów:
  
* **Nazwa** — zaplanowanego tworzenia kopii zapasowej, ręcznego tworzenia kopii zapasowych, przywracania kopii zapasowej woluminu w klonowania, tryb failover kontenery woluminów tworzenia woluminu przypiętego lokalnie, zmodyfikować woluminu, instalowania aktualizacji, zbierz dzienniki pomocy technicznej lub utworzyć urządzenia chmury.
* **Stan** — uruchomiona, zakończone, anulowane, nie powiodło się, anulowanie lub ukończone z błędami.
* **Jednostka** — zadania może być skojarzony z woluminu, zasad tworzenia kopii zapasowej lub urządzeniu. Na przykład zadanie klonowania jest skojarzony z woluminem, zaplanowane zadanie tworzenia kopii zapasowej jest skojarzone z zasadami tworzenia kopii zapasowej. Zadania urządzenia jest tworzony w wyniku odzyskiwania awaryjnego (DR) lub operacji przywracania.
* **Urządzenie** — nazwa urządzenia, na którym zadanie zostało uruchomione.
* **Rozpoczęto w** — czas uruchomienia zadania.
* **Czas trwania** — czas wymagany do zakończenia tego zadania.

Lista zadań są odświeżane co 30 sekund.

Na tej stronie można wykonywać następujące czynności związanych z pracą:

* Wyświetlanie szczegółów zadania
* Anulowanie zadania

## <a name="view-job-details"></a>Wyświetlanie szczegółów zadania
Wykonaj poniższe kroki, aby wyświetlić szczegóły wszystkie zadania.

#### <a name="to-view-job-details"></a>Aby wyświetlić szczegóły zadania
1. Przejdź do usługi Menedżer StorSimple urządzenia, a następnie kliknij przycisk **zadania**.

2. W **zadania** bloku wyświetlania zadań są zainteresowani, uruchamiając zapytanie o odpowiednie filtry. Można wyszukiwać ukończone, uruchomiona lub anulowane zadania.

    ![Blok zadania](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Wybierz, a następnie kliknij zadanie.

    ![Blok zadania](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. W bloku szczegóły zadania można wyświetlić stan, szczegóły, statystyk czasu i statystyki danych.
   
    ![Szczegóły zadania](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Anulowanie zadania
Wykonaj poniższe kroki, aby anulować zadanie uruchomione.

> [!NOTE]
> Niektóre zadania, takie jak modyfikowanie woluminu, aby zmienić typ woluminu lub rozszerzenie woluminu, nie można anulować.


### <a name="to-cancel-a-job"></a>Aby anulować zadanie
1. Na **zadania** pozycję Wyświetl uruchomione zadania, które chcesz anulować, uruchamiając zapytanie z filtrami odpowiednie. Wybierz zadanie.

2. Kliknij prawym przyciskiem myszy wybrane zadanie można wywołać z menu kontekstowego, a następnie kliknij przycisk **anulować**.

    ![Szczegóły zadania](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. To zadanie jest teraz anulowane.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [Zarządzanie zasadami tworzenia kopii zapasowej StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Dowiedz się, jak [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

