---
title: Uruchom DR Przechodzenie do szczegółów maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchomić wyszczególniania odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V w chmurach VMM do dodatkowego centrum danych przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: ponatara
ms.openlocfilehash: c389776f62db5fd04f67ef22822e21fd4aee368f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Uruchom wyszczególniania odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej


W tym artykule opisano sposób wykonywania wyszczególniania odzyskiwania (DR) po awarii dla maszyn wirtualnych funkcji Hyper-V, które są zarządzane w chmurach System Center Virtual Machine Manager V(MM), do lokacji dodatkowej lokalnymi przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

Uruchom test trybu failover, aby zweryfikować swoją strategię replikacji, a następnie wykonaj wyszczególniania odzyskiwania po awarii, bez utraty danych lub przestoju. Test trybu failover nie ma żadnego wpływu na trwającej replikacji lub w środowisku produkcyjnym. 

## <a name="how-do-test-failovers-work"></a>Jak przetestować tryb failover pracy?

Możesz uruchomić test trybu failover z serwera podstawowego do lokacji dodatkowej. Jeśli po prostu chcesz sprawdzić, czy przełącza Maszynę wirtualną, możesz uruchomić test trybu failover bez konfigurowania żadnych czynności w lokacji dodatkowej. Jeśli chcesz sprawdzić aplikacja trybu failover działa zgodnie z oczekiwaniami, należy skonfigurować sieci i infrastruktury w lokalizacji dodatkowej.
- Można uruchomić testowy tryb failover na jednej maszynie Wirtualnej lub na [planu odzyskiwania](site-recovery-create-recovery-plans.md).
- Test trybu failover bez sieci, można uruchomić z istniejącej sieci lub z siecią automatycznie utworzone. Więcej informacji o tych opcjach znajdują się w poniższej tabeli.
    - Możesz uruchomić test trybu failover bez sieci. Ta opcja jest przydatna, jeśli po prostu chcesz sprawdzić, czy maszyna wirtualna była w stanie pracy awaryjnej, ale nie będzie mógł zweryfikować żadnej konfiguracji sieci.
    - Uruchom pracę awaryjną z istniejącą siecią. Firma Microsoft zaleca się, że nie używasz środowiska produkcyjnego.
    - Uruchom tryb failover i pozwól Site Recovery automatycznie twórz sieci testowej. W takim przypadku Usługa Site Recovery automatycznie twórz sieci i wyczyścić po zakończeniu testowania trybu failover.
- Należy wybrać punkt odzyskiwania do testowania trybu failover: 
    - **Najnowsze przetworzone**: Ta opcja awaryjnie Maszynę wirtualną do ostatniego punktu odzyskiwania przetworzone przez usługę Site Recovery. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
    - **Najnowsza wersja aplikacji spójne**: Ta opcja pracy awaryjnej Maszynę wirtualną do punktu najnowszych odzyskiwania zapewniających spójność aplikacji przetworzone przez usługę Site Recovery. 
    - **Najnowsze**: tę opcję, najpierw przetwarza wszystkie dane, które zostało wysłane do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny Wirtualnej przed jej awarii. Ta opcja umożliwia najniższy RPO (cel punktu odzyskiwania), ponieważ maszyna wirtualna utworzona po trybu failover ma wszystkie dane, które są replikowane do usługi Site Recovery pracę w trybie failover zostało wyzwolone.
    - **Najnowsze wielu maszyn wirtualnych przetwarzane**: dostępne dla planów odzyskiwania, które obejmują co najmniej jeden maszyn wirtualnych, które mają włączoną spójnością wielu maszyn wirtualnych. Maszyny wirtualne z ustawieniem włączone przełączyć najnowsze wspólnych punktów odzyskiwania zapewniających spójność wielu maszyn wirtualnych. Innych maszyn wirtualnych w trybie Failover do najnowszego punktu odzyskiwania przetworzone.
    - **Najnowsze wielu maszyn wirtualnych całej aplikacji**: Ta opcja jest dostępna dla planów odzyskiwania z przynajmniej jednej maszyny wirtualnej, które mają włączoną spójnością wielu maszyn wirtualnych. Maszyny wirtualne, które są częścią grupy replikacji w trybie Failover do najnowszego punktu odzyskiwania zapewniających spójność aplikacji wspólnej wielu maszyn wirtualnych. Pozostałe maszyny wirtualne przełączone awaryjnie do ich najnowszy punkt odzyskiwania zapewniających spójność aplikacji.
    - **Niestandardowe**: Użyj tej opcji do określonej maszyny Wirtualnej do określonego punktu w tryb failover.



## <a name="prepare-networking"></a>Przygotowanie sieci

Po uruchomieniu test trybu failover, użytkownik jest proszony o wybierz ustawienia sieci dla maszyny repliki testu, zgodnie z opisem w tabeli.

**Opcja** | **Szczegóły** 
--- | --- 
**Brak** | Testowej maszyny Wirtualnej jest tworzona na hoście, na którym znajduje się repliki maszyn wirtualnych. Nie jest dodawany do chmury, a nie jest połączona z żadną siecią.<br/><br/> Komputer można połączyć z siecią maszyny Wirtualnej, po jego utworzeniu.
**Użyj istniejących** | Testowej maszyny Wirtualnej jest tworzona na hoście, na którym znajduje się repliki maszyn wirtualnych. Nie jest dodawany do chmury.<br/><br/>Utwórz sieć maszyny Wirtualnej, która jest odizolowana od produkcyjnego środowiska sieciowego.<br/><br/>Jeśli używasz sieci opartej na sieci VLAN, zalecamy utworzenie oddzielnych sieci logicznej (nie używane w środowisku produkcyjnym) w programie VMM w tym celu. Ta sieć logiczna jest używana do tworzenia sieci maszyny Wirtualnej dla testu pracy w trybie Failover.<br/><br/>Sieć logiczna powinna być skojarzona z co najmniej jedną z kart sieciowych wszystkich serwerów funkcji Hyper-V, które obsługują maszyny wirtualne.<br/><br/>Dla sieci logicznych VLAN powinna zostać odizolowana Lokacje sieciowe, które dodajesz do sieci logicznej.<br/><br/>Jeśli używasz sieć logiczną na podstawie wirtualizacja sieci systemu Windows Azure Site Recovery automatycznie tworzy izolowane sieci maszyny Wirtualnej. 
**Tworzenie sieci** | Sieci testowej tymczasowy jest tworzony automatycznie zgodnie z ustawieniem w **sieci logicznej** i jej lokacji związane z siecią.<br/><br/> Tryb failover sprawdza, czy maszyny wirtualne są tworzone. |Należy używać tej opcji, jeśli plan odzyskiwania korzysta z więcej niż jedną sieć maszyny Wirtualnej.<br/><br/> Jeśli używasz wirtualizacji sieci systemu Windows, ta opcja może automatycznie tworzyć sieci maszyn wirtualnych z tymi samymi ustawieniami (podsieci i pule adresów IP) w sieci maszyny wirtualnej repliki. Te sieci maszyn wirtualnych są automatycznie czyszczone po zakończeniu testowania trybu failover.<br/><br/> Test maszyny Wirtualnej jest tworzony na hoście, na którym znajduje się maszyna wirtualna repliki. Nie jest dodawany do chmury.

### <a name="best-practices"></a>Najlepsze praktyki

- Testowanie sieci produkcyjnej powoduje, że czas przestoju w przypadku obciążeń produkcyjnych. Poproś nie użytkownikom korzystanie z aplikacji pokrewnych, gdy trwa wyszczególniania odzyskiwania po awarii.

- Sieci testowej nie musi być zgodny typ sieci logicznej VMM, które są używane do testowania trybu failover. Jednak niektóre kombinacje nie działają:

     - Jeśli replika używa protokołu DHCP i izolacji opartej na sieci VLAN, sieć maszyny Wirtualnej repliki nie wymaga puli statycznych adresów IP. Do testowania trybu failover przy użyciu wirtualizacja sieci systemu Windows nie będzie działać, ponieważ nie są dostępne żadne pule adresów. 
        
     - Testowanie trybu failover nie będzie działać, jeśli sieć repliki używa bez izolacji, a wirtualizacja sieci systemu Windows używa sieci testowej. Jest to spowodowane Brak izolacji sieci nie ma podsieci, wymagane do utworzenia sieci wirtualizacja sieci systemu Windows.
        
- Zaleca się, że wybrana sieć nie jest używany do mapowania sieci do testowania trybu failover.

- Jak maszyny wirtualne repliki są podłączone do mapowanej sieci maszyn wirtualnych po pracy awaryjnej zależy od konfiguracji sieci maszyny Wirtualnej w konsoli programu VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Sieć maszyny Wirtualnej skonfigurowaną bez izolacji i izolacji sieci VLAN

Jeśli sieć wirtualna jest skonfigurowany w programie VMM bez izolacji i izolacji sieci VLAN, należy uwzględnić następujące informacje:

- DHCP jest zdefiniowana dla sieci maszyny Wirtualnej, maszyna wirtualna repliki jest podłączony do Identyfikatora sieci VLAN za pomocą ustawień, które są określone dla lokacji sieciowej w sieci logiczne skojarzone. Maszyna wirtualna otrzymuje adres IP z dostępnego serwera DHCP.
- Nie trzeba definiować puli statycznych adresów IP sieci docelowej maszyny Wirtualnej. Użycie puli statycznych adresów IP dla sieci maszyny Wirtualnej maszyny wirtualnej repliki jest podłączony do Identyfikatora sieci VLAN za pomocą ustawień, które są określone dla lokacji sieciowej w sieci logiczne skojarzone.
- Maszyna wirtualna otrzymuje adres IP z puli, która jest zdefiniowana dla sieci maszyny Wirtualnej. Jeśli puli statycznych adresów IP nie jest zdefiniowana w sieci docelowej maszyny Wirtualnej, przydzielanie adresów IP zakończy się niepowodzeniem. Utwórz pulę adresów IP na serwerach VMM źródłowych i docelowych, które będą używane do ochrony i odzyskiwania.

### <a name="vm-network-with-windows-network-virtualization"></a>Sieci Vmnetwork z wirtualizacją sieci systemu Windows

Jeśli sieć wirtualna jest skonfigurowany w programie VMM z wirtualizacją sieci systemu Windows, należy uwzględnić następujące informacje:

- Należy zdefiniować pulę statycznych dla docelowej sieci maszyny Wirtualnej, niezależnie od tego, czy źródłowej sieci maszyny Wirtualnej jest skonfigurowana do używania protokołu DHCP lub statyczna pula adresów IP. 
- Po zdefiniowaniu DHCP docelowym serwerze VMM działa jako serwer DHCP i udostępnia adres IP z puli, która jest zdefiniowana w sieci docelowej maszyny Wirtualnej.
- Jeśli korzystanie z puli statycznych adresów IP jest zdefiniowana na serwerze źródłowym, docelowym serwerze VMM przydziela adres IP z puli. W obu przypadkach przydzielanie adresów IP zakończy się niepowodzeniem, jeśli nie zdefiniowano puli statycznych adresów IP.



## <a name="prepare-the-infrastructure"></a>Przygotowywanie infrastruktury

Jeśli po prostu chcesz sprawdzić, czy maszyna wirtualna może przełączyć, możesz uruchomić test trybu failover bez infrastruktury. Jeśli chcesz wykonać pełne wyszczególniania odzyskiwania po awarii do testowania aplikacji w tryb failover, należy przygotować infrastruktury w lokacji dodatkowej:

- Jeśli możesz uruchomić test trybu failover za pomocą istniejącej sieci, przygotuj usługi Active Directory, DHCP i DNS w sieci.
- Po uruchomieniu test trybu failover umożliwia automatyczne tworzenie sieci maszyny Wirtualnej, należy dodać zasoby infrastruktury automatycznie utworzone sieci, przed uruchomieniem testowania trybu failover. W planie odzyskiwania można ułatwić to przez dodanie to krok wykonywany ręcznie przed grupy 1 w planie odzyskiwania, który będzie używać do testowania trybu failover. Następnie należy dodać zasoby infrastruktury automatycznie utworzone sieci przed uruchomieniem testowania trybu failover.


### <a name="prepare-dhcp"></a>Przygotowanie DHCP
W przypadku maszyn wirtualnych testowy tryb failover korzystać z protokołu DHCP, Utwórz test serwera DHCP w sieci izolowanej na potrzeby testowania trybu failover.


### <a name="prepare-active-directory"></a>Przygotowanie usługi Active Directory
Aby uruchomić test trybu failover do testowania aplikacji, należy kopię w środowisku produkcyjnym usługi Active Directory w środowisku testowym. Aby uzyskać więcej informacji, przejrzyj [test pracy awaryjnej zagadnienia dotyczące usługi Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Przygotowanie DNS
Przygotuj serwer DNS do testowania trybu failover w następujący sposób:

* **DHCP**: maszyn wirtualnych, użycie protokołu DHCP, adres IP testu DNS należy zaktualizować na serwerze DHCP testu. Jeśli używasz sieci typu wirtualizacja sieci systemu Windows, serwer VMM działa jako serwer DHCP. Adres IP serwera DNS powinna zostać uaktualnione w testowej sieci trybu failover. W przypadku maszyn wirtualnych rejestrują się do odpowiedniego serwera DNS.
* **Statyczny adres**: maszyny wirtualnej użycie statycznego adresu IP, adres IP serwera DNS testu powinien zostać zaktualizowany w testowej sieci trybu failover. Konieczne może być zaktualizować DNS przy użyciu adresu IP maszyn wirtualnych testu. Poniższy przykładowy skrypt można użyć w tym celu:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Ta procedura opisuje sposób testować tryb failover planu odzyskiwania. Alternatywnie można uruchomić trybu failover dla jednej maszyny wirtualnej na **maszyn wirtualnych** kartę.

1. Wybierz **plany odzyskiwania** > *recoveryplan_name*. Kliknij przycisk **pracy awaryjnej** > **testowanie trybu Failover**.
2. Na **testowego trybu Failover** bloku, określ, jak repliki maszyn wirtualnych powinny być połączone z sieciami po testowania trybu failover.
3. Śledzić postęp trybu failover na **zadania** kartę.
4. Po zakończeniu trybu failover Sprawdź pomyślnie uruchomić maszyn wirtualnych.
5. Gdy wszystko będzie gotowe, kliknij przycisk **oczyszczania testowy tryb failover** dla planu odzyskiwania. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem pracy w trybie failover. Ten krok polega na usunięciu wszelkich maszyn wirtualnych i sieci, które zostały utworzone przez usługę Site Recovery podczas testowego trybu failover. 

![Testowanie trybu failover](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> Adres IP podany dla maszyny wirtualnej podczas testowania trybu failover jest ten sam adres IP, który otrzyma maszyny wirtualnej dla planowanego lub nieplanowanego trybu failover (przy założeniu, że adres IP jest dostępny w testowej sieci trybu failover). Jeśli ten sam adres IP nie jest dostępny w testowej sieci trybu failover, maszyna wirtualna odbiera innego adresu IP, który jest dostępny w testowej sieci trybu failover.



### <a name="run-a-test-failover-to-a-production-network"></a>Uruchom test trybu failover do środowiska produkcyjnego

Zaleca się, że do sieci produkcyjnej odzyskiwania lokacji, które zostały określone podczas mapowania sieci, nie uruchamiaj test trybu failover. Jednak jeśli należy dokonać weryfikacji łączności sieciowej na całej trasie na maszynie wirtualnej przełączona w tryb failover, weź pod uwagę następujące kwestie:

* Upewnij się, że podstawowej maszyny Wirtualnej jest zamknięta podczas testowania trybu failover podczas wykonywania. Jeśli nie zostanie dwóch maszyn wirtualnych o tej samej tożsamości będzie uruchomiony w tej samej sieci, w tym samym czasie. Taka sytuacja może spowodować niepożądane skutki.
* Wszelkie zmiany wprowadzone do testowania trybu failover maszyny wirtualne zostaną utracone po wyczyść testowe maszyny wirtualne w tryb failover. Te zmiany nie są replikowane do głównej maszyn wirtualnych.
* Testowanie takie prowadzi do przestojów w przypadku aplikacji produkcyjnej. Poproś użytkowników aplikacji, nie należy używać aplikacji, gdy Trwa przechodzenie do szczegółów odzyskiwania po awarii.  


## <a name="next-steps"></a>Kolejne kroki
Po pomyślnym uruchomieniu wyszczególniania odzyskiwania po awarii, możesz [pełne tryb failover](site-recovery-failover.md).



