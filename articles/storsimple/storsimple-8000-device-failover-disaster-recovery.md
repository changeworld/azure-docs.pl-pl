---
title: Tryb failover i odzyskiwanie po awarii dla urządzenia storsimple serii 8000
description: Dowiedz się, jak przewinąć urządzenie StorSimple w trybie fail over your StorSimple na siebie, innym urządzeniu fizycznym lub urządzeniu w chmurze.
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
ms.openlocfilehash: 179bc5cdf982792f41e0dec209341f346959a31a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397522"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Przechodzenie w tryb failover i odzyskiwanie po awarii dla urządzenia StorSimple z serii 8000

## <a name="overview"></a>Omówienie

W tym artykule opisano funkcję trybu failover urządzenia dla urządzeń z serii StorSimple 8000 i jak ta funkcja może służyć do odzyskiwania urządzeń StorSimple w przypadku wystąpienia awarii. StorSimple używa pracy awaryjnej urządzenia do migracji danych z urządzenia źródłowego w centrum danych do innego urządzenia docelowego. Wskazówki zawarte w tym artykule dotyczą urządzeń fizycznych serii StorSimple 8000 i urządzeń w chmurze z systemem aktualizacji 3 i nowszych.

StorSimple używa **urządzenia** bloku do uruchomienia funkcji pracy awaryjnej urządzenia w przypadku awarii. Ten blok zawiera listę wszystkich urządzeń StorSimple podłączonych do usługi StorSimple Device Manager.

![Ostrze urządzenia](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Odzyskiwanie po awarii (DR) i funkcja failover urządzenia

W scenariuszu odzyskiwania po awarii (DR) urządzenie podstawowe przestaje działać. StorSimple używa urządzenia podstawowego jako _źródła_ i przenosi skojarzone dane w chmurze do innego urządzenia _docelowego._ Ten proces jest określany jako *pracy awaryjnej*. Poniższa grafika przedstawia proces pracy awaryjnej.

![Co się dzieje w pracy awaryjnej urządzenia?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Urządzenie docelowe dla pracy awaryjnej może być urządzenie fizyczne lub nawet urządzenia w chmurze. Urządzenie docelowe może znajdować się w tym samym lub innym miejscu geograficznym niż urządzenie źródłowe.

Podczas pracy awaryjnej można wybrać kontenery woluminów do migracji. StorSimple następnie zmienia własność tych kontenerów woluminu z urządzenia źródłowego na urządzenie docelowe. Gdy kontenery woluminów zmienić własność StorSimple usuwa te kontenery z urządzenia źródłowego. Po zakończeniu usuwania można przywrócić urządzenie docelowe po awarii. _Powrót po awarii_ przenosi własność z powrotem do oryginalnego urządzenia źródłowego.

### <a name="cloud-snapshot-used-during-device-failover"></a>Migawka w chmurze używana podczas pracy awaryjnej urządzenia

Po dr najnowszej kopii zapasowej w chmurze jest używany do przywracania danych do urządzenia docelowego. Aby uzyskać więcej informacji na temat migawek w chmurze, zobacz [Korzystanie z usługi StorSimple Device Manager w celu wykonywania ręcznej kopii zapasowej](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

W serii StorSimple 8000 zasady tworzenia kopii zapasowych są skojarzone z kopiami zapasowymi. Jeśli istnieje wiele zasad tworzenia kopii zapasowych dla tego samego woluminu, storsimple wybiera zasady tworzenia kopii zapasowych z największą liczbą woluminów. StorSimple następnie używa najnowszej kopii zapasowej z wybranych zasad tworzenia kopii zapasowych, aby przywrócić dane na urządzeniu docelowym.

Załóżmy, że istnieją dwie zasady tworzenia kopii zapasowych, *defaultPol* i *customPol:*

* *defaultPol*: Jeden wolumin, *vol1*, działa codziennie od 22:30.
* *customPol*: Cztery tomy, *vol1*, *vol2*, *vol3*, *vol4*, działa codziennie od 22:00.

W takim przypadku StorSimple priorytety dla spójności awarii i używa *customPol,* ponieważ ma więcej woluminów. Najnowsza kopia zapasowa z tej zasady służy do przywracania danych. Aby uzyskać więcej informacji na temat tworzenia zasad tworzenia kopii zapasowych i zarządzania nimi, zobacz [Zarządzanie zasadami tworzenia kopii zapasowych za pomocą usługi StorSimple Device Manager](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Typowe zagadnienia dotyczące pracy awaryjnej urządzenia

Przed przełączeniem urządzenia w stan fail over a, zapoznaj się z następującymi informacjami:

* Przed uruchomieniem pracy awaryjnej urządzenia wszystkie woluminy w kontenerach woluminów muszą być w trybie offline. W nieplanowanej pracy awaryjnej woluminy StotSimple automatycznie przejdzie do trybu offline. Ale jeśli wykonujesz planowaną tryb failover (do testowania odzyskiwania po awarii), należy wykonać wszystkie woluminy w trybie offline.
* Tylko kontenery woluminów, które mają skojarzoną migawkę chmury są wyświetlane dla odzyskiwania po awarii. Musi istnieć co najmniej jeden kontener woluminu ze skojarzoną migawką chmury, aby odzyskać dane.
* Jeśli istnieją migawki w chmurze, które obejmują wiele kontenerów woluminów, StorSimple w trybie fail-over tych kontenerów woluminów jako zestaw. W rzadkich przypadkach, jeśli istnieją migawki lokalne, które obejmują wiele kontenerów woluminów, ale skojarzone migawki w chmurze nie, StorSimple nie działa w trybie fail over migawek lokalnych i dane lokalne są tracone po dr. dr.
* Dostępne urządzenia docelowe dla odzyskiwania po awarii to urządzenia, które mają wystarczająco dużo miejsca, aby pomieścić wybrane kontenery woluminów. Wszystkie urządzenia, które nie mają wystarczającej ilości miejsca, nie są wymienione jako urządzenia docelowe.
* Po dr (przez ograniczony czas), wydajność dostępu do danych może mieć znaczący wpływ, ponieważ urządzenie musi uzyskać dostęp do danych z chmury i przechowywać je lokalnie.

#### <a name="device-failover-across-software-versions"></a>Funkcja pracy awaryjnej urządzenia w różnych wersjach oprogramowania

Usługa StorSimple Device Manager we wdrożeniu może mieć wiele urządzeń, zarówno fizycznych, jak i chmurze, wszystkie z różnymi wersjami oprogramowania.

Poniższa tabela służy do określenia, czy można awaryjnie lub po awarii z innym urządzeniem z inną wersją oprogramowania i jak zachowują się typy woluminów podczas odzyskiwania po awarii.

#### <a name="failover-and-failback-across-software-versions"></a>Przejście w stan failover i powiększe po awarii w różnych wersjach oprogramowania

| Przewijaniu awaryjnego/ Powrót po awarii z | Urządzenie fizyczne | Urządzenie w chmurze |
| --- | --- | --- |
| Aktualizacja 3 do aktualizacji 4 |Woluminy warstwowe są przemijane w stan failed jako warstwowe. <br></br>Woluminy przypięte lokalnie są przesiąknięte awaryjnie jako przypięte lokalnie. <br></br> Po przejściu pracy awaryjnej podczas robienia migawki na urządzeniu z aktualizacją 4 rozpoczyna [się śledzenie oparte na mapach cieplnych.](storsimple-update4-release-notes.md#whats-new-in-update-4) |Woluminy przypięte lokalnie są awaryjne w miarę warstwowego. |
| Aktualizacja 4 do aktualizacji 3 |Woluminy warstwowe są przemijane w stan failed jako warstwowe. <br></br>Woluminy przypięte lokalnie są przesiąknięte awaryjnie jako przypięte lokalnie. <br></br> Kopie zapasowe używane do przywracania metadanych mapy cieplnej. <br></br>Śledzenie oparte na mapach cieplnych nie jest dostępne w aktualizacji 3 po powiększeniu po awarii. |Woluminy przypięte lokalnie są awaryjne w miarę warstwowego. |


## <a name="device-failover-scenarios"></a>Scenariusze pracy awaryjnej urządzenia

W przypadku awarii można zdecydować się na przełączenie urządzenia StorSimple w tryb fail over:

* [Do urządzenia fizycznego](storsimple-8000-device-failover-physical-device.md).
* [Do siebie](storsimple-8000-device-failover-same-device.md).
* [Do urządzenia w chmurze](storsimple-8000-device-failover-cloud-appliance.md).

Powyższe artykuły zawierają szczegółowe kroki dla każdego z powyższych przypadków pracy awaryjnej.


## <a name="failback"></a>Powrót po awarii

W przypadku aktualizacji 3 i nowszych wersji StorSimple obsługuje również powrót po awarii. Powrót po awarii jest tylko odwrotną od pracy awaryjnej, obiekt docelowy staje się źródłem, a oryginalne urządzenie źródłowe podczas pracy awaryjnej staje się teraz urządzeniem docelowym. 

Podczas powrotu po awarii StorSimple ponownie synchronizuje dane z powrotem do lokalizacji podstawowej, zatrzymuje we/wy i działania aplikacji i przechodzi z powrotem do oryginalnej lokalizacji.

Po zakończeniu pracy awaryjnej StorSimple wykonuje następujące akcje:

* StorSimple czyści kontenery woluminów, które są przejęte awaryjnie z urządzenia źródłowego.
* StorSimple inicjuje zadanie w tle na kontener woluminu (przejęte awaryjnie) na urządzeniu źródłowym. Jeśli spróbujesz wrócić po awarii, gdy zadanie jest w toku, otrzymasz powiadomienie w tej sprawie. Poczekaj, aż zadanie zostanie ukończone, aby rozpocząć powrót po awarii.
* Czas ukończenia usuwania kontenerów woluminów zależy od różnych czynników, takich jak ilość danych, wiek danych, liczba kopii zapasowych i przepustowość sieci dostępna dla operacji.

Jeśli planujesz testowe testy awaryjne lub testowe powroty po awarii, zaleca się przetestowanie kontenerów woluminów z mniejszą ilością danych (Gbs). Zazwyczaj można uruchomić powrót po awarii 24 godziny po zakończeniu pracy awaryjnej.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

PYTANIE: **Co się stanie, jeśli dr odzyskiwania po awarii lub ma częściowy sukces?**

A. Jeśli odzyskiwania po awarii, zaleca się spróbować ponownie. Drugie zadanie pracy awaryjnej urządzenia jest świadome postępu pierwszego zadania i rozpoczyna się od tego momentu.

PYTANIE: **Czy mogę usunąć urządzenie w trakcie pracy awaryjnej urządzenia?**

A. Nie można usunąć urządzenia, gdy dr jest w toku. Urządzenie można usunąć tylko po zakończeniu odzyskiwania po awarii. Można monitorować postęp zadania pracy awaryjnej urządzenia w **zadaniach zadań** bloku.

PYTANIE: **Kiedy rozpoczyna się zbieranie elementów bezużytecznych na urządzeniu źródłowym, aby dane lokalne na urządzeniu źródłowym zostały usunięte?**

A. Wyrzucanie elementów bezużytecznych jest włączone na urządzeniu źródłowym tylko po całkowitym oczyszczeniu urządzenia. Oczyszczanie obejmuje czyszczenie obiektów, które zostały przejęte awaryjnie z urządzenia źródłowego, takich jak woluminy, obiekty kopii zapasowych (nie dane), kontenery woluminów i zasady.

PYTANIE: **Co się stanie, jeśli zadanie usuwania skojarzone z kontenerami woluminów w urządzeniu źródłowym ulegnie awarii?**

A.  Jeśli zadanie usuwania nie powiedzie się, można ręcznie usunąć kontenery woluminów. W bloku **Urządzenia** wybierz urządzenie źródłowe i kliknij pozycję **Kontenery woluminów**. Wybierz kontenery woluminów, które zostały przejęte awaryjnie i w dolnej części bloku, kliknij przycisk **Usuń**. Po usunięciu wszystkich kontenerów woluminów awaryjnych na urządzeniu źródłowym można uruchomić powrót po awarii. Aby uzyskać więcej informacji, przejdź do temat [Usuwanie kontenera woluminów](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Odzyskiwanie po awarii ciągłości działania (BCDR)

Scenariusz odzyskiwania po awarii ciągłości biznesowej (BCDR) występuje, gdy całe centrum danych platformy Azure przestaje działać. W tym scenariuszu może mieć wpływ na usługę StorSimple Device Manager i skojarzone urządzenia StorSimple.

Jeśli urządzenie StorSimple zostało zarejestrowane tuż przed wystąpieniem awarii, to urządzenie może wymagać przywrócenia ustawień fabrycznych. Po awarii urządzenie StorSimple jest wyświetlane w witrynie Azure portal w trybie offline. To urządzenie musi zostać usunięte z portalu. Zresetuj domyślne ustawienia fabryczne urządzenia i zarejestruj go ponownie w usłudze.

## <a name="next-steps"></a>Następne kroki

Jeśli urządzenie jest gotowe do pracy awaryjnej urządzenia, wybierz jeden z następujących scenariuszy, aby uzyskać szczegółowe instrukcje:

- [Praca awaryjna na innym urządzeniu fizycznym](storsimple-8000-device-failover-physical-device.md)
- [Przeładuj awaryjnie na tym samym urządzeniu](storsimple-8000-device-failover-same-device.md)
- [Przełączenie awaryjne na urządzenie storsimple w chmurze](storsimple-8000-device-failover-cloud-appliance.md)

Jeśli urządzenie uległo awarii, wybierz jedną z następujących opcji:

* [Dezaktywuj lub usuń urządzenie StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Użyj usługi StorSimple Device Manager do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

