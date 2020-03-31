---
title: Aktualizacje i uaktualnienia składników w usłudze Azure Site Recovery
description: Zawiera omówienie aktualizacji usługi Azure Site Recovery i uaktualnień składników.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257825"
---
# <a name="service-updates-in-site-recovery"></a>Aktualizacje usługi w odzyskiwaniu witryn

W tym artykule przedstawiono omówienie aktualizacji [usługi Azure Site Recovery](site-recovery-overview.md) i opisano sposób uaktualniania składników usługi Site Recovery.

Usługa Site Recovery regularnie publikuje aktualizacje usług. Aktualizacje obejmują nowe funkcje, ulepszenia pomocy technicznej, aktualizacje składników i poprawki błędów. Aby korzystać z najnowszych funkcji i poprawek, zalecamy uruchomienie najnowszych wersji składników usługi Site Recovery. 
 
 
## <a name="updates-support"></a>Obsługa aktualizacji

### <a name="support-statement-for-azure-site-recovery"></a>Instrukcja pomocy technicznej dla usługi Azure Site Recovery

Zalecamy zawsze uaktualnianie do najnowszych wersji komponentów:

**Z każdą nową wersją 'N' składnika usługi Azure Site Recovery, który został wydany, wszystkie wersje poniżej "N-4" są uważane za niewsporcie.** 

> [!IMPORTANT]
> Oficjalna pomoc techniczna jest dla aktualizacji z > wersji N-4 do wersji N. Na przykład, jeśli korzystasz z N-6, musisz najpierw uaktualnić do N-4, a następnie uaktualnić do N.


### <a name="links-to-currently-supported-update-rollups"></a>Łącza do aktualnie obsługiwanych pakietów zbiorczych aktualizacji

 Przejrzyj najnowszy pakiet zbiorczy aktualizacji (wersja N) w [tym artykule](site-recovery-whats-new.md). Należy pamiętać, że odzysk witryny zapewnia obsługę wersji N-4.



## <a name="component-expiry"></a>Wygaśnięcie składnika

Usługa Site Recovery powiadamia użytkownika o wygasłych składnikach (lub zbliżających się wygaśnięciu) pocztą e-mail (jeśli zasubskrybujesz powiadomienia e-mail) lub na pulpicie nawigacyjnym magazynu w portalu.

- Ponadto gdy aktualizacje są dostępne, w widoku infrastruktury dla scenariusza w portalu obok składnika pojawi się przycisk **Aktualizuj dostępne.** Ten przycisk przekierowuje do łącza do pobrania najnowszej wersji składnika.
-  Powiadomienia pulpitu nawigacyjnego przechowalni nie są dostępne w przypadku replikowania maszyn wirtualnych funkcji Hyper V. 

Powiadomienia e-mail są wysyłane w następujący sposób.

**Czas** | **Częstotliwości**
--- | ---
60 dni przed wygaśnięciem składnika | Raz dwa razy w tygodniu
Następne 53 dni | Raz w tygodniu
Ostatnie 7 dni | raz dziennie
Po wygaśnięciu | Raz dwa razy w tygodniu


### <a name="upgrading-outside-official-support"></a>Modernizacja poza oficjalnym wsparciem

Jeśli różnica między wersją składnika a najnowszą wersją jest większa niż cztery, jest to uważane za niew pomocy technicznej. W takim przypadku uaktualnij w następujący sposób: 

1. Uaktualnienie aktualnie zainstalowanego składnika do bieżącej wersji plus cztery. Na przykład jeśli wersja, jeśli 9.16, a następnie uaktualnić do 9.20.
2. Następnie uaktualnij do następnej zgodnej wersji. Tak więc w naszym przykładzie, po uaktualnieniu 9.16 do 9.20, uaktualnienie do 9.24. 

Postępuj zgodnie z tym samym procesem dla wszystkich odpowiednich składników.

### <a name="support-for-latest-operating-systemskernels"></a>Obsługa najnowszych systemów operacyjnych/jąder

> [!NOTE]
> Jeśli masz zaplanowane okno konserwacji i znajduje się w nim ponowne uruchomienie, zaleca się najpierw uaktualnienie składników odzyskiwania witryny, a następnie kontynuowanie pozostałych zaplanowanych działań w oknie konserwacji.

1. Przed uaktualnieniem wersji systemu operacyjnego/jądra sprawdź, czy wersja docelowa jest obsługiwana przez odzyskiwanie witryny. 

    - Obsługa [maszyny wirtualnej platformy Azure.](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)
    - [Obsługa serwerów VMware/fizycznego](vmware-physical-azure-support-matrix.md#replicated-machines)
    - Obsługa [funkcji Hyper-V.](hyper-v-azure-support-matrix.md#replicated-vms)
2. Przejrzyj [dostępne aktualizacje,](site-recovery-whats-new.md) aby dowiedzieć się, co chcesz uaktualnić.
3. Uaktualnienie do najnowszej wersji usługi Site Recovery.
4. Uaktualnij system operacyjny/jądro do wymaganych wersji.
5. Ponownie obuwać.


Ten proces gwarantuje, że system operacyjny komputera/jądro zostanie uaktualniony do najnowszej wersji i że najnowsze zmiany w układzie odzyskiwania lokacji potrzebne do obsługi nowej wersji są ładowane na komputerze.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Odzyskiwanie po awarii maszyn wirtualnych platformy Azure na platformę Azure

W tym scenariuszu zdecydowanie zaleca się [włączenie aktualizacji automatycznych](azure-to-azure-autoupdate.md). Można zezwolić funkcji Site Recovery na zarządzanie aktualizacjami w następujący sposób:

- Podczas procesu włączania replikacji.
- Ustawiając ustawienia aktualizacji rozszerzenia wewnątrz przechowalni.

Jeśli chcesz ręcznie zarządzać aktualizacjami, wykonaj następujące czynności:

1. W przechowalni > **elementy replikowane**kliknij to powiadomienie u góry ekranu: 
    
    **Dostępna jest nowa aktualizacja agenta replikacji odzyskiwania witryny. Kliknij, aby zainstalować ->**

4. Wybierz maszyny wirtualne, dla których chcesz zastosować aktualizację, a następnie kliknij przycisk **OK**.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Odzyskiwanie po awarii vmware VMware VM/server physical server na platformie Azure

1. Na podstawie bieżącej wersji i [instrukcji pomocy technicznej](#support-statement-for-azure-site-recovery)należy najpierw zainstalować aktualizację na lokalnym serwerze konfiguracyjnym, korzystając z tych [instrukcji](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). 
2. Jeśli masz serwery procesów skalowanych w poziomie, zaktualizuj je dalej, korzystając z [tych instrukcji](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Aby zaktualizować agenta mobilności na każdym chronionym komputerze, zapoznaj się z [tym](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) artykułem.

### <a name="reboot-after-mobility-service-upgrade"></a>Ponowne uruchomienie po uaktualnieniu usługi mobilności

Po każdym uaktualnieniu usługi mobilności zaleca się ponowne uruchomienie komputera, aby upewnić się, że wszystkie najnowsze zmiany są ładowane na komputerze źródłowym.

Ponowne uruchomienie nie jest obowiązkowe, chyba że różnica między wersją agenta podczas ostatniego ponownego uruchomienia a bieżącą wersją jest większa niż cztery.

W przykładzie w tabeli pokazano, jak to działa.

|**Wersja agenta (ostatni restart)** | **Uaktualnienie do** | **Obowiązkowy restart?**|
|---------|---------|---------|
|9.16 |  9.18 | Nieojasze|
|9.16 | 9.19 | Nieojasze|
| 9.16 | 9.20 | Nieojasze
 | 9.16 | 9.21 | Obowiązkowy.<br/><br/> Uaktualnij do 9.20, a następnie uruchom ponownie przed uaktualnieniem do 9.21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Odzyskiwanie po awarii maszyn wirtualnych funkcji Hyper-V na platformę Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Między witryną funkcji Hyper-V a platformą Azure

1. Pobierz aktualizację dla dostawcy odzyskiwania witryny platformy Microsoft Azure.
2. Zainstaluj dostawcę na każdym serwerze funkcji Hyper-V zarejestrowanym w programie Site Recovery. Jeśli używasz klastra, uaktualnij wszystkie węzły klastra.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Między lokalną lokacją programu VMM a platformą Azure
1. Pobierz aktualizację dla dostawcy odzyskiwania witryny platformy Microsoft Azure.
2. Zainstaluj dostawcę na serwerze programu VMM. Jeśli program VMM jest wdrożony w klastrze, zainstaluj dostawcę we wszystkich węzłach klastra.
3. Zainstaluj najnowszy agent usług odzyskiwania platformy Microsoft Azure we wszystkich hostach funkcji Hyper-V lub węzłach klastra.


## <a name="between-two-on-premises-vmm-sites"></a>Między dwoma lokalnymi obiektami VMM
1. Pobierz najnowszą aktualizację dostawcy odzyskiwania witryny platformy Microsoft Azure.
2. Zainstaluj najnowszego dostawcę na serwerze programu VMM zarządzającego dodatkową lokacją odzyskiwania. Jeśli program VMM jest wdrożony w klastrze, zainstaluj dostawcę we wszystkich węzłach klastra.
3. Po zaktualizowaniu lokacji odzyskiwania zainstaluj dostawcę na serwerze programu VMM, który zarządza lokacją główną.

## <a name="next-steps"></a>Następne kroki

Śledź naszą stronę [Aktualizacje platformy Azure,](https://azure.microsoft.com/updates/?product=site-recovery) aby śledzić nowe aktualizacje i wydania.
