---
title: Wyświetlanie zadań i zarządzanie nimi macierzy wirtualnej StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano strona zadania usługi Menedżer urządzeń StorSimple i jak z niej korzystać, aby śledzić ostatnich i bieżących zadań dla rozwiązania StorSimple Virtual Array.
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
ms.openlocfilehash: dbab2aaab2c12bef07748f54e5864d042f1c982a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60302503"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Wyświetl zadania dla rozwiązania StorSimple Virtual Array przy użyciu usługi Menedżer urządzeń StorSimple
## <a name="overview"></a>Omówienie
**Zadań** bloku udostępnia pojedynczy Centralny portal do przeglądania i zarządzania zadaniami, które są uruchamiane w macierzy wirtualnych, które są podłączone do usługi Menedżer urządzeń StorSimple. Można wyświetlić uruchomiony, ukończone i zakończone niepowodzeniem zadania dla wielu urządzeń wirtualnych. Wyniki są prezentowane w formacie tabelarycznym.

![Blok zadania](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Możesz szybko znaleźć zadania, które są zainteresowani przez filtrowanie według pól, takich jak:

* **Zakres czasu** — zadania mogą być filtrowane zależnie od zakres dat i godzin.
* **Urządzenia** — zadania są inicjowane na konkretnym urządzeniu połączoną z usługą. Następnie odfiltrowanych zadań wyszczególniono na podstawie następujących atrybutów:
  
  * **Nazwa** — Nazwa zadania może być **wszystkich**, **kopii zapasowej**, **klonowania**, **w trybie Failover**, **pobieranie aktualizacji**, lub **instalowania aktualizacji**.
  * **Stan** — zadania mogą być **wszystkich**, **w toku**, **Powodzenie**, lub **nie powiodło się**, lub **anulowane**.
  * **Jednostka** — zadania mogą być skojarzone z woluminu, udziału lub urządzenia.
  * **Urządzenie** — nazwa urządzenia, na którym zadanie zostało uruchomione.
  * **Data rozpoczęcia** — czas uruchomienia zadania.
  * **Czas trwania** — czas trwania na zostało uruchomione zadanie.
* **Stan** — możesz wyszukać wszystkie zadania uruchomione, ukończone lub nie powiodło się.
* **Typ zadania** — typ zadania mogą być wszystkie, tworzenia kopii zapasowych, przywracania, trybu failover, Pobierz aktualizacje lub instalowania aktualizacji.

Lista zadań są odświeżane co 30 sekund.

## <a name="view-job-details"></a>Wyświetlanie szczegółów zadania
Wykonaj poniższe kroki, aby wyświetlić szczegóły dowolnego zadania.

#### <a name="to-view-job-details"></a>Aby wyświetlić szczegóły zadania
1. Na **zadań** bloku wyświetlania zadań, o których chcesz się dowiedzieć, uruchamiając zapytanie o odpowiednie filtry. Można wyszukiwać zadania zakończone lub nie działają.
2. Wybierz zadanie z tabelarycznej listy zadań.
   
    ![Blok zadania](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. W dolnej części strony kliknij **szczegóły**.
4. W **szczegóły** okno dialogowe, można wyświetlić stan, szczegółowe informacje i dane statystyczne czas. Na poniższej ilustracji przedstawiono przykład **szczegóły zadania kopii zapasowej** okno dialogowe.
   
    ![Szczegóły zadania](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Niepowodzenia zadań, gdy maszyna wirtualna jest wstrzymana w funkcji hypervisor
Gdy zadanie jest w toku na rozwiązania StorSimple Virtual Array i urządzenia (maszyna wirtualna aprowizowana w funkcji hypervisor) zostało wstrzymane dla więcej niż 15 minut, zadanie nie powiedzie się. To ze względu na czas rozwiązania StorSimple Virtual Array jest zsynchronizowany z czasem Microsoft Azure. 

Zostanie wyświetlony następujący błąd: "Czas urządzenia nie jest zsynchronizowany z czasem Microsoft Azure przez ponad 15 minut. Upewnij się, że funkcja hypervisor i urządzenia, czas są zsynchronizowane z serwerem NTP. Sprawdź, czy nie występują żadne problemy z łącznością. Aby rozwiązać problemy z łącznością, uruchom testy diagnostyczne z poziomu lokalnego internetowego interfejsu użytkownika urządzenia wirtualnego."

Te błędy mają zastosowanie do zadania kopii zapasowej, przywracanie, aktualizacji i trybu failover. Jeśli maszyna wirtualna jest gotowa w funkcji Hyper-V, maszyny po pewnym czasie synchronizuje czas Twojej funkcji hypervisor. Gdy tak się stanie, możesz ponownie uruchomić zadanie.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się, jak używać lokalnego internetowego interfejsu użytkownika do administrowania rozwiązania StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

