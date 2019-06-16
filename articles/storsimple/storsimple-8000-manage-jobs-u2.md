---
title: Wyświetlanie zadań i zarządzanie nimi w przypadku serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Opis bloku zadań usługi Menedżer urządzeń StorSimple i jak z niej korzystać, aby śledzić najnowsze, bieżące i zaplanowane zadania tworzenia kopii zapasowej.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720697"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Usługa Menedżer urządzeń StorSimple umożliwia wyświetlanie zadań i zarządzanie nimi (aktualizacja Update 3 i nowszych)

## <a name="overview"></a>Omówienie
**Zadań** blok zapewnia jednego portalu centralnej do przeglądania i zarządzania zadaniami, które zostały uruchomione na urządzeniach połączonych z usługą Menedżera urządzeń StorSimple. Możesz wyświetlić zadania zaplanowane, uruchamianie, zakończone, anulowane i nie powiodło się dla wielu urządzeń. Wyniki są prezentowane w formacie tabelarycznym.

![Blok zadania](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Możesz szybko znaleźć zadania, które są zainteresowani przez filtrowanie według pól, takich jak:

* **Stan** — zadania może być w toku, zakończyło się pomyślnie, anulowany, nie powiodło się, anulowania lub z błędów.
* **Zakres czasu** — zadania mogą być filtrowane zależnie od zakres dat i godzin. Zakresy są Ostatnia 1 godzina, ostatnie 24 godziny, ostatnich 7 dni, ostatnie 30 dni, ubiegłego roku lub Niestandardowa data.
* **Typ** — typ zadania mogą być zaplanowane tworzenie kopii zapasowej, ręcznego tworzenia kopii zapasowej, przywracanie kopii zapasowej, Klonowanie woluminów, kontenery woluminów w tryb failover, Utwórz wolumin przypięty lokalnie, modyfikowania woluminu, instalowania aktualizacji, zbieranie dzienników pomocy technicznej i utworzyć urządzenie w chmurze.
* **Urządzenia** — zadania są inicjowane na niektórych urządzeniach połączoną z usługą.
  
Następnie wyszczególniono odfiltrowanych zadań na podstawie następujących atrybutów:
  
* **Nazwa** — zaplanowane tworzenie kopii zapasowej, ręcznego tworzenia kopii zapasowych, przywracania kopii zapasowej klonowania woluminu w tryb failover kontenery woluminów, Utwórz wolumin przypięty lokalnie, modyfikowania woluminu, instalowania aktualizacji, zbieranie dzienników pomocy technicznej lub Utwórz urządzenie w chmurze.
* **Stan** — uruchamianie, zakończone, anulowane, nie powiodło się, anulowania lub ukończone z błędami.
* **Jednostka** — zadania mogą być skojarzone z wolumin, zasady tworzenia kopii zapasowych lub urządzeniu. Na przykład zadanie klonowania jest skojarzony z woluminem, zaplanowane zadanie tworzenia kopii zapasowej jest skojarzone z zasadami tworzenia kopii zapasowej. W wyniku operacji przywracania lub odzyskiwania po awarii (DR) zostanie utworzone zadanie urządzenia.
* **Urządzenie** — nazwa urządzenia, na którym zadanie zostało uruchomione.
* **Data rozpoczęcia** — czas uruchomienia zadania.
* **Czas trwania** — czas wymagany do ukończenia zadania.

Lista zadań są odświeżane co 30 sekund.

Na tej stronie można wykonywać następujące czynności związanych z pracą:

* Wyświetlanie szczegółów zadania
* Anulowanie zadania

## <a name="view-job-details"></a>Wyświetlanie szczegółów zadania
Wykonaj poniższe kroki, aby wyświetlić szczegóły dowolnego zadania.

#### <a name="to-view-job-details"></a>Aby wyświetlić szczegóły zadania
1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij przycisk **zadań**.

2. W **zadań** bloku wyświetlania zadań, o których chcesz się dowiedzieć, uruchamiając zapytanie o odpowiednie filtry. Można wyszukiwać ukończone, uruchamiania lub anulowane zadania.

    ![Blok zadania](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Wybierz i kliknij zadanie.

    ![Blok zadania](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. W bloku szczegółów zadania można wyświetlić stan, szczegółowe informacje, statystyki czasu i statystyki danych.
   
    ![Szczegóły zadania](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Anulowanie zadania
Wykonaj poniższe kroki, aby anulować uruchomionego zadania.

> [!NOTE]
> Nie można anulować niektórych zadań, takich jak modyfikowanie woluminu, aby zmienić typ woluminu lub rozszerzanie woluminu.


### <a name="to-cancel-a-job"></a>Aby anulować zadanie
1. Na **zadań** strony, Wyświetl uruchomione zadania (), którą chcesz anulować, uruchamiając zapytanie o odpowiednie filtry. Wybierz zadanie.

2. Kliknij prawym przyciskiem myszy na wybranego zadania, aby wywołać menu kontekstowe, a następnie kliknij przycisk **anulować**.

    ![Szczegóły zadania](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. To zadanie jest teraz anulowane.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Zarządzanie zasadami kopii zapasowych usługi StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Dowiedz się, jak [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

