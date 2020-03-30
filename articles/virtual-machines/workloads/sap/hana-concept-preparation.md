---
title: Zasady odzyskiwania po awarii i przygotowanie na platformie SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Zasady odzyskiwania po awarii i przygotowania na sap HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d52f871de75a7f7d34016b040e44d6f1623fd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101259"
---
# <a name="disaster-recovery-principles"></a>Zasady odzyskiwania po awarii

Hana duże wystąpienia oferują funkcję odzyskiwania po awarii między sygnaturami dużych wystąpień HANA w różnych regionach platformy Azure. Na przykład jeśli wdrożysz hana jednostek wystąpienia dużych w regionie West usa platformy Azure, można użyć jednostek dużych wystąpień HANA w regionie wschodnich stanów USA jako jednostek odzyskiwania po awarii. Jak wspomniano wcześniej, odzyskiwanie po awarii nie jest konfigurowany automatycznie, ponieważ wymaga zapłaty za inną jednostkę dużych wystąpień HANA w regionie odzyskiwania po awarii. Konfiguracja odzyskiwania po awarii działa w przypadku konfiguracji skalowania w górę i skalowania w poziomie. 

W scenariuszach wdrożonych do tej pory klienci używają jednostki w regionie odzyskiwania po awarii do uruchamiania systemów nieprodukcyjnych, które używają zainstalowanego wystąpienia HANA. Jednostka dużych wystąpień HANA musi mieć taką samą jednostkę SKU, jak jednostka SKU używana do celów produkcyjnych. Na poniższej ilustracji przedstawiono, jak wygląda konfiguracja dysku między jednostką serwera w regionie produkcji platformy Azure a regionem odzyskiwania po awarii:

![Konfiguracja konfiguracji odzyskiwania po awarii z punktu widzenia dysku](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Jak pokazano na tej grafice przeglądowej, należy zamówić drugi zestaw woluminów dyskowych. Woluminy dysku docelowego mają taki sam rozmiar jak woluminy produkcyjne dla wystąpienia produkcji w jednostkach odzyskiwania po awarii. Te woluminy dysków są skojarzone z jednostką serwera dużych wystąpień HANA w lokacji odzyskiwania po awarii. Następujące woluminy są replikowane z regionu produkcji do lokacji odzyskiwania po awarii:

- /hana/dane
- /hana/logbackups 
- /hana/shared (w tym /usr/sap)

Wolumin /hana/log nie jest replikowany, ponieważ dziennik transakcji SAP HANA nie jest potrzebny w taki sposób, że przywracanie z tych woluminów jest wykonywane. 

Podstawą funkcji odzyskiwania po awarii oferowanych jest funkcjonalność replikacji magazynu oferowanych przez infrastrukturę dużych wystąpień HANA. Funkcja, która jest używana po stronie magazynu nie jest stały strumień zmian, które replikują się w sposób asynchroniczne, jak zmiany się do woluminu magazynu. Zamiast tego jest mechanizm, który opiera się na fakcie, że migawki tych woluminów są tworzone regularnie. Delta między już replikowaną migawką a nową migawką, która nie została jeszcze zreplikowana, jest następnie przesyłana do lokacji odzyskiwania po awarii na woluminy dysków docelowych.  Te migawki są przechowywane na woluminach i, jeśli istnieje po awarii odzyskiwania po awarii, muszą zostać przywrócone na tych woluminach.  

Pierwszy transfer pełnych danych woluminu powinien być przed ilość danych staje się mniejsza niż różnice między migawkami. W rezultacie woluminy w lokacji odzyskiwania po awarii zawierają wszystkie migawki woluminów wykonywane w lokacji produkcyjnej. Ostatecznie można użyć tego systemu odzyskiwania po awarii, aby uzyskać wcześniejszy stan, aby odzyskać utracone dane, bez wycofywania systemu produkcyjnego.

Jeśli istnieje wdrożenie MCOD z wieloma niezależnymi wystąpieniami SAP HANA na jednej jednostce dużych wystąpień HANA, oczekuje się, że wszystkie wystąpienia SAP HANA są coraz magazynu replikowane po stronie odzyskiwania po awarii.

W przypadkach, gdy używasz replikacji systemu HANA jako funkcji o wysokiej dostępności w lokacji produkcyjnej i używasz replikacji opartej na magazynie dla lokacji odzyskiwania po awarii, woluminy obu węzłów z lokacji głównej do wystąpienia odzyskiwania po awarii są replikowane. Aby uwzględnić replikację z podstawowej i pomocniczej do odzyskiwania po awarii, należy zakupić dodatkowy magazyn (taki sam rozmiar jak węzeł podstawowy) w lokacji odzyskiwania po awarii. 



>[!NOTE]
>Funkcja replikacji magazynu dużych wystąpień HANA jest dublowanie i replikowanie migawek magazynu. Jeśli nie wykonujesz migawek magazynu wprowadzonych w sekcji Kopia zapasowa i przywracanie w tym artykule, nie może być żadnej replikacji do lokacji odzyskiwania po awarii. Wykonanie migawki magazynu jest warunkiem wstępnym replikacji magazynu do lokacji odzyskiwania po awarii.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Przygotowanie scenariusza odzyskiwania po awarii
W tym scenariuszu masz system produkcyjny uruchomiony na hana dużych wystąpień w regionie platformy Azure produkcji. Aby wykonać kroki, załóżmy, że identyfikator SID tego systemu HANA jest "PRD" i że masz system nieprodukcyjny uruchomiony na hana dużych wystąpień w regionie platformy Azure dr. Dla tego ostatniego załóżmy, że jego identyfikator SID to "TST". Na poniższej ilustracji przedstawiono następującą konfigurację:

![Początek konfiguracji odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Jeśli wystąpienie serwera nie zostało jeszcze zamówione z dodatkowym zestawem woluminów magazynu, sap HANA w usłudze Azure Service Management dołącza dodatkowy zestaw woluminów jako miejsce docelowe repliki produkcyjnej do jednostki dużego wystąpienia HANA, na której jest uruchomiony tst Wystąpienie HANA. W tym celu należy podać identyfikator SID wystąpienia HANA produkcji. Po SAP HANA na usługi Azure Service Management potwierdza załącznik tych woluminów, należy zainstalować te woluminy do jednostki dużych wystąpień HANA.

![Konfiguracja odzyskiwania po awarii w następnym kroku](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Następnym krokiem jest zainstalowanie drugiego wystąpienia SAP HANA na jednostce dużych wystąpień HANA w regionie platformy DR Azure, gdzie uruchomisz wystąpienie TST HANA. Nowo zainstalowane wystąpienie SAP HANA musi mieć ten sam identyfikator SID. Utworzono użytkowników muszą mieć ten sam identyfikator UID i identyfikator grupy, który ma wystąpienie produkcji. Przeczytaj [kopię zapasową i przywróć, aby](hana-backup-restore.md) uzyskać szczegółowe informacje. Jeśli instalacja powiodła się, należy:

- Wykonaj krok 2 przygotowania migawki magazynu opisane w [kopii zapasowej i przywracania](hana-backup-restore.md).
- Utwórz klucz publiczny dla jednostki odzyskiwania po awarii jednostki dużych wystąpień HANA, jeśli jeszcze tego nie zrobiono. Zobacz krok 3 przygotowania migawki magazynu opisany w [sekcji Kopia zapasowa i przywracanie](hana-backup-restore.md).
- Obsługa *HANABackupCustomerDetails.txt* z nowym wystąpieniem HANA i sprawdź, czy łączność z magazynem działa poprawnie.  
- Zatrzymaj nowo zainstalowane wystąpienie SAP HANA na jednostce dużych wystąpień HANA w regionie platformy DR Azure.
- Odinstaluj te woluminy PRD i skontaktuj się z sap HANA w usłudze Azure Service Management. Woluminy nie mogą pozostać zainstalowane w urządzeniu, ponieważ nie mogą być dostępne podczas działania jako miejsce docelowe replikacji magazynu.  

![Krok konfiguracji odzyskiwania po awarii przed ustanowieniem replikacji](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Zespół operacyjny ustanawia relację replikacji między woluminami PRD w regionie platformy Azure produkcji i woluminów PRD w regionie platformy DR Azure.

>[!IMPORTANT]
>Wolumin /hana/log nie jest replikowany, ponieważ nie jest konieczne przywrócenie zreplikowanej bazy danych SAP HANA do spójnego stanu w lokacji odzyskiwania po awarii.

Następnie skonfiguruj lub dostosuj harmonogram tworzenia kopii zapasowych migawki magazynu, aby uzyskać pomoc w celu uzyskania ochrony konta RTO i celu odzyskiwania w przypadku awarii. Aby zminimalizować cel punktu odzyskiwania, ustaw następujące interwały replikacji w usłudze dużych wystąpień HANA:
- Dla woluminów objętych połączone migawki (typ migawki **hana),** zestaw do replikacji co 15 minut do równoważnych obiektów docelowych woluminu magazynu w lokacji odzyskiwania po awarii.
- Dla woluminu kopii zapasowej dziennika transakcji **(dzienniki**typu migawki), ustawiona na replikowanie co 3 minuty do równoważnych obiektów docelowych woluminu magazynu w lokacji odzyskiwania po awarii.

Aby zminimalizować cel punktu odzyskiwania, należy skonfigurować następujące elementy:
- Wykonywanie migawki magazynu typu **hana** (zobacz "Krok 7: Wykonywanie migawek") co 30 minut do 1 godziny.
- Wykonywanie kopii zapasowych dziennika transakcji SAP HANA co 5 minut.
- Wykonywanie migawki magazynu typu **dzienniki** co 5-15 minut. W tym okresie interwału osiągasz cel ochrony cel około 15-25 minut.

W tej konfiguracji sekwencja kopii zapasowych dziennika transakcji, migawek magazynu i replikacja woluminu kopii zapasowej dziennika transakcji HANA i /hana/data i /hana/shared (w tym /usr/sap) może wyglądać jak dane pokazane na tej grafice:

 ![Relacja między migawką kopii zapasowej dziennika transakcji a lustrem przyciągania na osi czasu](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Aby osiągnąć jeszcze lepszy cel ochrony środowiska w przypadku odzyskiwania po awarii, można skopiować kopie zapasowe dziennika transakcji HANA z sap HANA na platformie Azure (duże wystąpienia) do innego regionu platformy Azure. Aby osiągnąć dalszą redukcję RPO, wykonaj następujące kroki:

1. Łącze zapasowe dziennika transakcji HANA tak często, jak to możliwe do /hana/logbackups.
1. Użyj rsync, aby skopiować kopie zapasowe dziennika transakcji do maszyn wirtualnych platformy Azure hostowanych przez usługę NFS. Maszyny wirtualne znajdują się w sieciach wirtualnych platformy Azure w regionie produkcji platformy Azure i w regionach odzyskiwania po awarii. Należy połączyć obie sieci wirtualne platformy Azure z obwodem łączącym produkcyjne wystąpienia duże HANA z platformą Azure. Zobacz grafiki w [sieci zagadnienia odzyskiwania po awarii z HANA dużych wystąpień](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) sekcji. 
1. Zachowaj kopie zapasowe dziennika transakcji w regionie na maszynie wirtualnej dołączonej do magazynu eksportowanego przez nfs.
1. W przypadku awarii awaryjnej uzupełnij kopie zapasowe dziennika transakcji, które można znaleźć na woluminie /hana/logbackups, z niedawno pobranymi kopiami zapasowymi dziennika transakcji w udziale NFS w lokacji odzyskiwania po awarii. 
1. Uruchom kopię zapasową dziennika transakcji, aby przywrócić najnowszą kopię zapasową, która może zostać zapisana w regionie odzyskiwania po awarii.

Gdy operacje dużych wystąpień HANA potwierdzają konfigurację relacji replikacji i uruchamiasz kopie zapasowe migawki magazynu wykonywania, rozpoczyna się replikacja danych.

![Krok konfiguracji odzyskiwania po awarii przed ustanowieniem replikacji](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

W miarę postępu replikacji migawki na woluminach PRD w regionach platformy dr platformy Azure nie są przywracane. Są one przechowywane tylko. Jeśli woluminy są zainstalowane w takim stanie, reprezentują one stan, w którym odinstalowane te woluminy po wystąpieniu PRD SAP HANA został zainstalowany w jednostce serwera w regionie platformy Dr Azure. Reprezentują one również kopie zapasowe magazynu, które nie zostały jeszcze przywrócone.

Jeśli istnieje przerój stanu failover, można również przywrócić do starszej migawki magazynu zamiast najnowszej migawki magazynu.

## <a name="next-steps"></a>Następne kroki

- Poleć [Procedura pracy awaryjnej odzyskiwania po awarii](hana-failover-procedure.md).
