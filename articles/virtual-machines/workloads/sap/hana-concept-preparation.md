---
title: Zasady odzyskiwania po awarii i przygotowania na platformie SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Zasady odzyskiwania po awarii i przygotowania na platformie SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb1ed063cb11a82d786badd3f63b2d4b6932ce13
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709731"
---
# <a name="disaster-recovery-principles"></a>Zasady odzyskiwania po awarii

Dużych wystąpień HANA oferuje funkcje odzyskiwania po awarii, między sygnatury duże wystąpienie oprogramowania HANA w różnych regionach platformy Azure. Na przykład w przypadku wdrożenia jednostek duże wystąpienie oprogramowania HANA w regionie zachodnie stany USA Azure służy jednostki duże wystąpienie oprogramowania HANA w regionie wschodnie stany USA jako jednostka odzyskiwania po awarii. Jak wspomniano wcześniej, odzyskiwania po awarii nie jest konfigurowana automatycznie, ponieważ wymaga ona płacisz z inną jednostką duże wystąpienie oprogramowania HANA w regionie odzyskiwania po awarii. Instalator odzyskiwania po awarii działa dla ustawień skalowania w górę, a także skalowania w poziomie. 

W scenariuszach wdrożone do tej pory klientów jednostka jest używana w regionie odzyskiwania po awarii do uruchamiania systemów nieprodukcyjnych, które używają zainstalowanego wystąpienia platformy HANA. Jednostka dużych wystąpień HANA musi mieć tej samej jednostki SKU jako jednostki SKU, używane do celów produkcyjnych. Na poniższej ilustracji przedstawiono konfigurację dysku od jednostki serwera w regionie platformy Azure środowiska produkcyjnego i regionie odzyskiwania po awarii wygląda następująco:

![W konfiguracji odzyskiwania po awarii z punktu widzenia dysku](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Jak pokazano na poniższej ilustracji — omówienie, należy następnie kolejność drugi zestaw woluminów dysku. Woluminy dysku docelowego są dostępne w taki sam rozmiar jak wielkości produkcji dla wystąpienia produkcyjnego w jednostkach odzyskiwania po awarii. Te woluminy dysku są skojarzone z jednostki serwera duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii. Następujące woluminy są replikowane z regionu produkcji do lokacji odzyskiwania po awarii:

- /hana/data
- / hana/logbackups 
- /Hana/Shared (obejmuje/usr/sap)

Wolumin /hana/log nie jest replikowana, ponieważ dziennik transakcji platformy SAP HANA nie jest potrzebna w taki sposób, który odbywa się przywracanie z tych woluminów. 

Podstawy funkcji odzyskiwania po awarii oferowana jest funkcje replikacji magazynu oferowana przez infrastrukturę dużych wystąpień HANA. Funkcje, które jest używane po stronie magazynu nie jest stały strumień zmiany, które są replikowane w sposób asynchroniczny, ponieważ wprowadzenia zmiany do woluminu magazynu. Zamiast tego jest mechanizm, który opiera się na fakcie, że migawki te woluminy są tworzone na bieżąco. Różnica między już replikowanych migawki i nową migawkę, która nie została jeszcze zreplikowana jest następnie przekazywane do lokacji odzyskiwania po awarii na woluminy dysku docelowego.  Te migawki są przechowywane w woluminach, a w przypadku trybu failover odzyskiwania po awarii trzeba przywrócić na tych woluminach.  

Pierwszy transfer pełnych danych woluminu powinien być przed ilość danych staje się mniejszy niż różnic między migawkami. Z tego powodu woluminy w lokacji odzyskiwania po awarii zawierają każdy jeden migawek woluminu wykonywane w lokacji produkcyjnej. Po pewnym czasie można użyć tego systemu odzyskiwania po awarii można pobrać stanu starszych odzyskać utracone dane, bez wycofywania w systemie produkcyjnym.

W przypadku wdrożenia usługi MCOD wielu wystąpieniom niezależnie od oprogramowania SAP HANA w jednej jednostce dużych wystąpień HANA oczekuje się, że wszystkie wystąpienia oprogramowania SAP HANA otrzymują magazynu replikowane do odzyskiwania po awarii.

W przypadku których pełnić funkcji wysokiej dostępności replikacji systemu HANA w przypadku witryn produkcyjnych i używane replikacji na podstawie magazynu dla danej lokacji odzyskiwania po awarii ilości oba węzły w lokacji głównej do wystąpienia usługi odzyskiwania po awarii są replikowane. Należy zakupić dodatkowy magazyn (ten sam rozmiar od węzła podstawowego) w lokacji odzyskiwania po awarii, aby pomieścić replikacji z podstawowego i pomocniczego do odzyskiwania po awarii. 



>[!NOTE]
>Funkcje replikacji magazynu dużych wystąpień HANA jest dublowania i replikacji migawek magazynu. Jeśli nie wykonasz migawek magazynu wprowadzonego w kopii zapasowej i przywracania sekcji tego artykułu, nie może być dowolnym replikacji do lokacji odzyskiwania po awarii. Wykonanie migawki magazynu to warunek wstępny do magazynu replikacji do lokacji odzyskiwania po awarii.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Przygotowywanie scenariusza odzyskiwania po awarii
W tym scenariuszu masz systemu produkcyjnego w dużych wystąpieniach HANA w regionie platformy Azure w środowisku produkcyjnym. Aby uzyskać instrukcje, które należy wykonać, załóżmy, że identyfikator SID tego systemu HANA jest "PRD" i że masz systemu nieprodukcyjnych w dużych wystąpieniach HANA w regionie odzyskiwania po awarii Azure. W ostatniej Załóżmy, że jego identyfikator SID jest "TST." Na poniższej ilustracji przedstawiono tę konfigurację:

![Początek konfiguracji odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Jeśli wystąpienie serwera nie ma jeszcze nie zostało uporządkowane z zestawem woluminu dodatkowego miejsca do magazynowania, SAP HANA na dołącza usługi Azure Service Management dodatkowego zestawu woluminów jako element docelowy dla repliki produkcji do jednostki dużych wystąpień HANA, na którym jest uruchomiony TST Wystąpienie oprogramowania HANA. W tym celu należy podać identyfikator SID wystąpienie oprogramowania HANA produkcji. Po oprogramowanie SAP HANA na usługi Azure Service Management załącznika tych woluminów, musisz zainstalować tych woluminów do jednostki dużych wystąpień HANA.

![Następnym krokiem konfiguracji odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Następnym krokiem jest dla Ciebie zainstalować drugie wystąpienie SAP HANA w jednostce duże wystąpienie oprogramowania HANA w regionie odzyskiwania po awarii platformy Azure, w którym jest uruchamiane wystąpienie oprogramowania HANA TST. Nowo zainstalowane wystąpienie SAP HANA musi mieć ten sam identyfikator SID. Użytkownicy utworzeni konieczne tych samych identyfikatorów UID i identyfikator grupy, który zawiera wystąpienie produkcyjne. Odczyt [kopii zapasowej i przywracania](hana-backup-restore.md) Aby uzyskać szczegółowe informacje. Jeśli instalacja się powiodła, należy:

- Wykonaj krok 2 przygotowania migawki magazynu, które są opisane w [kopii zapasowej i przywracania](hana-backup-restore.md).
- Utwórz klucz publiczny dla jednostki odzyskiwania po awarii w dużych wystąpień HANA jednostki, jeśli nie zostały jeszcze zrobione. Zobacz krok 3 Przygotowanie migawek magazynu, które są opisane w [kopii zapasowej i przywracania](hana-backup-restore.md).
- Obsługa *HANABackupCustomerDetails.txt* nowe wystąpienie oprogramowania HANA i testów czy łączność w magazynie działa prawidłowo.  
- Zatrzymaj nowo zainstalowane wystąpienie SAP HANA w jednostce duże wystąpienie oprogramowania HANA w regionie odzyskiwania po awarii platformy Azure.
- Odinstaluj woluminy te PRD i skontaktuj się z oprogramowania SAP HANA na zarządzanie usługami platformy Azure. Woluminy nie może pozostać zainstalowanego do jednostki, ponieważ nie mogą być dostępne podczas działania jako cel replikacji magazynu.  

![Krok instalacji odzyskiwania po awarii przed nawiązaniem replikacji](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Zespół operacyjny ustanawia relację replikacji między woluminy PRD w regionie platformy Azure w środowisku produkcyjnym, a woluminy PRD w regionie odzyskiwania po awarii platformy Azure.

>[!IMPORTANT]
>Wolumin /hana/log nie jest replikowana, ponieważ nie jest konieczne przywrócenie zreplikowanej bazy danych SAP HANA do spójnego stanu w lokacji odzyskiwania po awarii.

Następnie należy skonfigurować lub dostosować harmonogram tworzenia kopii zapasowych migawki magazynu, aby przejść do swojej RTO i RPO w przypadku awarii. Aby zminimalizować cel punktu odzyskiwania, należy ustawić następujący interwały replikacji w usłudze dużych wystąpień HANA:
- W przypadku woluminów objętych połączone migawki (typ migawki **hana**), zestawu do replikowania co 15 minut do celów woluminu magazynu równoważne w lokacji odzyskiwania po awarii.
- Na woluminie kopii zapasowej dziennika transakcji (typ migawki **dzienniki**), zestawu do replikowania co 3 minuty do celów woluminu magazynu równoważne w lokacji odzyskiwania po awarii.

Aby zminimalizować cel punktu odzyskiwania, wprowadź następujące ustawienia:
- Wykonaj **hana** typu magazynu migawek (zobacz "krok 7: Wykonywanie migawek") co 30 minut do 1 godziny.
- Wykonaj kopie zapasowe dziennika transakcji platformy SAP HANA co 5 minut.
- Wykonaj **dzienniki** typu magazynu migawek co 5 – 15 minut. Za pomocą tego okresu interwał osiągasz RPO około 15 25 minut.

Dzięki tej opcji instalacji, sekwencji, kopie zapasowe dziennika transakcji, magazynu migawek i replikacji transakcji HANA Zaloguj się dane kopii zapasowej woluminu i/hana/i /hana/shared (obejmuje/usr/sap), może wyglądać dane wyświetlane na poniższej ilustracji:

 ![Relacja między migawki kopii zapasowej dziennika transakcji i dublowanie przystawki na osi czasu](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Aby osiągnąć jeszcze lepszy cel punktu odzyskiwania w przypadku odzyskiwania po awarii, możesz skopiować kopie zapasowe dziennika transakcji HANA z platformą SAP HANA na platformie Azure (duże wystąpienia) do innego regionu platformy Azure. Aby osiągnąć ten dalsze obniżenie cel punktu odzyskiwania, należy wykonać następujące czynności:

1. Tworzenie kopii zapasowych transakcji HANA dziennika tak często, jak to tylko możliwe /hana/logbackups.
1. Aby skopiować kopie zapasowe dziennika transakcji systemu plików NFS hostowanych udziału maszyn wirtualnych platformy Azure, należy użyć polecenia rsync. Maszyny wirtualne znajdują się w sieciach wirtualnych platformy Azure w regionie platformy Azure środowiska produkcyjnego i regionu odzyskiwania po awarii. Należy połączyć obie sieci wirtualne platformy Azure z obwodem łączenia dużych wystąpień HANA w środowisku produkcyjnym na platformie Azure. Zobacz grafikę w [sieci zagadnienia dotyczące odzyskiwania po awarii przy użyciu dużych wystąpień HANA](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) sekcji. 
1. Zachowaj kopie zapasowe dziennika transakcji w regionie na maszynie wirtualnej jest dołączony do systemu plików NFS wyeksportowane magazynu.
1. W przypadku trybu failover po awarii należy uzupełnić kopie zapasowe dziennika transakcji, dostępne na woluminie /hana/logbackups więcej ostatnio wykonane kopie zapasowe dziennika transakcji na systemu plików NFS udostępniania w lokacji odzyskiwania po awarii. 
1. Uruchom kopię zapasową dziennika transakcji w celu przywrócenia najnowszej kopii zapasowej, który może zostać zapisany w regionie odzyskiwania po awarii.

Podczas operacji dużych wystąpień HANA upewnij się, Instalator relacji replikacji i Rozpocznij kopie zapasowe migawek magazynu wykonywania, rozpoczyna się replikacja danych.

![Krok instalacji odzyskiwania po awarii przed nawiązaniem replikacji](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Zgodnie z postępów replikacji migawek na woluminach PRD w regionach Azure odzyskiwania po awarii nie są przywracane. Tylko są one przechowywane. Jeśli woluminy są instalowane w taki stan, stanowią one stan, w którym możesz odinstalować tych woluminów po zainstalowaniu wystąpienie PRD SAP HANA w jednostce serwera w regionie odzyskiwania po awarii platformy Azure. Reprezentują one magazynu kopii zapasowych, które nie zostały jeszcze przywrócone.

W przypadku przejścia w tryb failover, również można przywrócić starsze migawkę pamięci masowej zamiast najnowszą migawkę pamięci masowej.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [procedurę trybu failover odzyskiwania po awarii](hana-failover-procedure.md).
