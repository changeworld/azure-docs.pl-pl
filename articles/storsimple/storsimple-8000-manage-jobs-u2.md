---
title: Wyświetlanie zadań dla storsimple serii 8000 i zarządzanie nimi | Dokumenty firmy Microsoft
description: W tym artykule opisano storsimple usługi Menedżera zadań bloku i jak go używać do śledzenia ostatnich, bieżących i zaplanowanych zadań tworzenia kopii zapasowych.
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
ms.openlocfilehash: 462f8dafdffa7ee01e6ccf7945a1abfdff90db42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254874"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Usługa StorSimple Device Manager służy do wyświetlania zadań i zarządzania nimi (aktualizacja 3 i nowsze)

## <a name="overview"></a>Omówienie
**Bloku zadania** znajduje się jeden centralny portal do przeglądania zadań, które zostały uruchomione na urządzeniach podłączonych do usługi StorSimple Device Manager i zarządzania nimi. Można wyświetlić zaplanowane, uruchomione, ukończone, anulowane i nieudane zadania dla wielu urządzeń. Wyniki są prezentowane w formacie tabelarycznym.

![Ostrze zadania](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Możesz szybko znaleźć interesujące Cię zadania, filtrując w takich dziedzinach, jak:

* **Stan** — zadania mogą być w toku, powiodło się, anulowane, nie powiodło się, anulowanie lub powiodło się z błędami.
* **Zakres czasu** — zadania mogą być filtrowane na podstawie zakresu daty i godziny. Zakresy są po 1 godzinie, w ciągu ostatnich 24 godzin, w ciągu ostatnich 7 dni, ostatnich 30 dni, w ubiegłym roku lub niestandardowej dacie.
* **Typ** — typ zadania może być zaplanowane tworzenie kopii zapasowych, ręczne tworzenie kopii zapasowych, przywracanie kopii zapasowej, klonowanie woluminu, kontenery woluminu w trybie fail over, tworzenie lokalnie przypięty wolumin, modyfikowanie woluminu, instalowanie aktualizacji, zbieranie dzienników pomocy technicznej i tworzenie urządzenia w chmurze.
* **Urządzenia** — zadania są inicjowane na określonym urządzeniu podłączonym do usługi.
  
Filtrowane zadania są następnie tabelaryczne na podstawie następujących atrybutów:
  
* **Nazwa** — zaplanowana kopia zapasowa, ręczna kopia zapasowa, przywracanie kopii zapasowej, klonowanie woluminu, kontenery woluminów w trybie fail over, tworzenie lokalnie przypiętego woluminu, modyfikowanie woluminu, instalowanie aktualizacji, zbieranie dzienników pomocy technicznej lub tworzenie urządzenia w chmurze.
* **Stan** — uruchamianie, ukończone, anulowane, nie powiodło się, anulowanie lub zakończone z błędami.
* **Encja** — zadania mogą być skojarzone z woluminem, zasadą tworzenia kopii zapasowych lub urządzeniem. Na przykład zadanie klonowania jest skojarzone z woluminem, podczas gdy zaplanowane zadanie tworzenia kopii zapasowej jest skojarzone z zasadami tworzenia kopii zapasowej. Zadanie urządzenia jest tworzone w wyniku odzyskiwania po awarii (DR) lub operacji przywracania.
* **Urządzenie** — nazwa urządzenia, na którym zostało uruchomione zadanie.
* **Rozpoczęto** — czas rozpoczęcia zadania.
* **Czas trwania** — czas wymagany do ukończenia zadania.

Lista zadań jest odświeżana co 30 sekund.

Na tej stronie można wykonać następujące działania związane z pracą:

* Wyświetlanie szczegółów zadania
* Anulowanie zadania

## <a name="view-job-details"></a>Wyświetlanie szczegółów zadania
Wykonaj następujące kroki, aby wyświetlić szczegóły dowolnego zadania.

#### <a name="to-view-job-details"></a>Aby wyświetlić szczegóły zadania
1. Przejdź do usługi StorSimple Device Manager, a następnie kliknij pozycję **Zadania**.

2. W **zadaniach** bloku wyświetl zadania, które Cię interesują, uruchamiając kwerendę z odpowiednimi filtrami. Można wyszukiwać ukończone, uruchomione lub anulowane zadania.

    ![Ostrze zlecenia](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Zaznacz i kliknij zadanie.

    ![Ostrze zlecenia](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. W bloku szczegóły zadania można wyświetlić stan, szczegóły, statystyki czasu i statystyki danych.
   
    ![Szczegóły zadania](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Anulowanie zadania
Wykonaj następujące kroki, aby anulować uruchomione zadanie.

> [!NOTE]
> Niektórych zadań, takich jak modyfikowanie woluminu w celu zmiany typu woluminu lub rozszerzanie woluminu, nie można anulować.


### <a name="to-cancel-a-job"></a>Aby anulować zadanie
1. Na stronie **Zadania** wyświetl uruchomione zadania, które chcesz anulować, uruchamiając kwerendę z odpowiednimi filtrami. Wybierz zadanie.

2. Kliknij prawym przyciskiem myszy wybrane zadanie, aby wywołać menu kontekstowe, a następnie kliknij polecenie **Anuluj**.

    ![Szczegóły zadania](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. To zadanie zostało anulowane.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać zasadami tworzenia kopii zapasowych StorSimple.](storsimple-8000-manage-backup-policies-u2.md)
* Dowiedz się, jak [zarządzać urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

