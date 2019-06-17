---
title: Uruchamianie próbnego odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchamianie próbnego odzyskiwania po awarii dla maszyn wirtualnych z funkcją Hyper-V w chmurach programu VMM do dodatkowego lokalnego centrum danych przy użyciu usługi Azure Site Recovery.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: dc8deb16f7d124c5fb11568f25050eee99a245b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60865534"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Uruchamianie próbnego odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej


W tym artykule opisano sposób przeprowadzania odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V, które są zarządzane w chmurach System Center Virtual Machine Manager V(MM) do lokacji dodatkowej w środowisku lokalnym, za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).

Możesz uruchomić testowy tryb failover, aby zweryfikować działanie strategii replikacji oraz przeprowadzać odzyskiwania po awarii bez przestoju lub utraty danych. Testowanie trybu failover nie ma żadnego wpływu na trwającą replikację lub w środowisku produkcyjnym. 

## <a name="how-do-test-failovers-work"></a>Jak przetestować pracy przejścia w tryb failover?

Możesz uruchomić test trybu failover z węzła podstawowego do lokacji dodatkowej. Jeśli po prostu chcesz sprawdzić, czy maszyny Wirtualnej w trybie Failover, można uruchomić test trybu failover bez konfigurowania niczego w lokacji dodatkowej. Aby Sprawdź, czy aplikacja trybu failover działa zgodnie z oczekiwaniami, należy skonfigurować sieci i infrastruktury w dodatkowej lokalizacji.
- Można uruchomić testowy tryb failover na jednej maszynie Wirtualnej lub na [planu odzyskiwania](site-recovery-create-recovery-plans.md).
- Testowanie trybu failover bez sieci, można uruchomić z istniejącej sieci lub z siecią automatycznie utworzone. Więcej informacji na temat tych opcji znajdują się w poniższej tabeli.
    - Możesz uruchomić test trybu failover bez sieci. Ta opcja jest przydatna, jeśli chcesz po prostu zaznacz Maszynę wirtualną można było w trybie Failover, że nie będzie można sprawdzić żadnej konfiguracji sieci.
    - Uruchom tryb failover przy użyciu istniejącej sieci. Zaleca się, że nie używasz produkcyjnego środowiska sieciowego.
    - Uruchamianie trybu failover i pozwól Site Recovery automatycznie twórz sieci testowej. W tym przypadku Usługa Site Recovery automatycznie twórz sieci i czyszczenie po zakończeniu testowania trybu failover.
- Należy wybrać punkt odzyskiwania do testowania trybu failover: 
    - **Najnowszy przetworzony**: Ta opcja wprowadza Maszynę wirtualną za pośrednictwem do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
    - **Najnowszy spójny na poziomie aplikacji**: Ta opcja tryb failover maszyny Wirtualnej w celu najnowszy spójnych z aplikacją punkt przywracania przetworzone przez usługę Site Recovery. 
    - **Najnowszy**: Ta opcja najpierw przetwarza wszystkie dane, które została wysłana do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny Wirtualnej przed przechodzenie w tryb failover do niego. Ta opcja zapewnia najniższy cel punktu odzyskiwania (cel punktu odzyskiwania), ponieważ maszyna wirtualna utworzona po pracy awaryjnej, będzie miał wszystkie dane, które są replikowane do usługi Site Recovery podczas pracy w trybie failover zostało wyzwolone.
    - **Najnowsze wieloma Maszynami wirtualnymi przetwarzane**: Dostępne dla plany odzyskiwania obejmujące co najmniej jeden maszyn wirtualnych, które mają włączoną spójnością wielu maszyn wirtualnych. Maszyny wirtualne z włączone ustawienie przełączyć w tryb failover do najnowszego punktu odzyskiwania spójnego na poziomie wielu maszyn wirtualnych wspólnej. Inne maszyny wirtualne przełączyć w tryb failover do najnowszego punktu odzyskiwania przetworzonego.
    - **Najnowsze wieloma Maszynami wirtualnymi spójny na poziomie aplikacji**: Ta opcja jest dostępna w przypadku planów odzyskiwania z co najmniej jeden maszyn wirtualnych, które mają włączoną spójnością wielu maszyn wirtualnych. Maszyny wirtualne, które są częścią grupy replikacji przełączyć w tryb failover do najnowszego punktu odzyskiwania spójnego z aplikacją wspólnej wielu maszyn wirtualnych. Inne maszyny wirtualne Failover ich najnowszego punktu odzyskiwania spójnego na poziomie aplikacji.
    - **Niestandardowy**: Użyj tej opcji do określonej maszyny Wirtualnej do określonego punktu odzyskiwania w trybie Failover.



## <a name="prepare-networking"></a>Przygotuj sieci

Po uruchomieniu testu trybu failover, zostanie wyświetlony monit wybierz ustawienia sieci dla maszyny repliki testu, zgodnie z opisem w tabeli.

| **Option** | **Szczegóły** | |
| --- | --- | --- |
| **Brak** | Testowej maszyny Wirtualnej jest tworzony na hoście, na którym znajduje się maszyna wirtualna repliki. Nie została dodana do chmury, a nie jest połączona z żadną siecią.<br/><br/> Komputer można połączyć z siecią maszyny Wirtualnej, po jego utworzeniu.| |
| **Użyj istniejącej** | Testowej maszyny Wirtualnej jest tworzony na hoście, na którym znajduje się maszyna wirtualna repliki. Nie jest dodawany do chmury.<br/><br/>Utwórz sieć maszyny Wirtualnej, która jest odizolowana od sieci środowiska produkcyjnego.<br/><br/>Jeśli używasz sieci VLAN, zalecamy utworzenie oddzielnych sieci logiczne (ale nie używane w środowisku produkcyjnym) w programie VMM w tym celu. Ta sieć logiczna jest używana do tworzenia sieci maszyn wirtualnych dla testu pracy w trybie Failover.<br/><br/>Sieć logiczna powinna być skojarzona z co najmniej jedną z kart sieciowych w procentach wszystkich serwerów funkcji Hyper-V, które hostują maszyny wirtualne.<br/><br/>Dla sieci VLAN sieci logicznych lokacji sieciowych, które dodajesz do sieci logicznej powinna być odizolowane.<br/><br/>Jeśli używasz wirtualizacji sieci Windows oparte na sieci logicznej, usługi Azure Site Recovery automatycznie tworzy izolowanych sieci maszyn wirtualnych. | |
| **Tworzenie sieci** | Sieci testowej tymczasowy jest tworzony automatycznie w oparciu o ustawienia, którą określasz w **sieci logicznej** i jej lokacji związane z siecią.<br/><br/> Tryb failover sprawdza, czy maszyny wirtualne są tworzone.<br/><br/> Tej opcji należy używać, jeśli więcej niż jedną sieć maszyny Wirtualnej korzysta z planu odzyskiwania.<br/><br/> Jeśli używasz wirtualizacji sieci Windows ta opcja może automatycznie tworzyć sieci maszyn wirtualnych z tymi samymi ustawieniami (podsieci i pule adresów IP) w sieci maszyny wirtualnej repliki. Te sieci maszyn wirtualnych są automatycznie czyszczone po zakończeniu testowania trybu failover.<br/><br/> Test maszyna wirtualna jest tworzona na hoście, na którym znajduje się maszyna wirtualna repliki. Nie jest dodawany do chmury.|

### <a name="best-practices"></a>Najlepsze praktyki

- Testowanie produkcyjnego środowiska sieciowego powoduje, że przestój w przypadku obciążeń produkcyjnych. Poproś użytkowników nie należy używać pokrewnych aplikacji, gdy trwa próbnego odzyskiwania po awarii.

- W sieci testowej nie muszą być zgodne typ sieci logicznej program VMM, które są używane do testowania trybu failover. Ale nie działają niektóre kombinacje:

     - Jeśli replika używa protokołu DHCP i izolacji opartej na sieci VLAN, sieci maszyny Wirtualnej repliki nie wymaga puli statycznych adresów IP. Za pomocą wirtualizacji sieci Windows do testowania trybu failover nie będą działać, ponieważ nie są dostępne żadne pule adresów. 
        
     - Testowanie trybu failover nie będą działać, jeśli sieć repliki używa bez izolacji, a Windows wirtualizacji sieci korzysta z sieci testowej. Jest to spowodowane sieć bez izolacji, nie ma podsieci, wymagane do utworzenia sieci Windows wirtualizacji sieci.
        
- Zaleca się, że wybrane sieci nie jest używany do mapowania sieci, na potrzeby testowania trybu failover.

- Jak maszyny wirtualne repliki są podłączone do sieci maszyn wirtualnych zamapowanych po pracy awaryjnej jest zależna od konfiguracji sieci maszyny Wirtualnej w konsoli programu VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Sieć maszyn wirtualnych skonfigurowaną bez izolacji i izolacji sieci VLAN

Jeśli sieć maszyny Wirtualnej jest skonfigurowany w programie VMM bez izolacji i izolacji sieci VLAN, pamiętaj o następujących kwestiach:

- Jeżeli DHCP jest zdefiniowana dla sieci maszyny Wirtualnej, maszyna wirtualna repliki jest podłączony do Identyfikatora sieci VLAN za pomocą ustawień, które są określone dla lokacji sieciowych w skojarzona sieć logiczna. Maszyna wirtualna odbiera adresu IP z dostępnego serwera DHCP.
- Nie ma potrzeby definiowania puli statycznych adresów IP dla sieci maszyn wirtualnych docelowej. Puli statycznych adresów IP jest używany dla sieci maszyny Wirtualnej, maszyna wirtualna repliki jest podłączony do Identyfikatora sieci VLAN za pomocą ustawień, które są określone dla lokacji sieciowych w skojarzona sieć logiczna.
- Maszyna wirtualna otrzymuje adres IP z puli, która jest zdefiniowana dla sieci maszyny Wirtualnej. Puli statycznych adresów IP nie jest zdefiniowana w sieci docelowej maszyny Wirtualnej, przydzielanie adresów IP zakończy się niepowodzeniem. Utwórz pulę adresów IP na serwerach VMM źródłowego i docelowego, które będą używane w celu ochrony i odzyskiwania.

### <a name="vm-network-with-windows-network-virtualization"></a>Sieć Vmnetwork z wirtualizacją sieci Windows

Jeśli sieć maszyny Wirtualnej jest skonfigurowana w programie VMM z wirtualizacją sieci Windows, pamiętaj o następujących kwestiach:

- Należy zdefiniować pulę statycznych dla sieci maszyn wirtualnych docelowej niezależnie od tego, czy źródłowej sieci maszyny Wirtualnej jest skonfigurowany do używania protokołu DHCP lub puli statycznych adresów IP. 
- Jeśli zdefiniujesz DHCP, serwer programu VMM docelowy działa jako serwer DHCP i udostępnia adres IP z puli, która jest zdefiniowana dla sieci maszyn wirtualnych docelowej.
- Jeśli nie zdefiniowano korzystanie z puli statycznych adresów IP na serwerze źródłowym, docelowym serwerze VMM przydziela adresu IP z puli. W obu przypadkach przydzielanie adresów IP zakończy się niepowodzeniem, jeśli nie zdefiniowano puli statycznych adresów IP.



## <a name="prepare-the-infrastructure"></a>Przygotowywanie infrastruktury

Jeśli po prostu chcesz sprawdzić, czy maszyny Wirtualnej w trybie Failover, możesz uruchomić test trybu failover bez infrastruktury. Chcesz zrobić pełnego odzyskiwania po awarii do testowania aplikacji w tryb failover, należy najpierw przygotować infrastrukturę w lokacji dodatkowej:

- Po uruchomieniu testu trybu failover przy użyciu istniejącej sieci, należy przygotować usługi Active Directory, DHCP i DNS w tej sieci.
- Po uruchomieniu testu trybu failover przy użyciu opcji, aby automatycznie utworzyć sieć maszyn wirtualnych, należy dodać zasoby infrastruktury siecią automatycznie utworzone przed uruchomieniem testu trybu failover. W planie odzyskiwania można to ułatwić, dodając krok wykonywany ręcznie przed 1 grupy w ramach planu odzyskiwania, który będzie używać do testowania trybu failover. Następnie należy dodać zasoby infrastruktury w automatycznie utworzonej sieci przed uruchomieniem testu trybu failover.


### <a name="prepare-dhcp"></a>Przygotowanie DHCP
W przypadku maszyn wirtualnych testowy tryb failover użyć protokołu DHCP, Utwórz test serwera DHCP w sieci izolowanej na potrzeby testowania trybu failover.


### <a name="prepare-active-directory"></a>Prepare Active Directory
Aby uruchomić testowy tryb failover do testowania aplikacji, potrzebujesz kopii środowiska produkcyjnego w usłudze Active Directory w środowisku testowym. Aby uzyskać więcej informacji, zobacz [testowanie trybu failover zagadnienia dotyczące usługi Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Przygotowanie DNS
Przygotuj serwer DNS do testowania trybu failover w następujący sposób:

* **DHCP**: Jeśli maszyny wirtualne używać protokołu DHCP, adres IP test DNS powinien zostać zaktualizowany na serwerze DHCP testu. Jeśli używasz typu sieci wirtualizacji sieci Windows, serwer programu VMM działa jako serwer DHCP. W związku z tym adres IP serwera DNS powinien zostać zaktualizowany w testowej sieci trybu failover. W przypadku maszyn wirtualnych, rejestrują się do odpowiedniego serwera DNS.
* **Statyczny adres**: Jeśli maszyny wirtualne używać statycznego adresu IP, adres IP serwera DNS, test powinien zostać zaktualizowany w testowej sieci trybu failover. Może być konieczne aktualizowanie systemu DNS przy użyciu adresu IP testowe maszyny wirtualne. W tym celu, można użyć następującego przykładowego skryptu:

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

Ta procedura opisuje sposób testować tryb failover planu odzyskiwania. Alternatywnie można uruchomić tryb failover dla pojedynczej maszyny wirtualnej na **maszyn wirtualnych** kartę.

1. Wybierz **plany odzyskiwania** > *recoveryplan_name*. Kliknij przycisk **trybu Failover** > **testowanie trybu Failover**.
2. Na **testu pracy awaryjnej** bloku, określ, jak maszyn wirtualnych repliki powinny być połączone z sieciami po testowy tryb failover.
3. Śledzić postęp trybu failover na **zadań** kartę.
4. Po zakończeniu trybu failover Sprawdź pomyślnie uruchomić maszyny wirtualne.
5. Gdy wszystko będzie gotowe, kliknij przycisk **wyczyść test pracy awaryjnej** w planie odzyskiwania. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem pracy w trybie failover. Ten krok usuwa wszelkie maszyny wirtualne i sieci, które zostały utworzone przez usługę Site Recovery podczas testowania trybu failover. 

![Testowanie pracy w trybie failover](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> Adres IP, biorąc pod uwagę na maszynę wirtualną podczas testowania trybu failover jest ten sam adres IP, który będzie otrzymywać maszyny wirtualnej dla planowanego lub nieplanowanego przejścia w tryb failover (przy założeniu, że adres IP jest dostępny w testowej sieci trybu failover). Jeśli ten sam adres IP nie jest dostępny w testowej sieci trybu failover, maszyna wirtualna otrzyma inny adres IP, który jest dostępny w testowej sieci trybu failover.



### <a name="run-a-test-failover-to-a-production-network"></a>Uruchom testowanie trybu failover do produkcyjnego środowiska sieciowego

Zaleca się testowanie trybu failover nie uruchamiać się sieci lokacji odzyskiwania środowiska produkcyjnego, które zostały określone podczas mapowania sieci. Ale jeśli potrzebujesz do weryfikowania łączności sieciowej end-to-end na maszynie wirtualnej w trybie Failover, pamiętaj o następujących kwestiach:

* Upewnij się, czy podstawowa maszyna wirtualna jest wyłączona, gdy wykonujesz test trybu failover. Jeśli nie, dwie maszyny wirtualne z tą samą tożsamością będzie działać w tej samej sieci w tym samym czasie. Taka sytuacja może spowodować niepożądane skutki.
* Wszelkie zmiany wprowadzone do testowania trybu failover maszyny wirtualne zostaną utracone podczas czyszczenia testowe maszyny wirtualne w tryb failover. Te zmiany nie są replikowane do główne maszyny wirtualne.
* Testowanie następująco prowadzi do przerwy w działaniu aplikacji produkcyjnych. Poproś użytkowników aplikacji, nie należy używać aplikacji, podczas testowania odzyskiwania po awarii jest w toku.  


## <a name="next-steps"></a>Kolejne kroki
Po pomyślnym uruchomieniu testowania odzyskiwania po awarii, możesz [Uruchamianie pełnego trybu failover](site-recovery-failover.md).



