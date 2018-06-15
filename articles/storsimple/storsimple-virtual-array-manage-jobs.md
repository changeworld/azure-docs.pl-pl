---
title: Wyświetl zadania i zarządzać nimi tablicy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano na stronie zadań usługi Menedżer StorSimple urządzenia i jak z niego korzystać, aby śledzić najnowsze i bieżącego zadania dla tablicy wirtualnego StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: 3fd1c262a8ce94d8e98f2b066a8028d974b15b1d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "23927719"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Użyj usługi Menedżer StorSimple urządzenia do wyświetlania zadań dla tablicy wirtualnego StorSimple
## <a name="overview"></a>Przegląd
**Zadania** bloku udostępnia pojedynczy portal centralnej do przeglądania i zarządzania zadaniami, które są uruchamiane w tablicach wirtualnych, które są połączone z usługą Menedżera urządzeń StorSimple. Możesz wyświetlić zadania uruchomione, została zakończona i nie powiodło się dla wielu urządzeń wirtualnych. Wyniki są prezentowane w formie tabeli.

![Bloku zadań](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Można szybko znaleźć zadania, które są zainteresowani przez filtrowanie w polach, takich jak:

* **Zakres czasu** — zadania mogą być filtrowane w oparciu o zakres dat i godzin.
* **Urządzenia** — zadań są inicjowane na określonym urządzeniu połączona z usługą. Odfiltrowanych zadań następnie wyszczególniono w oparciu o następujące atrybuty:
  
  * **Nazwa** — Nazwa zadania może być **wszystkie**, **kopii zapasowej**, **klonowania**, **awaryjnie**, **pobieranie aktualizacji**, lub **instalowania aktualizacji**.
  * **Stan** — zadania mogą być **wszystkie**, **w toku**, **zakończyło się pomyślnie**, lub ****, lub **anulowane**.
  * **Jednostka** — zadania może być skojarzony z woluminu, udziału lub urządzenia.
  * **Urządzenie** — nazwa urządzenia, na którym zadanie zostało uruchomione.
  * **Rozpoczęto w** — czas uruchomienia zadania.
  * **Czas trwania** — czas trwania na zostało uruchomione zadanie.
* **Stan** — możesz wyszukać wszystkie zadania uruchomione, zakończone lub nie powiodło się.
* **Typ zadania** — typ zadania mogą być wszystkie, tworzenia kopii zapasowych, przywracania, trybu failover, Pobierz aktualizacje lub instalowania aktualizacji.

Lista zadań są odświeżane co 30 sekund.

## <a name="view-job-details"></a>Wyświetlanie szczegółów zadania
Wykonaj poniższe kroki, aby wyświetlić szczegóły wszystkie zadania.

#### <a name="to-view-job-details"></a>Aby wyświetlić szczegóły zadania
1. Na **zadania** bloku wyświetlania zadań są zainteresowani, uruchamiając zapytanie o odpowiednie filtry. Można wyszukiwać zadania zakończone lub nie działają.
2. Wybierz zadanie z tabelarycznej listę zadań.
   
    ![Blok zadania](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. W dolnej części strony kliknij **szczegóły**.
4. W **szczegóły** okno dialogowe, można wyświetlić stan, szczegóły i statystyk czasu. Na poniższej ilustracji przedstawiono przykład **szczegóły zadania tworzenia kopii zapasowej** okno dialogowe.
   
    ![Szczegóły zadania](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Błędy zadań, gdy maszyna wirtualna jest wstrzymana w funkcji hypervisor
Gdy zadanie jest w postępu tablica wirtualnego StorSimple oraz urządzenia (udostępnione w funkcji hypervisor, maszyna wirtualna) jest wstrzymana na więcej niż 15 minut, zadanie nie powiedzie się. To jest z powodu czasu tablicy wirtualnego StorSimple są zsynchronizowane z czasem Microsoft Azure. 

Zostanie wyświetlony następujący błąd: "Godzina na urządzeniu jest zsynchronizowany z czasem Microsoft Azure przez ponad 15 minut. Sprawdź, czy funkcja hypervisor i urządzenie, które czasy są zsynchronizowane z serwer NTP. Sprawdź, czy nie ma żadnych problemów łączności. Aby rozwiązać problemy z łącznością, uruchom testów diagnostycznych z lokalnej sieci web interfejsu użytkownika z urządzeniem wirtualnym."

Błędy te dotyczą zadań kopii zapasowych, przywracania, aktualizacji i pracy awaryjnej. Jeśli zainicjowaniu obsługi maszyny wirtualnej w funkcji Hyper-V, maszyny ostatecznie synchronizuje czas z funkcji hypervisor. Gdy tak się stanie, można ponownie uruchomić zadanie.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się, jak używać lokalnego interfejsu użytkownika sieci web do administrowania tablica wirtualnego StorSimple](storsimple-ova-web-ui-admin.md).

