---
title: Tryb failover StorSimple, odzyskiwania po awarii dla urządzeń z serii 8000 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak urządzenia StorSimple, inne fizyczne urządzenie lub urządzenia chmury w tryb failover.
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 5dc4a98bf889d38c62c76364289c2d58c14d771e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874983"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Tryb failover i odzyskiwania po awarii dla danego urządzenia z serii StorSimple 8000

## <a name="overview"></a>Omówienie

W tym artykule opisano funkcję pracy awaryjnej urządzenia dla urządzeń z serii StorSimple 8000 i jak ta funkcja może służyć do odzyskiwania urządzenia StorSimple w razie awarii. StorSimple używa trybu failover urządzenia do migracji danych z urządzenia źródłowego w centrum danych na inne urządzenie. Wskazówki zawarte w tym artykule dotyczą fizycznych urządzeń z serii StorSimple 8000 oraz urządzenia chmury wersjami oprogramowania aktualizacji 3 lub nowszym.

Używa StorSimple **urządzeń** bloku, aby uruchomić funkcję urządzenia pracy awaryjnej w przypadku awarii. Ten blok zawiera listę wszystkich urządzeń StorSimple połączona z usługą Menedżera urządzeń StorSimple.

![Blok urządzeń](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Odzyskiwania awaryjnego (DR) i urządzenia trybu failover

W przypadku odzyskiwanie po awarii urządzenie podstawowe przestanie działać. Urządzenie podstawowe jako używa StorSimple _źródła_ i przenosi dane skojarzonej chmury do innej _docelowej_ urządzenia. Ten proces jest nazywany *pracy awaryjnej*. Poniższa ilustracja przedstawia proces trybu failover.

![Co się stanie w tryb failover urządzeń?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Urządzenie docelowe dla trybu failover może być urządzenie fizyczne lub nawet urządzenia do chmury. Urządzenie docelowe może znajdować się w tej samej lub innej lokalizacji geograficznej niż urządzenia źródłowego.

W trybie failover można wybrać kontenery woluminów do migracji. StorSimple następnie zmienia własność kontenery woluminów z urządzenia źródłowego na urządzeniu docelowym. Gdy właścicielem kontenery woluminów StorSimple usuwa kontenery z urządzenia źródłowego. Po zakończeniu usuwania można w trybie wstecz urządzenia docelowego. _Powrót po awarii_ przenosi własności oryginalnego urządzenia źródłowego.

### <a name="cloud-snapshot-used-during-device-failover"></a>Migawka w chmurze używane podczas pracy awaryjnej urządzenia

Następujące DR najnowszej kopii zapasowej w chmurze służy do przywracania danych na urządzeniu docelowym. Aby uzyskać więcej informacji dotyczących migawki w chmurze, zobacz [wykonaj kopię zapasową ręczne przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

W serii StorSimple 8000 zasad tworzenia kopii zapasowych są skojarzone z kopii zapasowych. W przypadku wielu zasad tworzenia kopii zapasowych dla tego samego woluminu StorSimple wybiera zasady tworzenia kopii zapasowej z największą liczbą woluminów. StorSimple następnie wykorzystuje najnowszej kopii zapasowej z wybranych zasad tworzenia kopii zapasowej do przywrócenia danych na urządzeniu docelowym.

Załóżmy, że istnieją dwie zasady tworzenia kopii zapasowej, *defaultPol* i *customPol*:

* *defaultPol*: jeden wolumin *vol1*, jest uruchamiany codziennie, zaczynając od 22:30:00.
* *customPol*: cztery woluminy *vol1*, *vol2*, *vol3*, *vol4*, jest uruchamiany codziennie, zaczynając od 10:00 PM.

W takim przypadku StorSimple priorytetem spójności awarii i używa *customPol* została ona więcej woluminów. Najnowszej kopii zapasowej z tych zasad służy do przywracania danych. Aby uzyskać więcej informacji na temat sposobu tworzenia i zarządzania nimi zasad tworzenia kopii zapasowych, przejdź do [Zarządzanie zasadami tworzenia kopii zapasowej przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Typowe kwestie dotyczące pracy w trybie failover urządzenia

Przed przejścia w tryb failover urządzenia, przejrzyj następujące informacje:

* Przed rozpoczęciem pracy awaryjnej urządzenia, wszystkie woluminy w kontenery woluminów musi być w trybie offline. W przypadku nieplanowanego trybu failover woluminy StotSimple nastąpi automatyczne przejście w trybie offline. Jednak jeśli przeprowadzasz zaplanowany tryb failover (test odzyskiwania po awarii), wszystkich woluminów, które należy wykonać w trybie offline.
* Kontenery woluminów, które mają skojarzone migawka w chmurze są wyświetlane dla odzyskiwania po awarii. Musi istnieć co najmniej jeden kontener woluminów z migawki skojarzonej chmury, aby odzyskać dane.
* W przypadku migawki w chmurze, obejmujące między wieloma kontenery woluminów StorSimple nie powiedzie się w tych kontenerach woluminu jako zestaw. W rzadkich wystąpienia Jeśli istnieją migawki lokalne, obejmujące między wieloma kontenery woluminów, ale migawki skojarzonej chmury nie StorSimple w tryb failover migawki lokalne i lokalne dane zostaną utracone po odzyskiwania po awarii.
* Urządzenia dostępnych do odzyskiwania po awarii są urządzenia, które mają wystarczającą ilość miejsca, aby pomieścić kontenery wybranego woluminu. Urządzenia, które nie mają wystarczająco dużo miejsca, nie są wyświetlane jako urządzenia docelowe.
* Po DR (na czas) wydajność dostępu do danych może to mieć wpływ znacznie, urządzenie musi uzyskać dostęp do danych z chmury, a także przechowywane lokalnie.

#### <a name="device-failover-across-software-versions"></a>Tryb failover urządzeń między wersjami oprogramowania

Usługa Menedżer urządzenia StorSimple w ramach wdrożenia może mieć wielu urządzeń, zarówno fizyczne i w chmurze, wszystkie wersje uruchamianie innego oprogramowania.

Skorzystaj z poniższej tabeli, aby określić, jeśli może funkcjonować w trybie awaryjnym lub awarii na innym urządzeniu z systemem wersję innego oprogramowania i zachowanie typy woluminu podczas odzyskiwania po awarii.

#### <a name="failover-and-failback-across-software-versions"></a>Tryb failover i powrotu po awarii między wersjami oprogramowania

| Tryb failover / się nie powieść z kopii | Urządzenie fizyczne | Urządzenie w chmurze |
| --- | --- | --- |
| Aktualizacja 3, aby zaktualizować 4 |Woluminy warstwowe ponad warstwowej zakończyć się niepowodzeniem. <br></br>Woluminów przypiętych lokalnie w trybie Failover przypięty lokalnie. <br></br> Po przejściu w tryb failover podczas wykonywania migawki na urządzeniu aktualizacji 4 [śledzenia na podstawie heatmap](storsimple-update4-release-notes.md#whats-new-in-update-4) jest uruchamiane. |Przypięty lokalnie woluminach działających w trybie Failover jako warstwowego. |
| Aktualizacja 4 aktualizacji 3 |Woluminy warstwowe ponad warstwowej zakończyć się niepowodzeniem. <br></br>Woluminów przypiętych lokalnie w trybie Failover przypięty lokalnie. <br></br> Kopie zapasowe służące do przywrócenia zachować heatmap metadanych. <br></br>Na podstawie Heatmap śledzenia nie jest dostępne w wersji Update 3 po powrotu po awarii. |Przypięty lokalnie woluminach działających w trybie Failover jako warstwowego. |


## <a name="device-failover-scenarios"></a>Scenariusze trybu failover urządzenia

W przypadku awarii, możesz przełączyć urządzenia StorSimple:

* [Na urządzeniu fizycznym](storsimple-8000-device-failover-physical-device.md).
* [Do samego siebie](storsimple-8000-device-failover-same-device.md).
* [Na urządzeniu chmury](storsimple-8000-device-failover-cloud-appliance.md).

Poprzedni artykuły zawierają szczegółowe kroki dla każdego z powyższych przypadkach trybu failover.


## <a name="failback"></a>Powrót po awarii

Update 3 i nowszych StorSimple obsługuje powrót po awarii. Powrót po awarii jest na odwrocie trybu failover, docelowej staje się źródłem i oryginalnego urządzenia źródłowego podczas pracy awaryjnej teraz staje się urządzeniem docelowym. 

Podczas powrotu po awarii StorSimple ponownie synchronizuje dane do lokalizacji głównej, zatrzymanie operacji We/Wy i działanie aplikacji i przejść z powrotem do oryginalnej lokalizacji.

Po zakończeniu pracy w trybie failover StorSimple wykonuje następujące czynności:

* StorSimple czyści kontenery woluminów pracujących w z urządzenia źródłowego.
* StorSimple inicjuje zadania tła na kontenera woluminów (failover) na urządzeniu źródła. Jeśli użytkownik spróbuje się niepowodzeniem w trakcie zadanie jest w toku, zostanie wyświetlone powiadomienie w tym celu. Poczekaj, aż zakończeniu zadania, aby uruchomić powrót po awarii.
* Czas potrzebny na zakończenie usuwania kontenery woluminów zależy od różnych czynników, takich jak ilość danych, wiek danych, liczba kopii zapasowych i przepustowość sieci dostępną dla tej operacji.

Jeśli planowania testowy tryb failover, lub test powrotu po awarii, zaleca się przetestowanie kontenery woluminów o mniejszej ilości danych (GB). Zazwyczaj można uruchomić powrót po awarii 24 godziny po zakończeniu pracy awaryjnej.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Q. **Co się stanie, jeśli DR nie powiedzie się lub została częściowe Powodzenie?**

A. W przypadku niepowodzenia DR zaleca się, spróbuj ponownie. Drugie urządzenie zadanie trybu failover zna postęp pierwszego zadania i rozpoczyna się od tego momentu i jego nowszych wersjach.

Q. **Urządzenie można usunąć w trakcie pracy awaryjnej urządzenia?**

A. Nie można usunąć urządzenia w trakcie odzyskiwania po awarii. Urządzenie można usunąć tylko po zakończeniu odzyskiwania po awarii. Można monitorować postęp zadania urządzenia trybu failover w **zadania** bloku.

Q. **Gdy wyrzucanie elementów bezużytecznych rozpoczyna się na urządzeniu źródła, aby dane lokalne na urządzenia źródłowego jest usuwane?**

A. Wyrzucanie elementów bezużytecznych jest włączona na urządzeniu źródła tylko wtedy, gdy urządzenie jest całkowicie oczyszczone. Oczyszczanie obejmuje czyszczenia obiektów, które nie powiodły się za pośrednictwem z urządzenia źródłowego, takich jak woluminy, liczby obiektów kopii zapasowej (a nie dane), kontenery woluminów i zasady.

Q. **Co się stanie, jeśli zadanie usuwania skojarzone z kontenerów wolumin w urządzeniu źródła zakończy się niepowodzeniem?**

A.  Jeśli zadanie usuwania zakończy się niepowodzeniem, można ręcznie usunąć kontenery woluminów. W **urządzeń** bloku, wybierz urządzenia źródłowego i kliknij przycisk **kontenery woluminów**. Wybierz kontenery woluminów, których nie powiodła się za pośrednictwem i w dolnej części bloku, kliknij przycisk **usunąć**. Po usunięciu wszystkich nieudanych za pośrednictwem kontenery woluminów na urządzeniu źródła, możesz uruchomić powrót po awarii. Aby uzyskać więcej informacji, przejdź do [usunąć kontener woluminów](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Odzyskiwanie po awarii ciągłości biznesowej (BCDR)

Scenariusz odzyskiwania (BCDR) po awarii ciągłości biznesowej występuje, gdy całe centrum danych Azure przestanie działać. Ten scenariusz może mieć wpływ na usługi Menedżer StorSimple urządzenia i skojarzone urządzenia StorSimple.

Jeśli urządzenia StorSimple zarejestrowano tuż przed wystąpił awarii, następnie tego urządzenia może być konieczne przeprowadzenie resetowania do ustawień fabrycznych. Po awarii urządzenia StorSimple zostaną wyświetlone w portalu Azure w trybie offline. Urządzenia muszą zostać usunięte z portalu. Zresetowanie urządzenia do ustawień fabrycznych i ponownie zarejestrować w usłudze.

## <a name="next-steps"></a>Następne kroki

Gdy wszystko będzie gotowe włączyć tryb failover urządzenia, wybierz jedną z poniższych scenariuszy, aby uzyskać szczegółowe instrukcje:

- [Przełączyć do innego urządzenia fizycznego](storsimple-8000-device-failover-physical-device.md)
- [Przełączyć się do tego samego urządzenia](storsimple-8000-device-failover-same-device.md)
- [Przełączyć się urządzenia StorSimple chmury](storsimple-8000-device-failover-cloud-appliance.md)

Jeśli nie powiodło przez urządzenie, wybierz jedną z następujących opcji:

* [Dezaktywuj lub usuń urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

