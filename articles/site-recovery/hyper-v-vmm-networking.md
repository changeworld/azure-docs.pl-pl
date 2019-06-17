---
title: Konfigurowanie adresów IP, odnoszący się do połączenia z lokacji dodatkowej w środowisku lokalnym po pracy awaryjnej przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania adresów IP na potrzeby nawiązywania połączenia z maszynami wirtualnymi w lokacji dodatkowej w środowisku lokalnym po odzyskiwania po awarii i trybu failover za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 8e4dca61016adce209bdce356ea4280fee525c05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66397962"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Konfigurowanie adresów IP adresowania połączyć po włączeniu trybu failover do lokacji dodatkowej w środowisku lokalnym

Po przełączeniu w tryb failover maszyn wirtualnych z funkcją Hyper-V w chmurach programu System Center Virtual Machine Manager (VMM) do lokacji dodatkowej muszą być w stanie nawiązać połączenie z maszyn wirtualnych repliki. Ten artykuł ułatwia w tym celu. 

## <a name="connection-options"></a>Opcje połączenia

Po przejściu w tryb failover istnieje kilka sposobów, aby obsłużyć adresowania IP dla maszyn wirtualnych repliki: 

- **Zachować ten sam adres IP, po włączeniu trybu failover**: W tym scenariuszu zreplikowanej maszyny Wirtualnej ma ten sam adres IP jako podstawowej maszyny Wirtualnej. Upraszcza to związany z siecią, problemy po włączeniu trybu failover, ale wymaga pewnej pracy infrastruktury.
- **Użyj innego adresu IP po włączeniu trybu failover**: W tym scenariuszu maszyna wirtualna pobiera nowego adresu IP po włączeniu trybu failover. 
 

## <a name="retain-the-ip-address"></a>Zachować adres IP

Jeśli chcesz zachować adresy IP z lokacji głównej, po włączeniu trybu failover do lokacji dodatkowej, możesz wykonywać następujące czynności:

- Wdróż rozciągnięta podsieci między główną lokacji dodatkowych.
- Wykonaj pełną podsieci przejścia w tryb failover z serwera podstawowego do lokacji dodatkowej. Musisz zaktualizować trasy, aby wskazać lokalizację nowych adresów IP.


### <a name="deploy-a-stretched-subnet"></a>Wdrażanie rozciągnięta podsieci

W rozproszonym konfiguracji podsieci jest dostępna jednocześnie w lokacjach głównych i dodatkowych. W rozproszonym podsieci podczas przenoszenia maszyny i jej konfiguracja adresu IP (warstwa 3) do lokacji dodatkowej sieci automatycznie kieruje ruch do nowej lokalizacji. 

- Z punktu widzenia warstwy 2 (warstwę łącza danych) należy urządzeń sieciowych, która może zarządzać rozproszonych sieci VLAN.
- Rozciągając sieci VLAN, potencjalne domeny błędów obejmuje zarówno witryn. Ten staje się pojedynczym punktem awarii. Podczas, gdy jest to mało prawdopodobne, w tym scenariuszu nie można wyizolować incydentu, takich jak storm emisji. 


### <a name="fail-over-a-subnet"></a>W trybie Failover podsieć

W całej podsieci, aby czerpać korzyści z rozproszonym podsieci bez faktycznego rozciągając go może zakończyć się niepowodzeniem. W tym rozwiązaniu podsieci jest dostępna w lokacji źródłowej lub docelowej, ale nie oba jednocześnie.

- Aby zachować przestrzeń adresów IP w przypadku pracy awaryjnej, można programowo rozmieścić infrastruktury routera przenieść podsieci z jednej lokacji.
- W przypadku przejścia w tryb failover podsieci przejściem ich skojarzonych maszynach wirtualnych.
- Główną wadą tego podejścia jest to, że w przypadku awarii, należy przenieść całej podsieci.

#### <a name="example"></a>Przykład

Oto przykład pełną podsieci przejścia w tryb failover. 

- Przed włączeniem trybu failover lokacja główna ma aplikacje działające na platformie 192.168.1.0/24 podsieci.
- Podczas pracy w trybie failover wszystkich maszyn wirtualnych w tej podsieci są przełączone w tryb failover w lokacji dodatkowej i Zachowaj ich adresów IP. 
- Trasy między wszystkimi lokacjami muszą zostać zmodyfikowane w celu odzwierciedlenia fakt, że wszystkie maszyny wirtualne w podsieci 192.168.1.0/24 zostały przeniesione do lokacji dodatkowej.

Na poniższej grafice podsieci przed i po włączeniu trybu failover.


**Przed włączeniem trybu failover**

![Przed włączeniem trybu failover](./media/hyper-v-vmm-networking/network-design2.png)

**Po włączeniu trybu failover**

![Po włączeniu trybu failover](./media/hyper-v-vmm-networking/network-design3.png)

Po przejściu w tryb failover Usługa Site Recovery przydziela adresu IP dla każdego interfejsu sieciowego na maszynie Wirtualnej. Adres jest przydzielany z puli statycznych adresów IP w sieci istotne dla każdego wystąpienia maszyny Wirtualnej.

- Jeśli w puli adresów IP w lokacji dodatkowej jest taka sama jak w lokacji źródłowej, Usługa Site Recovery przydziela ten sam adres IP (źródłowa maszyna wirtualna), do repliki maszyny Wirtualnej. Adres IP jest zastrzeżony w programie VMM, ale nie jest ustawiona jako adresu IP trybu failover na hoście funkcji Hyper-V. Adres IP trybu failover na hoście funkcji Hyper-v jest ustawiony tuż przed przełączenie w tryb failover.
- Jeśli ten sam adres IP jest niedostępna, Usługa Site Recovery przydziela kolejny dostępny adres IP z puli.
- Jeśli maszyny wirtualne używać protokołu DHCP, Usługa Site Recovery nie zarządzać adresami IP. Należy sprawdzić, czy serwer DHCP w lokacji dodatkowej można przydzielić adresy z tego samego zakresu jak lokacja źródłowa.

### <a name="validate-the-ip-address"></a>Sprawdź poprawność adresu IP

Po włączeniu ochrony dla maszyny Wirtualnej, można użyć następujący przykładowy skrypt, aby sprawdzić adres przypisany do maszyny Wirtualnej. Ten adres IP jest ustawiony jako adresu IP trybu failover i przypisanych do maszyny Wirtualnej w momencie przejścia w tryb failover:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Użyj innego adresu IP

W tym scenariuszu są zmieniane adresy IP maszyn wirtualnych, które w trybie Failover. Wadą tego rozwiązania jest wymaganej obsługi technicznej.  Wpisy DNS i pamięci podręcznej może być konieczne do zaktualizowania. Może to spowodować Przestój, który może być ograniczona w następujący sposób:

- Niskie wartości czasu wygaśnięcia na użytek aplikacji intranetowych.
- Użyj następującego skryptu w planie odzyskiwania Usługa Site Recovery dla aktualnych aktualizacji serwera DNS. Jeśli używasz dynamiczną rejestrację DNS nie jest konieczne skryptu.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Przykład 

W tym przykładzie firma Microsoft ma różne adresy IP w lokacjach głównych i dodatkowych, a trzeci lokacji, z aplikacji, które w serwisie główna osoba kontaktowa lub odzyskiwanie lokacji można uzyskać dostęp.

- Przed włączeniem trybu failover aplikacje są 192.168.1.0/24 podsieci hostowanej w lokacji głównej.
- Po przejściu w tryb failover aplikacji są konfigurowane w podsieci 172.16.1.0/24 w lokacji dodatkowej.
- Wszystkie trzy lokalizacje mogą uzyskiwać dostęp do siebie nawzajem.
- Po włączeniu trybu failover aplikacje zostaną przywrócone w podsieci odzyskiwania.
- W tym scenariuszu nie ma potrzeby do pracy awaryjnej w całej podsieci, a następnie ponownie skonfigurować trasy sieci VPN lub sieci są wymagane żadne zmiany. Przełączenie w tryb failover, a niektóre aktualizacjami rekordów DNS, upewnij się, że aplikacje będą nadal dostępne.
- Jeśli DNS jest skonfigurowany do zezwalania na aktualizacje dynamiczne, maszyny wirtualne będą rejestrują się za pomocą nowego adresu IP podczas uruchamiania po włączeniu trybu failover.

**Przed włączeniem trybu failover**

![Inny adres IP — przed włączeniem trybu failover](./media/hyper-v-vmm-networking/network-design10.png)

**Po włączeniu trybu failover**

![Inny adres IP — po włączeniu trybu failover](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Kolejne kroki

[Uruchamianie trybu failover](hyper-v-vmm-failover-failback.md)

