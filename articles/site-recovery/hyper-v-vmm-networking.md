---
title: Konfigurowanie adresowania IP po przewijaniu po awarii w lokacji dodatkowej za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania adresowania IP do łączenia się z maszynami wirtualnymi w dodatkowej lokacji lokalnej po odzyskiwaniu po awarii i pracy awaryjnej za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: a61f7ff69e648262eb721eb61a98b09dbbee924c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961424"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Konfigurowanie adresowania IP w celu łączenia się z dodatkową lokacją lokalną po przełączeniu w ten sposób po przełączeniu awaryjnym

Po przełączeniu maszyn wirtualnych funkcji Hyper-V w chmurach Menedżera maszyn wirtualnych (System Center Virtual Machine Manager) w lokacji dodatkowej należy mieć możliwość nawiązania połączenia z replikami maszyn wirtualnych. Ten artykuł pomaga to zrobić. 

## <a name="connection-options"></a>Opcje połączenia

Po przemieraniu awaryjnego istnieje kilka sposobów obsługi adresowania IP dla maszyn wirtualnych replik: 

- **Zachowaj ten sam adres IP po przewijaniu awaryjnym:** W tym scenariuszu replikowana maszyna wirtualna ma ten sam adres IP co podstawowa maszyna wirtualna. Upraszcza to problemy związane z siecią po pracy awaryjnej, ale wymaga pewnych prac związanych z infrastrukturą.
- **Użyj innego adresu IP po pracy awaryjnej:** W tym scenariuszu maszyna wirtualna pobiera nowy adres IP po przejściu w tryb failover. 
 

## <a name="retain-the-ip-address"></a>Zachowaj adres IP

Jeśli chcesz zachować adresy IP z lokacji głównej, po przełączeniu w życie awaryjne lokacji dodatkowej, możesz:

- Wdrażanie rozciągniętej podsieci między lokacjami podstawowymi i dodatkowymi.
- Wykonaj pełną podsieć pracy awaryjnej z lokacji podstawowej do dodatkowej. Należy zaktualizować trasy, aby wskazać nową lokalizację adresów IP.


### <a name="deploy-a-stretched-subnet"></a>Wdrażanie rozciągniętej podsieci

W konfiguracji rozciągniętej podsieć jest dostępna jednocześnie w lokacjach podstawowych i dodatkowych. W rozciągniętej podsieci po przeniesieniu komputera i jego konfiguracji adresu IP (Warstwa 3) do lokacji dodatkowej sieć automatycznie kieruje ruch do nowej lokalizacji. 

- Z punktu widzenia warstwy 2 (warstwa łącza danych) potrzebny jest sprzęt sieciowy, który może zarządzać rozciągniętą siecią VLAN.
- Rozciągając sieć VLAN, domena potencjalnych usterek rozciąga się na obie lokacje. Staje się to pojedynczym punktem awarii. Chociaż jest to mało prawdopodobne, w takim scenariuszu może nie być w stanie wyizolować incydentu, takiego jak burza transmisji. 


### <a name="fail-over-a-subnet"></a>Przejmuje się w sieci w stanie fail over podsieci

Można w pełni przejść przez całą podsieć, aby uzyskać korzyści z rozciągniętej podsieci, bez rozciągania jej. W tym rozwiązaniu podsieć jest dostępna w lokacji źródłowej lub docelowej, ale nie w obu jednocześnie.

- Aby zachować przestrzeń adresową IP w przypadku pracy awaryjnej, można programowo zorganizować dla infrastruktury routera, aby przenieść podsieci z jednej lokacji do drugiej.
- W przypadku pracy awaryjnej podsieci są przesuniętye wraz ze skojarzonymi z nimi maszynami wirtualnymi.
- Główną wadą tego podejścia jest to, że w przypadku awarii należy przenieść całą podsieć.

#### <a name="example"></a>Przykład

Oto przykład pełnego trybu failover podsieci. 

- Przed przełączeniem awaryjnym lokacja główna ma aplikacje uruchomione w podsieci 192.168.1.0/24.
- Podczas pracy awaryjnej wszystkie maszyny wirtualne w tej podsieci są awaryjne w lokacji dodatkowej i zachowują swoje adresy IP. 
- Trasy między wszystkimi lokacjami muszą zostać zmodyfikowane, aby odzwierciedlić fakt, że wszystkie maszyny wirtualne w podsieci 192.168.1.0/24 zostały przeniesione do lokacji dodatkowej.

Poniższa grafika ilustruje podsieci przed i po pracy awaryjnej.


**Przed przejściem awaryjnym**

![Przed przejściem awaryjnym](./media/hyper-v-vmm-networking/network-design2.png)

**Po pracy awaryjnej**

![Po pracy awaryjnej](./media/hyper-v-vmm-networking/network-design3.png)

Po przemijeniu awaryjnym usługa Site Recovery przydziela adres IP dla każdego interfejsu sieciowego na maszynie Wirtualnej. Adres jest przydzielany ze statycznej puli adresów IP w odpowiedniej sieci dla każdego wystąpienia maszyny Wirtualnej.

- Jeśli pula adresów IP w lokacji dodatkowej jest taka sama jak w lokacji źródłowej, usługa Site Recovery przydziela ten sam adres IP (źródłowej maszyny Wirtualnej) do repliki maszyny Wirtualnej. Adres IP jest zarezerwowany w programie VMM, ale nie jest ustawiony jako adres IP trybu failover na hoście funkcji Hyper-V. Adres IP trybu failover na hoście funkcji Hyper-v jest ustawiany tuż przed przełączeniem trybu failover.
- Jeśli ten sam adres IP nie jest dostępny, usługa Site Recovery przydziela inny dostępny adres IP z puli.
- Jeśli maszyny wirtualne korzystają z usługi DHCP, usługa Site Recovery nie zarządza adresami IP. Należy sprawdzić, czy serwer DHCP w lokacji dodatkowej może przydzielać adresy z tego samego zakresu co lokacja źródłowa.

### <a name="validate-the-ip-address"></a>Sprawdzanie poprawności adresu IP

Po włączeniu ochrony maszyny Wirtualnej można użyć następującego przykładowego skryptu, aby zweryfikować adres przypisany do maszyny Wirtualnej. Ten adres IP jest ustawiony jako adres IP trybu failover i przypisany do maszyny Wirtualnej w czasie pracy awaryjnej:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Używanie innego adresu IP

W tym scenariuszu adresy IP maszyn wirtualnych, które są awaryjne są zmieniane. Wadą tego rozwiązania jest wymagana konserwacja.  Wpisy DNS i pamięci podręcznej mogą wymagać aktualizacji. Może to spowodować przestoje, które można złagodzić w następujący sposób:

- Użyj niskich wartości TTL dla aplikacji intranetowych.
- Użyj następującego skryptu w planie odzyskiwania usługi Site Recovery, aby na czas zaktualizować serwer DNS. Skrypt nie jest potrzebny, jeśli używasz dynamicznej rejestracji DNS.

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

W tym przykładzie mamy różne adresy IP w lokacjach podstawowych i dodatkowych, a istnieje trzecia lokacja, z której można uzyskać dostęp do aplikacji hostowanych w lokacji podstawowej lub lokacji odzyskiwania.

- Przed przełączeniem pracy awaryjnej aplikacje są hostowane podsieć 192.168.1.0/24 w lokacji głównej.
- Po przemijce awaryjnej aplikacje są konfigurowane w podsieci 172.16.1.0/24 w lokacji dodatkowej.
- Wszystkie trzy witryny mogą mieć do siebie dostęp.
- Po przemijeniu w stan failover aplikacje zostaną przywrócone w podsieci odzyskiwania.
- W tym scenariuszu nie ma potrzeby pracy awaryjnej w całej podsieci i nie są potrzebne żadne zmiany do ponownej konfiguracji tras sieci VPN lub sieci. Przewijalnia awaryjna i niektóre aktualizacje dns zapewniają dostępność aplikacji.
- Jeśli usługa DNS jest skonfigurowana tak, aby zezwalała na aktualizacje dynamiczne, maszyny wirtualne rejestrują się przy użyciu nowego adresu IP po uruchomieniu po przejściu w tryb failover.

**Przed przejściem awaryjnym**

![Inny adres IP - przed przejściem awaryjnym](./media/hyper-v-vmm-networking/network-design10.png)

**Po pracy awaryjnej**

![Inny adres IP - po pracy awaryjnej](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Następne kroki

[Uruchamianie trybu failover](hyper-v-vmm-failover-failback.md)

