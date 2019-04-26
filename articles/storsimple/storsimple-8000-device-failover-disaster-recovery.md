---
title: Usługa StorSimple przejścia w tryb failover odzyskiwania po awarii dla urządzeń z serii 8000 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak w trybie Failover urządzenia StorSimple, inne fizyczne urządzenie lub urządzenia w chmurze.
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
ms.openlocfilehash: 079a2f153f257040d1899a33c9e255d633e526ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576376"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Tryb failover i odzyskiwanie po awarii dla urządzenia StorSimple 8000 series

## <a name="overview"></a>Omówienie

W tym artykule opisano funkcję trybu failover urządzenia dla urządzeń z serii StorSimple 8000 i jak ta funkcja może być używana w taki sposób, w przypadku poważnej awarii, odzyskanie urządzeń StorSimple. Usługa StorSimple używa urządzenia w tryb failover do migracji danych z urządzenia źródłowego w centrum danych do innego urządzenia docelowego. Wskazówki zawarte w tym artykule mają zastosowanie do fizycznych urządzeń z serii StorSimple 8000 i urządzeń w chmurze z wersjami oprogramowania Update 3 i nowszych.

Usługa StorSimple używa **urządzeń** bloku, aby uruchomić funkcję urządzenia pracy awaryjnej w przypadku awarii. Ten blok zawiera listę wszystkich urządzeń StorSimple połączoną z usługą Menedżera urządzeń StorSimple.

![Blok urządzeń](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Odzyskiwanie po awarii (DR) i tryb failover urządzenia

W scenariuszu odzyskiwania po awarii (DR) urządzenie podstawowe przestanie działać. Usługa StorSimple używa urządzenie podstawowe jako _źródła_ i przenosi dane w skojarzonej chmurze _docelowej_ urządzenia. Ten proces jest nazywany *trybu failover*. Poniższa ilustracja przedstawia proces przejścia w tryb failover.

![Co się dzieje w tryb failover urządzenia?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Na urządzeniu docelowym przejścia w tryb failover może być urządzenie fizyczne lub nawet urządzenia w chmurze. Urządzenie docelowe może znajdować się w tej samej lub innej lokalizacji geograficznej niż urządzenia źródłowego.

Podczas pracy awaryjnej można wybrać kontenerów woluminów dla migracji. Usługa StorSimple następnie zmienia własności te kontenery woluminów z urządzenia źródłowego na urządzeniu docelowym. Po właścicielem kontenery woluminów StorSimple usuwa te kontenery z urządzenia źródłowego. Po zakończeniu usuwania możesz można wykonać powrotu po awarii na urządzeniu docelowym. _Powrót po awarii_ przenosi własność oryginalnego urządzenia źródłowego.

### <a name="cloud-snapshot-used-during-device-failover"></a>Migawka w chmurze używane w tryb failover urządzenia

Następujące odzyskiwania po awarii najnowszej kopii zapasowej w chmurze służące do przywrócenia danych na urządzeniu docelowym. Aby uzyskać więcej informacji na temat migawki w chmurze, zobacz [korzystać z usługi Menedżer urządzeń StorSimple do podejmowania ręcznego tworzenia kopii zapasowej](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

W serii StorSimple 8000 zasadami tworzenia kopii zapasowych są skojarzone z kopii zapasowych. W przypadku wielu zasad tworzenia kopii zapasowych tego samego woluminu StorSimple wybiera zasad tworzenia kopii zapasowej z największą liczbą woluminów. Usługa StorSimple używa następnie najnowszej kopii zapasowej z wybranych zasadach kopii zapasowych do przywrócenia danych na urządzeniu docelowym.

Załóżmy, że istnieją dwie zasady tworzenia kopii zapasowej, *defaultPol* i *customPol*:

* *defaultPol*: Jeden wolumin *vol1*, jest uruchamiane codzienne o 10:30 PM.
* *customPol*: Cztery woluminy *vol1*, *vol2*, *vol3*, *vol4*, jest uruchamiany codziennie o 10:00.

W takim przypadku StorSimple priorytetem dla spójności na poziomie awarii i używa *customPol* ponieważ ta kolumna ma więcej woluminów. Najnowszej kopii zapasowej z tych zasad służy do przywracania danych. Aby uzyskać więcej informacji na temat sposobu tworzenia i zarządzania zasadami tworzenia kopii zapasowych, przejdź do [usługi Menedżer urządzeń StorSimple umożliwia zarządzanie zasadami kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Typowe kwestie dotyczące pracy w trybie Failover urządzenia

Przed przejścia w tryb failover urządzenia, przejrzyj następujące informacje:

* Przed uruchomieniem trybu failover urządzenia, wszystkie woluminy w kontenerach woluminu musi być w trybie offline. W przypadku nieplanowanego trybu failover StotSimple woluminy zostaną automatycznie przejdą w tryb offline. Ale jeśli przeprowadzasz zaplanowany tryb failover (test odzyskiwania po awarii) wszystkich woluminów, które należy wykonać w trybie offline.
* Kontenery woluminów, które mają skojarzoną migawkę w chmurze są wymienione na potrzeby odzyskiwania po awarii. Musi istnieć co najmniej jeden kontener woluminów w migawce skojarzonej chmurze, aby odzyskać dane.
* W przypadku migawki w chmurze, które rozciągają się w różnych kontenerach woluminów StorSimple nie powiedzie się za pośrednictwem tych kontenerów woluminów jako zestaw. W rzadkich wystąpienia Jeśli istnieją migawki lokalne, które rozciągają się w różnych kontenerach woluminów, ale nie obsługują migawki w chmurze skojarzone, StorSimple w trybie Failover migawki lokalne i dane lokalne zostaną utracone po odzyskiwania po awarii.
* Te urządzenia dostępne do odzyskiwania po awarii są urządzenia, które mają wystarczającą ilość miejsca na wybrane kontenery woluminów. Wszystkie urządzenia, które nie mają wystarczającej ilości miejsca, nie są wyświetlane jako urządzenia docelowe.
* Po odzyskiwania po awarii (na czas) wydajność dostępu do danych może mieć wpływ na znacznie, jak urządzenie musi uzyskać dostęp do danych z chmury i zapisz go lokalnie.

#### <a name="device-failover-across-software-versions"></a>Tryb failover urządzenia między wersjami oprogramowania

Usługa Menedżer urządzeń StorSimple w ramach wdrożenia mogą mieć wiele urządzeń, zarówno fizyczne i w chmurze — wszystkie uruchomione różne wersje oprogramowania.

Skorzystaj z poniższej tabeli, do określenia, czy można go w tryb failover i powrót po awarii do innego urządzenia z uruchomioną wersję innego oprogramowania i zachowaniem typów woluminów podczas odzyskiwania po awarii.

#### <a name="failover-and-failback-across-software-versions"></a>Tryb failover i powrotu po awarii między wersjami oprogramowania

| W trybie Failover / powrót po awarii z | Urządzenie fizyczne | Urządzenie w chmurze |
| --- | --- | --- |
| Aktualizacja 3, aby zaktualizować 4 |Woluminy warstwowe ponad warstwowego zakończyć się niepowodzeniem. <br></br>Woluminy przypięte lokalnie pracy awaryjnej przypięty lokalnie. <br></br> Po przejściu w tryb failover, gdy migawki na urządzeniu z aktualizacją Update 4 [śledzenia na podstawie mapy cieplnej](storsimple-update4-release-notes.md#whats-new-in-update-4) aktywowany. |Lokalnie przypięte woluminy pracy awaryjnej jako warstwowego. |
| Update 4, aby zaktualizować 3 |Woluminy warstwowe ponad warstwowego zakończyć się niepowodzeniem. <br></br>Woluminy przypięte lokalnie pracy awaryjnej przypięty lokalnie. <br></br> Kopie zapasowe służące do przywrócenia zachować metadane do mapy cieplnej. <br></br>Śledzenie na podstawie mapy cieplnej nie jest dostępna w wersji Update 3, zgodnie z powrotem po awarii. |Lokalnie przypięte woluminy pracy awaryjnej jako warstwowego. |


## <a name="device-failover-scenarios"></a>Scenariusze trybu failover urządzenia

W przypadku awarii, może wybrać do trybu failover urządzenia StorSimple:

* [Na urządzeniu fizycznym](storsimple-8000-device-failover-physical-device.md).
* [Do samego siebie](storsimple-8000-device-failover-same-device.md).
* [Aby urządzenie w chmurze](storsimple-8000-device-failover-cloud-appliance.md).

Poprzedni artykuły zawierają szczegółowy opis kroków, dla każdego z powyższych przypadkach trybu failover.


## <a name="failback"></a>Powrót po awarii

Update 3 i nowszych wersjach StorSimple obsługuje także powrotu po awarii. Powrót po awarii jest po prostu odwrotna do przejścia w tryb failover i docelowym źródłem oryginalnego źródła podczas pracy w trybie failover teraz staje się na urządzeniu docelowym. 

Podczas powrotu po awarii StorSimple ponownie synchronizuje dane do lokalizacji głównej, zatrzymanie We/Wy i działania aplikacji i przejść z powrotem do oryginalnej lokalizacji.

Po zakończeniu przejścia w tryb failover StorSimple wykonuje następujące czynności:

* Usługa StorSimple czyści kontenery woluminów, które są przełączone w tryb failover z urządzenia źródłowego.
* Usługa StorSimple inicjuje zadania w tle na kontener woluminów (failover) na urządzeniu źródłowym. Próba się nie powieść ponownie w przypadku, gdy zadanie jest w toku, otrzymasz powiadomienie w tym celu. Poczekaj, aż zadanie zostało ukończone, aby rozpocząć powrotu po awarii.
* Czas, aby zakończyć usuwanie kontenerów woluminów, zależy od różnych czynników, takich jak ilość danych, wiek danych, liczby kopii zapasowych i przepustowość sieci dostępną dla tej operacji.

Jeśli planowane jest test pracy w trybie Failover lub testu powrotu po awarii, zaleca się przetestowanie kontenery woluminów przy użyciu mniejszej ilości danych (GB). Zazwyczaj można uruchomić powrotu po awarii 24 godziny po zakończeniu pracy w trybie failover.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

PYTANIE: **Co się stanie, jeśli odzyskiwania po awarii ulegnie awarii lub ma częściowe Powodzenie?**

A. W przypadku niepowodzenia odzyskiwania po awarii zaleca się, spróbuj ponownie. Drugie zadanie urządzenia w tryb failover ma informacje o postępie pierwszego zadania i rozpoczyna się od tego momentu i nowszych wersjach.

PYTANIE: **Urządzenie można usunąć w trakcie pracy w trybie failover urządzenia?**

A. Nie można usunąć urządzenia, w trakcie odzyskiwania po awarii. Urządzenie można usunąć tylko po ukończeniu odzyskiwania po awarii. Możesz monitorować postęp zadania trybu failover urządzenia w **zadań** bloku.

PYTANIE: **Podczas wyrzucania elementów bezużytecznych zostanie uruchomiona na urządzeniu źródłowym, aby dane lokalne na urządzenia źródłowego zostaną usunięte?**

A. Wyrzucanie elementów bezużytecznych jest włączona na urządzeniu źródłowym, tylko wtedy, gdy urządzenie jest całkowicie oczyszczone. Oczyszczanie obejmuje czyszczenie obiektów, które zostały przełączone w tryb failover z urządzenia źródłowego, takich jak woluminy, tworzenia kopii zapasowej obiektów (a nie dane), kontenery woluminów i zasady.

PYTANIE: **Co się stanie, jeśli skojarzone z kontenerami woluminów na urządzeniu źródła Usuń zadanie zakończy się niepowodzeniem?**

A.  Jeśli zadanie usuwania zakończy się niepowodzeniem, można ręcznie usunąć kontenery woluminów. W **urządzeń** bloku wybierz urządzenia źródłowego i kliknij przycisk **kontenery woluminów**. Wybierz kontenery woluminów, które nie powiodło się za pośrednictwem i w dolnej części bloku, kliknij przycisk **Usuń**. Po usunięciu wszystkich nieudane za pośrednictwem kontenerów woluminów na urządzeniu źródłowym, można uruchomić powrotu po awarii. Aby uzyskać więcej informacji, przejdź do [usuwanie kontenera woluminów](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Odzyskiwanie po awarii ciągłość działania firmy (BCDR)

Dotyczy scenariusza biznesowego ciągłości działania po awarii recovery (BCDR) występuje, gdy całe centrum danych platformy Azure, przestanie działać. Ten scenariusz może mieć wpływ na usługi Menedżer urządzeń StorSimple i skojarzone urządzenia StorSimple.

Jeśli urządzenie StorSimple zostało zarejestrowane tylko w przypadku, przed wystąpieniem awarii, to urządzenie może być konieczne przeprowadzenie resetowania do ustawień fabrycznych. Po awarii urządzenie StorSimple jest wyświetlane w portalu Azure w trybie offline. Urządzenia muszą zostać usunięte z portalu. Resetowanie urządzenia do domyślnych ustawień fabrycznych i ponownie zarejestrować w usłudze.

## <a name="next-steps"></a>Kolejne kroki

Jeśli wszystko jest gotowe do przejścia w tryb failover urządzenia, wybierz jedną z następujących scenariuszy w celu uzyskania szczegółowych instrukcji:

- [Pracy awaryjnej na innym urządzeniu fizycznych](storsimple-8000-device-failover-physical-device.md)
- [Na tym samym urządzeniu pracy awaryjnej](storsimple-8000-device-failover-same-device.md)
- [Failover urządzenia StorSimple w chmurze](storsimple-8000-device-failover-cloud-appliance.md)

Jeśli urządzenia do trybu failover ma nie powiodła się, wybierz jedną z następujących opcji:

* [Dezaktywowanie i usuwanie urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Usługa Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

