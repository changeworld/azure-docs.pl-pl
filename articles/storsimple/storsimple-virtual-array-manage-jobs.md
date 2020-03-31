---
title: Wyświetlanie zadań tablicy wirtualnej StorSimple i zarządzanie nimi | Dokumenty firmy Microsoft
description: W tym artykule opisano storsimple usługi Menedżera urządzeń zadania strony i jak go używać do śledzenia ostatnich i bieżących zadań dla StorSimple Virtual Array.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60302503"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Usługa Menedżer urządzeń StorSimple służy do wyświetlania zadań dla tablicy wirtualnej StorSimple
## <a name="overview"></a>Omówienie
**Bloku zadań** znajduje się jeden centralny portal do przeglądania i zarządzania zadaniami, które są uruchamiane na tablicach wirtualnych, które są połączone z usługą StorSimple Device Manager. Można wyświetlić uruchomione, ukończone i nieudane zadania dla wielu urządzeń wirtualnych. Wyniki są prezentowane w formacie tabelarycznym.

![Ostrze zadania](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Możesz szybko znaleźć interesujące Cię zadania, filtrując w takich dziedzinach, jak:

* **Zakres czasu** — zadania mogą być filtrowane na podstawie zakresu daty i godziny.
* **Urządzenia** — zadania są inicjowane na określonym urządzeniu podłączonym do usługi. Filtrowane zadania są następnie tabelaryczne na podstawie następujących atrybutów:
  
  * **Nazwa** — nazwa zadania może być **Wszystkie**, **Kopia zapasowa**, **Klonowanie**, **Fail over**, **Pobierz aktualizacje**lub **Zainstaluj aktualizacje**.
  * **Stan** — zadania mogą być **wszystkie**, **W toku**, **Powiodło się**, lub nie **powiodło się**lub **Anulowane**.
  * **Encja** — zadania mogą być skojarzone z woluminem, udziałem lub urządzeniem.
  * **Urządzenie** — nazwa urządzenia, na którym zostało uruchomione zadanie.
  * **Rozpoczęto** — czas rozpoczęcia zadania.
  * **Czas trwania** — czas trwania, na którym zadanie zostało uruchomione.
* **Stan** — można wyszukiwać wszystkie, uruchomione, ukończone lub nieudane zadania.
* **Typ zadania** — typ zadania może być wszystkim, tworzenie kopii zapasowych, przywracanie, praca awaryjna, pobieranie aktualizacji lub instalowanie aktualizacji.

Lista zadań jest odświeżana co 30 sekund.

## <a name="view-job-details"></a>Wyświetlanie szczegółów zadania
Wykonaj następujące kroki, aby wyświetlić szczegóły dowolnego zadania.

#### <a name="to-view-job-details"></a>Aby wyświetlić szczegóły zadania
1. Na **zadania** bloku, wyświetl zadania, które są zainteresowane, uruchamiając kwerendę z odpowiednimi filtrami. Można wyszukiwać ukończone lub uruchomione zadania.
2. Wybierz zadanie z tabelarycznej listy zadań.
   
    ![Ostrze zlecenia](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. U dołu strony kliknij pozycję **Szczegóły**.
4. W oknie dialogowym **Szczegóły** można wyświetlić statystyki stanu, szczegółów i czasu. Na poniższej ilustracji przedstawiono przykład okna dialogowego **Szczegóły zadania kopii zapasowej.**
   
    ![Szczegóły zadania](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Błędy zadań, gdy maszyna wirtualna jest wstrzymana w funkcji hypervisor
Gdy zadanie jest w toku na StorSimple Virtual Array i urządzenia (maszyna wirtualna aprowizowana w funkcji hypervisor) jest wstrzymana na więcej niż 15 minut, zadanie kończy się niepowodzeniem. Jest to spowodowane czasem niezsynchronizowania z czasem platformy Microsoft Azure. 

Zostanie wyświetlony następujący błąd: "Czas urządzenia jest niezsynchronizowany z czasem platformy Microsoft Azure o więcej niż 15 minut. Upewnij się, że funkcja hypervisor i czasy urządzenia są synchronizowane z serwerem NTP. Sprawdź, czy nie ma żadnych problemów z łącznością. Aby rozwiązać problemy z łącznością, uruchom testy diagnostyczne z lokalnego interfejsu użytkownika sieci Web urządzenia wirtualnego."

Te błędy dotyczą zadań tworzenia kopii zapasowych, przywracania, aktualizacji i pracy awaryjnej. Jeśli maszyna wirtualna jest aprowizowana w funkcji Hyper-V, urządzenie ostatecznie synchronizuje czas z hipernadzorcą. Gdy tak się stanie, możesz ponownie uruchomić zadanie.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się, jak zarządzać tablicą wirtualną StorSimple za pomocą lokalnego interfejsu użytkownika sieci Web.](storsimple-ova-web-ui-admin.md)

