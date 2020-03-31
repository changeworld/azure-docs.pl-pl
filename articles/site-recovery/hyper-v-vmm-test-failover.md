---
title: Uruchamianie wiertła odzyskiwania po awarii NHyper-V w lokacji dodatkowej za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak uruchomić drążek odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do pomocniczego lokalnego centrum danych przy użyciu usługi Azure Site Recovery.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 0363911574a076b13cb72591fb2564364e096c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257968"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Uruchamianie wiertła odzyskiwania po awarii dla maszyn wirtualnych z programem Hyper-V w lokacji dodatkowej


W tym artykule opisano, jak wykonać drążek odzyskiwania po awarii (DR) dla maszyn wirtualnych funkcji Hyper-V zarządzanych w chmurach menedżera maszyn wirtualnych V(MM) w centrum systemu do dodatkowej lokacji lokalnej przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

Uruchom test pracy awaryjnej, aby sprawdzić poprawność strategii replikacji i wykonać dreder odzyskiwania po awarii bez utraty danych lub przestojów. Test pracy awaryjnej nie ma żadnego wpływu na trwającą replikację lub na środowisko produkcyjne. 

## <a name="how-do-test-failovers-work"></a>Jak działają testy awaryjne?

Uruchom test pracy awaryjnej z lokacji podstawowej do dodatkowej. Jeśli chcesz po prostu sprawdzić, czy maszyna wirtualna kończy się niepowodzeniem, można uruchomić test pracy awaryjnej bez konfigurowania niczego w lokacji dodatkowej. Jeśli chcesz sprawdzić, czy praca awaryjna aplikacji działa zgodnie z oczekiwaniami, musisz skonfigurować sieć i infrastrukturę w lokalizacji dodatkowej.
- Test można uruchomić w trybie failover na jednej maszynie wirtualnej lub na [planie odzyskiwania.](site-recovery-create-recovery-plans.md)
- Test można uruchomić bez pracy awaryjnej bez sieci, istniejącej sieci lub automatycznie utworzonej sieci. Więcej informacji na temat tych opcji znajduje się w poniższej tabeli.
    - Można uruchomić test pracy awaryjnej bez sieci. Ta opcja jest przydatna, jeśli chcesz po prostu sprawdzić, czy maszyna wirtualna mogła awaryjnie, ale nie będzie można zweryfikować żadnej konfiguracji sieci.
    - Uruchom przewijamy w błąd z istniejącą siecią. Zalecamy, aby nie korzystać z sieci produkcyjnej.
    - Uruchom opcję pracy awaryjnej i pozwól odzyskiwaniu lokacji automatycznie utworzyć sieć testową. W takim przypadku usługa Site Recovery automatycznie utworzy sieć i oczyści ją po zakończeniu pracy awaryjnej testu.
- Należy wybrać punkt odzyskiwania dla pracy awaryjnej testu: 
    - **Najnowsze przetworzone:** Ta opcja kończy się niepowodzeniem maszyny Wirtualnej do najnowszego punktu odzyskiwania przetwarzanego przez odzyskiwanie witryny. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
    - **Najnowsze spójne z aplikacjami:** Ta opcja przełącz się w grę maszynę wirtualną do najnowszego punktu odzyskiwania spójnego z aplikacją przetwarzanego przez odzyskiwanie witryny. 
    - **Najnowsze:** Ta opcja najpierw przetwarza wszystkie dane, które zostały wysłane do usługi site recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny Wirtualnej przed przepełnieniem jej po awarii. Ta opcja zapewnia najniższy cel punktu odzyskiwania (Cel punktu odzyskiwania), ponieważ maszyna wirtualna utworzona po przełączeniu w błąd będzie miała wszystkie dane replikowane do usługi Site Recovery po wyzwoleniu pracy awaryjnej.
    - **Najnowsze przetwarzane maszyny wielowęzłowe:** Dostępne dla planów odzyskiwania, które zawierają co najmniej jedną maszynę wirtualną z włączoną spójnością wielu maszyn wirtualnych. Maszyny wirtualne z włączoną włączoną funkcją fail over do najnowszego wspólnego wspólnego punktu odzyskiwania spójnego multi-VM. Inne maszyny wirtualne w pracy awaryjnej do ostatniego przetworzonego punktu odzyskiwania.
    - **Najnowsze aplikacje z wieloma maszynami wirtualnymi spójne:** Ta opcja jest dostępna dla planów odzyskiwania z co najmniej jedną maszyną wirtualną, która ma włączoną spójność wielu maszyn wirtualnych. Maszyny wirtualne, które są częścią grupy replikacji w trybie fail over do najnowszego wspólnego wspólnego punktu odzyskiwania spójne aplikacji wielu maszyn wirtualnych. Inne maszyny wirtualne w pracy awaryjnej do ich ostatni punkt odzyskiwania spójne aplikacji.
    - **Niestandardowe:** Użyj tej opcji, aby przewinąć w stan faila dla określonej maszyny Wirtualnej do określonego punktu odzyskiwania.



## <a name="prepare-networking"></a>Przygotowanie sieci

Po uruchomieniu testowego trybu failover zostaniesz poproszony o wybranie ustawień sieciowych dla testowych maszyn repliki, zgodnie z podsumowaniem w tabeli.

| **Opcja** | **Szczegóły** | |
| --- | --- | --- |
| **Brak** | Testowa maszyna wirtualna jest tworzona na hoście, na którym znajduje się replika maszyny Wirtualnej. Nie jest dodawany do chmury i nie jest podłączony do żadnej sieci.<br/><br/> Po utworzeniu urządzenia można podłączyć urządzenie do sieci maszyn wirtualnych.| |
| **Użyj istniejących** | Testowa maszyna wirtualna jest tworzona na hoście, na którym znajduje się replika maszyny Wirtualnej. Nie jest dodawany do chmury.<br/><br/>Utwórz sieć maszyn wirtualnych, która jest odizolowana od sieci produkcyjnej.<br/><br/>Jeśli używasz sieci opartej na sieci VLAN, zaleca się utworzenie oddzielnej sieci logicznej (nie używanej w produkcji) w programu VMM w tym celu. Ta sieć logiczna służy do tworzenia sieci maszyn wirtualnych dla testów pracy awaryjnej.<br/><br/>Sieć logiczna powinna być skojarzona z co najmniej jedną z kart sieciowych wszystkich serwerów funkcji Hyper-V obsługujących maszyny wirtualne.<br/><br/>W przypadku sieci logicznych sieci VLAN witryny sieciowe dodawanych do sieci logicznej powinny być izolowane.<br/><br/>Jeśli używasz sieci logicznej opartej na wirtualizacji sieci systemu Windows, usługa Azure Site Recovery automatycznie tworzy izolowane sieci maszyn wirtualnych. | |
| **Tworzenie sieci** | Tymczasowa sieć testowa jest tworzona automatycznie na podstawie ustawienia określonego w **sieci logicznej** i powiązanych lokacjach sieciowych.<br/><br/> Sprawdzanie trybu failover, że maszyny wirtualne są tworzone.<br/><br/> Tej opcji należy użyć, jeśli plan odzyskiwania używa więcej niż jednej sieci maszyn wirtualnych.<br/><br/> Jeśli używasz sieci wirtualizacji sieci systemu Windows, ta opcja może automatycznie tworzyć sieci maszyn wirtualnych z tymi samymi ustawieniami (podsieci i pule adresów IP) w sieci repliki maszyny wirtualnej. Te sieci maszyn wirtualnych są czyszczone automatycznie po zakończeniu pracy awaryjnej testu.<br/><br/> Testowa maszyna wirtualna jest tworzona na hoście, na którym istnieje maszyna wirtualna repliki. Nie jest dodawany do chmury.|

### <a name="best-practices"></a>Najlepsze rozwiązania

- Testowanie sieci produkcyjnej powoduje przestoje dla obciążeń produkcyjnych. Poproś użytkowników, aby nie używali powiązanych aplikacji, gdy trwa ćwiczenie odzyskiwania po awarii.

- Sieć testowa nie musi być zgodna z typem sieci logicznej programu VMM używanym do testowania trybu failover. Ale niektóre kombinacje nie działają:

     - Jeśli replika używa izolacji opartej na dhcp i sieci VLAN, sieć maszyn wirtualnych repliki nie potrzebuje statycznej puli adresów IP. Dlatego przy użyciu wirtualizacji sieci systemu Windows dla pracy awaryjnej testu nie będzie działać, ponieważ nie są dostępne żadne pule adresów. 
        
     - Testowanie trybu failover nie będzie działać, jeśli sieć repliki nie używa izolacji, a sieć testowa używa wirtualizacji sieci systemu Windows. Dzieje się tak, ponieważ sieć bez izolacji nie ma podsieci wymaganych do utworzenia sieci wirtualizacji sieci windows.
        
- Zaleca się, aby nie używać sieci wybranej do mapowania sieci, do testowania trybu failover.

- Sposób, w jaki maszyny wirtualne repliki są połączone z mapowanymi sieciami maszyn wirtualnych po przełączeniu w tryb failover, zależy od konfiguracji sieci maszyn wirtualnych w konsoli programu VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Sieć maszyn wirtualnych skonfigurowana bez izolacji lub izolacji sieci VLAN

Jeśli sieć maszyn wirtualnych jest skonfigurowana w programie VMM bez izolacji lub izolacji sieci VLAN, należy zwrócić uwagę na następujące kwestie:

- Jeśli usługa DHCP jest zdefiniowana dla sieci maszyn wirtualnych maszyn wirtualnych, maszyna wirtualna repliki jest podłączona do identyfikatora sieci VLAN za pomocą ustawień określonych dla lokacji sieciowej w skojarzonej sieci logicznej. Maszyna wirtualna odbiera swój adres IP z dostępnego serwera DHCP.
- Nie trzeba definiować statycznej puli adresów IP dla docelowej sieci maszyn wirtualnych. Jeśli dla sieci maszyn wirtualnych jest używana statyczna pula adresów IP, maszyna wirtualna repliki jest podłączona do identyfikatora sieci VLAN za pomocą ustawień określonych dla lokacji sieciowej w skojarzonej sieci logicznej.
- Maszyna wirtualna odbiera swój adres IP z puli zdefiniowanej dla sieci maszyn wirtualnych. Jeśli statyczna pula adresów IP nie jest zdefiniowana w docelowej sieci maszyn wirtualnych, alokacja adresów IP zakończy się niepowodzeniem. Utwórz pulę adresów IP zarówno na źródłowych, jak i docelowych serwerach programu VMM, które będą używane do ochrony i odzyskiwania.

### <a name="vm-network-with-windows-network-virtualization"></a>Sieć maszyn wirtualnych z wirtualizacją sieci systemu Windows

Jeśli sieć maszyn wirtualnych jest skonfigurowana w programie VMM z wirtualizacją sieci systemu Windows, należy zwrócić uwagę na następujące kwestie:

- Należy zdefiniować pulę statyczną dla docelowej sieci maszyn wirtualnych, niezależnie od tego, czy źródłowa sieć maszyn wirtualnych jest skonfigurowana do używania dhcp czy statycznej puli adresów IP. 
- W przypadku definiowania usługi DHCP docelowy serwer programu VMM działa jako serwer DHCP i udostępnia adres IP z puli zdefiniowanej dla docelowej sieci maszyn wirtualnych.
- Jeśli dla serwera źródłowego zdefiniowano użycie statycznej puli adresów IP, docelowy serwer programu VMM przydziela adres IP z puli. W obu przypadkach alokacja adresów IP zakończy się niepowodzeniem, jeśli nie zdefiniowano statycznej puli adresów IP.



## <a name="prepare-the-infrastructure"></a>Przygotowywanie infrastruktury

Jeśli chcesz po prostu sprawdzić, czy maszyna wirtualna może awaryjnie, można uruchomić test pracy awaryjnej bez infrastruktury. Jeśli chcesz wykonać pełną drążan odzyskiwania po awarii, aby przetestować tryb failover aplikacji, musisz przygotować infrastrukturę w lokacji dodatkowej:

- Jeśli test pracy awaryjnej jest uruchamiany przy użyciu istniejącej sieci, należy przygotować usługę Active Directory, DHCP i DNS w tej sieci.
- Jeśli zostanie uruchomiony test pracy awaryjnej z opcją automatycznego utworzenia sieci maszyn wirtualnych, należy dodać zasoby infrastruktury do automatycznie utworzonej sieci, przed uruchomieniem testu pracy awaryjnej. W planie odzyskiwania można to ułatwić, dodając ręczny krok przed grupą-1 w planie odzyskiwania, który będzie używany do pracy awaryjnej testu. Następnie dodaj zasoby infrastruktury do automatycznie utworzonej sieci przed uruchomieniem testowego trybu failover.


### <a name="prepare-dhcp"></a>Przygotowanie usługi DHCP
Jeśli maszyny wirtualne biorące udział w testowym trybie failover używają usługi DHCP, utwórz testowy serwer DHCP w izolowanej sieci w celu przetestowania trybu failover.


### <a name="prepare-active-directory"></a>Przygotowywanie usługi Active Directory
Aby uruchomić test pracy awaryjnej do testowania aplikacji, należy wykonać kopię środowiska usługi Active Directory w środowisku testowym. Aby uzyskać więcej informacji, zapoznaj się z [zagadnieniami dotyczących pracy awaryjnej testu dla usługi Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Przygotowanie systemu DNS
Przygotuj serwer DNS do pracy awaryjnej testu w następujący sposób:

* **DHCP**: Jeśli maszyny wirtualne używają usługi DHCP, adres IP testowego dns powinien zostać zaktualizowany na testowym serwerze DHCP. Jeśli używasz typu sieci wirtualizacji sieci systemu Windows, serwer programu VMM działa jako serwer DHCP. W związku z tym adres IP systemu DNS powinny być aktualizowane w sieci trybu failover testu. W takim przypadku maszyny wirtualne rejestrują się na odpowiednim serwerze DNS.
* **Adres statyczny:** Jeśli maszyny wirtualne używają statycznego adresu IP, adres IP testowego serwera DNS powinien zostać zaktualizowany w testowej sieci trybu failover. Może być konieczne zaktualizowanie systemu DNS przy przywiązywanie adresu IP testowych maszyn wirtualnych. W tym celu można użyć następującego przykładowego skryptu:

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

W tej procedurze opisano sposób uruchamiania testowego trybu failover dla planu odzyskiwania. Alternatywnie można uruchomić tryb failover dla pojedynczej maszyny wirtualnej na karcie **Maszyny wirtualne.**

1. Wybierz*recoveryplan_name* **plany** > odzyskiwania . Kliknij **pozycję Testowanie pracy** > **awaryjnej w pracy awaryjnej**.
2. W **bloku testowania trybu failover** określ, jak maszyny wirtualne repliki powinny być połączone z sieciami po przejściu testów w błąd.
3. Śledzenie postępu pracy awaryjnej na karcie **Zadania.**
4. Po zakończeniu pracy awaryjnej sprawdź, czy maszyny wirtualne uruchamiają się pomyślnie.
5. Po zakończeniu kliknij przycisk **Oczyszczanie testu pracy awaryjnej** w planie odzyskiwania. W **uwagach**należy rejestrować i zapisywać wszystkie obserwacje związane z testem w wersji failover. Ten krok usuwa wszystkie maszyny wirtualne i sieci, które zostały utworzone przez odzyskiwanie witryny podczas testowania pracy awaryjnej. 

![Testowanie pracy w trybie failover](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> Adres IP podany maszynie wirtualnej podczas testowania trybu failover jest tym samym adresem IP, który maszyna wirtualna otrzyma dla planowanej lub nieplanowanej pracy awaryjnej (przypuszczając, że adres IP jest dostępny w sieci trybu failover testu). Jeśli ten sam adres IP nie jest dostępny w testowej sieci trybu failover, maszyna wirtualna odbiera inny adres IP, który jest dostępny w testowej sieci trybu failover.



### <a name="run-a-test-failover-to-a-production-network"></a>Uruchamianie testowego trybu failover w sieci produkcyjnej

Zaleca się, aby nie uruchamiać testowego trybu failover w sieci lokacji odzyskiwania produkcji, który został określony podczas mapowania sieci. Jeśli jednak konieczne jest sprawdzenie poprawności kompleksowej łączności sieciowej na nieudanej maszynie wirtualnej, należy zwrócić uwagę na następujące kwestie:

* Upewnij się, że podstawowa maszyna wirtualna jest zamykana podczas wykonywania pracy awaryjnej testu. Jeśli nie, dwie maszyny wirtualne o tej samej tożsamości będą uruchomione w tej samej sieci w tym samym czasie. Sytuacja ta może prowadzić do niepożądanych konsekwencji.
* Wszelkie zmiany wprowadzone w testowych maszynach wirtualnych trybu failover są tracone podczas czyszczenia maszyn wirtualnych trybu failover testu. Te zmiany nie są replikowane z powrotem do podstawowych maszyn wirtualnych.
* Testowanie w ten sposób prowadzi do przestoju dla aplikacji produkcyjnej. Poproś użytkowników aplikacji, aby nie używali aplikacji, gdy dreder odzyskiwania po awarii jest w toku.  


## <a name="next-steps"></a>Następne kroki
Po pomyślnym uruchomieniu wiertła odzyskiwania po awarii można [uruchomić pełną tryb failover](site-recovery-failover.md).



