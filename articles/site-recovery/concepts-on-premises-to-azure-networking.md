---
title: Łączenie się z lokalnymi maszynami wirtualnymi platformy Azure w wersji failover za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób łączenia się z maszynami wirtualnymi platformy Azure po przełączeniu wdrówka po awarii z lokalnego na platformę Azure przy użyciu usługi Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281992"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Łączenie się z maszynami wirtualnymi platformy Azure po przełączeniu w ten sposób z lokalnego środowiska 


W tym artykule opisano sposób konfigurowania łączności, dzięki czemu można pomyślnie połączyć się z maszynami wirtualnymi platformy Azure po przełączeniu w stan failover.

Po skonfigurowaniu odzyskiwania po awarii lokalnych maszyn wirtualnych (VM) i serwerów fizycznych na platformie Azure usługa [Azure Site Recovery](site-recovery-overview.md) uruchamia replikację maszyn na platformie Azure. Następnie, gdy wystąpią awarie, można przejść w stan fail over do platformy Azure z witryny lokalnej. W przypadku pracy awaryjnej usługa Site Recovery tworzy maszyny wirtualne platformy Azure przy użyciu replikowanych danych lokalnych. W ramach planowania odzyskiwania po awarii, należy dowiedzieć się, jak połączyć się z aplikacjami uruchomionymi na tych maszynach wirtualnych platformy Azure po przełączeniu w stan failover.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Przygotuj maszyny lokalne przed przejściem w stan failover.
> * Przygotowywanie maszyn wirtualnych platformy Azure po przemijaniu awaryjnym. 
> * Zachowaj adresy IP na maszynach wirtualnych platformy Azure po przemijaniu awaryjnym.
> * Przypisywanie nowych adresów IP do maszyn wirtualnych platformy Azure po przemijania awaryjnego.

## <a name="prepare-on-premises-machines"></a>Przygotowywanie maszyn lokalnych

Aby zapewnić łączność z maszynami wirtualnymi platformy Azure, przygotuj swoje komputery lokalne przed przełączeniem awaryjnym.

### <a name="prepare-windows-machines"></a>Przygotowywanie maszyn z systemem Windows

Na lokalnych komputerach z systemem Windows wykonaj następujące czynności:

1. Skonfiguruj ustawienia systemu Windows. Obejmują one usunięcie statycznych tras trwałych lub serwera proxy WinHTTP i ustawienie zasad sieci SAN na **dysk onlineall**. [Postępuj zgodnie z](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) tymi instrukcjami.

2. Upewnij się, że [te usługi](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) są uruchomione.

3. Włącz pulpit zdalny (RDP), aby zezwolić na zdalne połączenia z komputerem lokalnym. [Dowiedz się, jak](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) włączyć prow z programem PowerShell.

4. Aby uzyskać dostęp do maszyny Wirtualnej platformy Azure przez Internet po pracy awaryjnej, w Zaporze systemu Windows na komputerze lokalnym, zezwalaj na protokół TCP i UDP w profilu publicznym i ustaw protokół RDP jako dozwoloną aplikację dla wszystkich profili.

5. Jeśli chcesz uzyskać dostęp do maszyny Wirtualnej platformy Azure za pośrednictwem sieci VPN lokacji lokacji po przełączeniu w tryb failover, w Zaporze systemu Windows na komputerze lokalnym zezwalaj na prow prow dla profilów domeny i prywatnych. [Dowiedz się,](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) jak zezwolić na ruch RDP.
6. Upewnij się, że nie ma żadnych aktualizacji systemu Windows oczekujących na lokalnej maszynie Wirtualnej podczas wyzwalania pracy awaryjnej. Jeśli tak, aktualizacje mogą rozpocząć instalację na maszynie Wirtualnej platformy Azure po przełączeniu w pracę awaryjną i nie będzie można zalogować się do maszyny Wirtualnej, dopóki aktualizacje nie zostaną zakończą.

### <a name="prepare-linux-machines"></a>Przygotowanie maszyn z systemem Linux

Na lokalnych komputerach z systemem Linux wykonaj następujące czynności:

1. Sprawdź, czy usługa Secure Shell jest skonfigurowana do automatycznego uruchamiania po rozruchu systemu.
2. Sprawdź, czy reguły zapory zezwalają na połączenie SSH.


## <a name="configure-azure-vms-after-failover"></a>Konfigurowanie maszyn wirtualnych platformy Azure po przeżyciu awaryjnym

Po przewijeniu w stan failover wykonaj następujące czynności na maszynach wirtualnych platformy Azure, które są tworzone.

1. Aby połączyć się z maszyną wirtualną przez Internet, przypisz publiczny adres IP do maszyny Wirtualnej. Nie można użyć tego samego publicznego adresu IP dla maszyny Wirtualnej platformy Azure, który był używany dla komputera lokalnego. [Dowiedz się więcej](../virtual-network/virtual-network-public-ip-address.md)
2. Sprawdź, czy reguły sieciowej grupy zabezpieczeń (NSG) na maszynie wirtualnej zezwalają na przychodzące połączenia z portem RDP lub SSH.
3. Sprawdź [diagnostykę rozruchu,](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) aby wyświetlić maszynę wirtualną.


> [!NOTE]
> Usługa Azure Bastion oferuje prywatny dostęp rdp i SSH do maszyn wirtualnych platformy Azure. [Dowiedz się więcej](../bastion/bastion-overview.md) o tej usłudze.

## <a name="set-a-public-ip-address"></a>Ustawianie publicznego adresu IP

Jako alternatywę dla ręcznego przypisywania publicznego adresu IP do maszyny Wirtualnej platformy Azure można przypisać adres podczas pracy awaryjnej przy użyciu skryptu lub systemu runbook automatyzacji platformy Azure w [planie odzyskiwania lokacji](site-recovery-create-recovery-plans.md)lub skonfigurować routing na poziomie DNS przy użyciu usługi Azure Traffic Manager. [Dowiedz się więcej](concepts-public-ip-address-with-site-recovery.md) o konfigurowaniu adresu publicznego.


## <a name="assign-an-internal-address"></a>Przypisywanie adresu wewnętrznego

Aby ustawić wewnętrzny adres IP maszyny Wirtualnej platformy Azure po przemijce awaryjnej, masz kilka opcji:

- **Zachowaj ten sam adres IP:** Można użyć tego samego adresu IP na maszynie Wirtualnej platformy Azure, jak adres przydzielony do komputera lokalnego.
- **Użyj innego adresu IP:** Można użyć innego adresu IP dla maszyny Wirtualnej platformy Azure.


## <a name="retain-ip-addresses"></a>Zachowywanie adresów IP

Usługa Site Recovery umożliwia zachowanie tych samych adresów IP w przypadku awarii na platformie Azure. Zachowanie tego samego adresu IP pozwala uniknąć potencjalnych problemów z siecią po pracy awaryjnej, ale wprowadza pewną złożoność.

- Jeśli docelowa maszyna wirtualna platformy Azure używa tego samego adresu IP/podsieci co lokacja lokalna, nie można połączyć się między nimi przy użyciu połączenia sieci VPN lokacja lokacja-lokacja lub usługi ExpressRoute, ponieważ adres nakłada się. Podsieci muszą być unikatowe.
- Potrzebujesz połączenia z lokalnego do platformy Azure po przełączeniu w błąd, dzięki czemu aplikacje są dostępne na maszynach wirtualnych platformy Azure. Platforma Azure nie obsługuje rozciągniętych sieci VLAN, więc jeśli chcesz zachować adresy IP, musisz przejąć miejsce ip na platformie Azure, ponieważ w przypadku awarii w całej podsieci, oprócz komputera lokalnego.
- Praca awaryjna podsieci zapewnia, że określona podsieć nie jest dostępna jednocześnie lokalnie i na platformie Azure.

Zachowywanie adresów IP wymaga następujących czynności:

- We właściwościach Sieci & obliczeń elementu replikowanego ustaw adres sieciowy i IP docelowej maszyny Wirtualnej platformy Azure, aby dublować ustawienie lokalne.
- Podsieci muszą być zarządzane w ramach procesu odzyskiwania po awarii. Potrzebujesz sieci wirtualnej platformy Azure, aby dopasować je do sieci lokalnej, a po przejściu awaryjnym trasy sieciowe muszą zostać zmodyfikowane, aby odzwierciedlić, że podsieć została przeniesiona na platformę Azure, a nowe lokalizacje adresów IP.  

### <a name="failover-example"></a>Przykład trybu failover

Spójrzmy na przykład.

- Fikcyjna firma Woodgrove Bank hostuje swoje aplikacje biznesowe lokalnie Hostują swoje aplikacje mobilne na platformie Azure.
- Łączą się one z lokalnego do platformy Azure za pośrednictwem sieci VPN lokacji lokacji. 
- Woodgrove używa usługi Site Recovery do replikowania maszyn lokalnych na platformie Azure.
- Ich aplikacje lokalne używają zakodowanych adresów IP, więc chcą zachować te same adresy IP na platformie Azure.
- Lokalnie na komputerach z uruchomionymi aplikacjami są uruchomione w trzech podsieciach:
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- Ich aplikacje uruchomione na platformie Azure znajdują się w sieci wirtualnej azure platformy **Azure network** w dwóch podsieciach:
- 172.16.1.0/24
- 172.16.2.0/24.

Aby zachować adresy, oto, co robią.

1. Po włączeniu replikacji określają one, że maszyny powinny być replikowane do **sieci Azure**.
2. Tworzą one **sieć odzyskiwania na** platformie Azure. Ta sieć wirtualna dubluje podsieć 192.168.1.0/24 w sieci lokalnej.
3. Woodgrove konfiguruje [połączenie sieci wirtualnej z siecią wirtualną](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) między dwiema sieciami. 

    > [!NOTE]
    > W zależności od wymagań aplikacji połączenie sieci wirtualnej z siecią wirtualną można skonfigurować przed przełączeniem awaryjnym, jako ręczny krok/skryptowy krok/identyfikator runbook automatyzacji platformy Azure w [planie odzyskiwania usługi](site-recovery-create-recovery-plans.md)site recovery lub po zakończeniu pracy awaryjnej.

4. Przed przełączeniem awaryjnym właściwości komputera w odzyskiwaniu lokacji ustawiają docelowy adres IP na adres komputera lokalnego, zgodnie z opisem w następnej procedurze.
5. Po przemierniu awaryjnym maszyny wirtualne platformy Azure są tworzone przy tym samym adresie IP. Woodgrove łączy się z **usługi Azure Network** do sieci **wirtualnej sieci odzyskiwania** przy użyciu komunikacji równorzędnej sieci wirtualnej (z włączoną łącznością tranzytową).
6. Lokalnie Woodgrove musi wprowadzać zmiany w sieci, w tym modyfikowanie tras, aby odzwierciedlić, że 192.168.1.0/24 został przeniesiony na platformę Azure.  

**Infrastruktura przed przejściem awaryjnym**

![Przed podsiecią trybu failover](./media/site-recovery-network-design/network-design7.png)


**Infrastruktura po przemijalniu awaryjnym**

![Po przemijaniu awaryjnym podsieci](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Ustawianie docelowych ustawień sieciowych

Przed przełączeniem awaryjnym określ ustawienia sieciowe i adres IP docelowej maszyny Wirtualnej platformy Azure.

1.  W magazynie usług odzyskiwania -> **elementy replikowane**wybierz komputer lokalny.
2. Na stronie **Obliczenia i Sieć** dla komputera kliknij przycisk **Edytuj,** aby skonfigurować ustawienia sieci i karty dla docelowej maszyny Wirtualnej platformy Azure.
3. We **właściwościach sieci**wybierz sieć docelową, w której będzie znajdować się maszyna wirtualna platformy Azure po jej utworzeniu po przełączeniu awaryjnym.
4. W **interfejsach sieci konfigurować**karty sieciowe w sieci docelowej. Domyślnie funkcja Odzysk lokacji pokazuje wszystkie wykryte karty sieciowe na komputerze lokalnym.
    - W **docelowym typie interfejsu sieciowego** można ustawić każdą kartę sieciową jako **podstawową,** **pomocniczą**lub **Nie należy tworzyć,** jeśli nie jest potrzebna określona karta sieciowa w sieci docelowej. Jedna karta sieciowa musi być ustawiona jako podstawowa dla trybu failover. Należy zauważyć, że modyfikowanie sieci docelowej wpływa na wszystkie karty sieciowe dla maszyny Wirtualnej platformy Azure.
    - Kliknij nazwę karty sieciowej, aby określić podsieć, w której zostanie wdrożona maszyna wirtualna platformy Azure.
    - Zastąp **dynamiczny** prywatnym adresem IP, który chcesz przypisać do docelowej maszyny Wirtualnej platformy Azure. Jeśli adres IP nie jest określony Site Recovery, przy pracy awaryjnej zostanie przypisany następny dostępny adres IP w podsieci do karty sieciowej.
    - [Dowiedz się więcej](site-recovery-manage-network-interfaces-on-premises-to-azure.md) o zarządzaniu kartami sieciowymi dla lokalnego trybu failover na platformie Azure.


## <a name="get-new-ip-addresses"></a>Uzyskaj nowe adresy IP

W tym scenariuszu maszyna wirtualna platformy Azure pobiera nowy adres IP po pracy awaryjnej. Aktualizacja DNS do aktualizowania rekordów dla komputerów awaryjnych, aby wskazać adres IP maszyny wirtualnej platformy Azure.



## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o](site-recovery-active-directory.md) replikowaniu lokalnej usługi Active Directory i usługi DNS na platformę Azure.


