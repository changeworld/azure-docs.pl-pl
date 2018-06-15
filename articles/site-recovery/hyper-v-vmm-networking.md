---
title: Skonfiguruj połączenie z lokacją lokalną dodatkowej po pracy awaryjnej z usługą Azure Site Recovery adresy IP | Dokumentacja firmy Microsoft
description: Opisuje sposób konfigurowania adresowania IP dla łączenia na maszynach wirtualnych w lokacji dodatkowej lokalnymi po pracy awaryjnej usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: rayne
ms.openlocfilehash: 531705bc704b3366c1c670ecf07c809ade67bc55
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29378890"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Skonfiguruj połączenie z lokacją lokalną dodatkowej po pracy awaryjnej adresy IP

Po przejścia w tryb failover maszyn wirtualnych funkcji Hyper-V w chmurach programu System Center Virtual Machine Manager (VMM) do lokacji dodatkowej, musisz być w stanie połączyć się repliki maszyn wirtualnych. Ten artykuł pomoże Ci w tym celu. 

## <a name="connection-options"></a>Opcje połączenia

Po przejściu w tryb failover istnieje kilka sposobów, aby obsłużyć adresowania IP dla maszyn wirtualnych repliki: 

- **Zachować ten sam adres IP po pracy awaryjnej**: W tym scenariuszu zreplikowanej maszyny Wirtualnej ma ten sam adres IP, jako podstawowej maszyny Wirtualnej. Upraszcza to związany z siecią problemów po pracy awaryjnej, ale wymaga dodatkowych czynności infrastruktury.
- **Użyj innego adresu IP po pracy awaryjnej**: W tym scenariuszu maszyny Wirtualnej pobiera nowego adresu IP po pracy awaryjnej. 
 

## <a name="retain-the-ip-address"></a>Zachować adres IP

Jeśli chcesz zachować adresów IP z lokacji głównej, po przejściu w tryb failover lokacji dodatkowej, można:

- Wdróż rozciągnięty podsieci między serwerem podstawowym i lokacje dodatkowe.
- Wykonaj pełną podsieci trybu failover z serwera podstawowego do lokacji dodatkowej. Musisz zaktualizować trasy, aby wskazać, do nowej lokalizacji adresów IP.


### <a name="deploy-a-stretched-subnet"></a>Wdrażanie rozciągnięty podsieci

W konfiguracji rozciągnięty podsieci jest dostępna jednocześnie w lokacjach głównych i dodatkowych. W podsieci rozciągnięty po przeniesieniu do lokacji dodatkowej konfiguracji adresu IP (warstwy 3) i komputer z sieci automatycznie kieruje ruchem do nowej lokalizacji. 

- Z perspektywy warstwy 2 (warstwę łącza danych) należy sprzęt sieciowy, którym może zarządzać rozciągnięty sieci VLAN.
- Przeciągnij uchwyt sieci VLAN, potencjalne domeny błędów rozszerza do obu lokacji. To staje się pojedynczym punktem awarii. Gdy jest to mało prawdopodobne, w takiej sytuacji nie można do izolowania zdarzenia, takie jak storm emisji. 


### <a name="fail-over-a-subnet"></a>Tryb failover podsieci

W całej podsieci w celu uzyskania zalet rozciągnięty podsieci, bez jego faktycznego rozciągania może zakończyć się niepowodzeniem. W tym rozwiązaniu podsieci jest dostępna w lokacji źródłowej lub docelowej, ale nie oba jednocześnie.

- Aby zachować przestrzeń adresów IP w przypadku trybu failover, można programowo Rozmieść infrastruktury router przenieść podsieci z jednej lokacji.
- W przypadku wystąpienia pracy awaryjnej podsieci są przenoszone wraz z ich skojarzone maszyny wirtualne.
- Główną wadą tego podejścia jest, że w przypadku awarii, należy przenieść w całej podsieci.

#### <a name="example"></a>Przykład

Oto przykład podsieci ukończenia pracy awaryjnej. 

- Przed trybu failover lokacja główna ma aplikacji uruchomionych w podsieci 192.168.1.0/24.
- Podczas przejścia w tryb failover wszystkich maszyn wirtualnych w tej podsieci można w lokacji dodatkowej i zachowywanie ich adresy IP. 
- Tras między wszystkimi lokacjami muszą zostać zmodyfikowane w celu odzwierciedlenia faktu, że wszystkich maszyn wirtualnych w podsieci 192.168.1.0/24 zostały przeniesione do lokacji dodatkowej.

Przedstawiono na poniższej grafice podsieci przed i po pracy awaryjnej.


**Przed trybu failover**

![Przed trybu failover](./media/hyper-v-vmm-networking/network-design2.png)

**Po pracy awaryjnej**

![Po pracy awaryjnej](./media/hyper-v-vmm-networking/network-design3.png)

Po pracy w trybie failover Usługa Site Recovery przydziela adres IP dla każdego interfejsu sieciowego na maszynie Wirtualnej. Ten adres jest przydzielone z puli statycznych adresów IP w sieci istotne dla każdego wystąpienia maszyny Wirtualnej.

- Jeśli w puli adresów IP w lokacji dodatkowej jest takie samo jak w lokacji źródłowej, Site Recovery przydziela tego samego adresu IP (źródłowej maszyny Wirtualnej), do repliki maszyny Wirtualnej. Adres IP jest zastrzeżony w programie VMM, ale nie została ustawiona jako adresu IP trybu failover, na hoście funkcji Hyper-V. Adres IP trybu failover na hoście funkcji Hyper-v ustawiono tuż przed pracy awaryjnej.
- Jeśli ten sam adres IP nie jest dostępna, Usługa Site Recovery przydziela kolejny dostępny adres IP z puli.
- Jeśli maszyny wirtualne korzystania z protokołu DHCP, Usługa Site Recovery nie zarządzania adresami IP. Należy sprawdzić, czy serwera DHCP w lokacji dodatkowej można przydzielić adresy z tego samego zakresu, jak lokacja źródłowa.

### <a name="validate-the-ip-address"></a>Sprawdź poprawność adresu IP

Po włączeniu ochrony dla maszyny Wirtualnej można następujący przykładowy skrypt zweryfikować adres przypisany do maszyny Wirtualnej. Ten adres IP jest ustawiony jako adresu IP trybu failover i przypisane do maszyny Wirtualnej w trybie failover:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Użyj innego adresu IP

W tym scenariuszu zmieniły się adresy IP maszyn wirtualnych działających w trybie Failover. Wadą tego rozwiązania jest utrzymanie wymagane.  Wpisy DNS i pamięci podręcznej może wymagać aktualizacji. Może to spowodować Przestój, które można zminimalizować w następujący sposób:

- Użyj niskich wartości TTL dla aplikacji sieci intranet.
- Użyj następującego skryptu w planie odzyskiwania usługi Site Recovery terminowo aktualizacji serwera DNS. Skrypt nie jest konieczne użycie dynamiczną rejestrację DNS.

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

W tym przykładzie mamy różne adresy IP w lokacjach głównych i dodatkowych, a trzeci witryny, z aplikacji, które na podstawowym lub odzyskiwania lokacji można uzyskać dostęp.

- Przed trybu failover aplikacje są 192.168.1.0/24 hostowanej podsieci w lokacji głównej.
- Po przejściu w tryb failover aplikacje są konfigurowane w podsieci 172.16.1.0/24 w lokacji dodatkowej.
- Wszystkie trzy witryny mają dostęp do siebie.
- Po przejściu w tryb failover aplikacje zostaną przywrócone w podsieci odzyskiwania.
- W tym scenariuszu nie istnieje potrzeba do pracy awaryjnej w całej podsieci i ponownie skonfigurować trasy sieci VPN lub sieć nie są wymagane żadne zmiany. Tryb failover, a niektóre aktualizacje DNS upewnij się, że aplikacje będą nadal dostępne.
- Jeśli DNS jest skonfigurowany do zezwalania na aktualizacje dynamiczne, maszyn wirtualnych będzie rejestrują się za pomocą nowego adresu IP, po rozpoczęciu po pracy awaryjnej.

**Przed trybu failover**

![Inny adres IP — przed trybu failover](./media/hyper-v-vmm-networking/network-design10.png)

**Po pracy awaryjnej**

![Inny adres IP — po trybu failover](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Kolejne kroki

[Tryb failover](hyper-v-vmm-failover-failback.md)

