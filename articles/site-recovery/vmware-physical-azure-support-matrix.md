---
title: Macierz obsługi na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie obsługi odzyskiwania po awarii maszyn wirtualnych VMware i serwera fizycznego na platformie Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: raynew
ms.openlocfilehash: 8fd9f56c262ce3a7110aa71bf72d01fe875212c0
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275763"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Macierz obsługi dla odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure

Ten artykuł zawiera podsumowanie obsługiwanych składników i ustawienia na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych do platformy Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).

- [Dowiedz się więcej](vmware-azure-architecture.md) o architekturze odzyskiwania po awarii serwera fizycznego/VMware, maszyny Wirtualnej.
- Postępuj zgodnie z naszym [samouczki](tutorial-prepare-azure.md) możesz wypróbować usługę odzyskiwania po awarii.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

**Scenariusz** | **Szczegóły**
--- | ---
Odzyskiwanie po awarii maszyn wirtualnych programu VMware | Replikacji lokalnych maszyn wirtualnych VMware na platformę Azure. Można wdrożyć ten scenariusz, w witrynie Azure portal lub za pomocą [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Odzyskiwanie po awarii serwerów fizycznych | Replikacji lokalnych serwerów fizycznych systemu Windows/Linux na platformie Azure. Można wdrożyć ten scenariusz, w witrynie Azure portal.

## <a name="on-premises-virtualization-servers"></a>Lokalne serwery wirtualizacji

**Serwer** | **Wymagania** | **Szczegóły**
--- | --- | ---
Program vCenter Server | 6\.7, 6.5, 6.0 lub 5.5 | Zalecamy użycie serwera vCenter w danym wdrożeniu odzyskiwania po awarii.
hosty vSphere | 6\.7, 6.5, 6.0 lub 5.5 | Zaleca się, że hosty vSphere i vCenter Server znajdują się w tej samej sieci, co serwer przetwarzania. Domyślnie serwer przetwarzania jest uruchamiany na serwerze konfiguracji. [Dowiedz się więcej](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Serwera konfiguracji usługi Site Recovery

Serwer konfiguracji jest maszyna w środowisku lokalnym, na której działa składniki usługi Site Recovery, w tym serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy.

- Dla maszyn wirtualnych VMware na serwerze konfiguracji jest ustawiony przez pobranie szablonu pakietu OVF do utworzenia maszyny Wirtualnej VMware.
- Dla serwerów fizycznych należy skonfigurować komputera serwera konfiguracji ręcznie.

**Składnik** | **Wymagania**
--- |---
Rdzenie procesora CPU | 8
Pamięć RAM | 16 GB
Liczba dysków | 3 dyskami<br/><br/> Dyski obejmują dysku systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania na potrzeby powrotu po awarii.
Wolne miejsce na dysku | 600 GB miejsca dla pamięci podręcznej serwera przetwarzania.
Wolne miejsce na dysku | 600 GB miejsca na dysku przechowywania.
System operacyjny  | Windows Server 2012 R2 lub Windows Server 2016 ze środowiskiem pulpitu |
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
[Interfejs PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Nie wymagane przez wersję serwera konfiguracji [: 9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) lub nowszej. 
Role systemu Windows Server | Don't enable Active Directory Domain Services; Internet Information Services (IIS) or Hyper-V. 
Zasady grupy| -Zapobiegaj dostępowi do wiersza polecenia. <br/> -Uniemożliwić dostęp do narzędzi edycji rejestru. <br/> — Logika zaufania dla plików załączników. <br/> -Włącz wykonywanie skryptu. <br/> - [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Upewnij się, że:<br/><br/> -Nie masz istniejących domyślnej witryny sieci Web <br/> -Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie  <br/> -Nie masz istniejących witryny sieci Web/aplikacja nasłuchuje na porcie 443<br/>
Typ karty NIC | Innego VMXNET3 (jeśli jest wdrażane jako maszyny Wirtualnej VMware)
Typ adresu IP | Static
Porty | 443 umożliwiający organizowanie kanału sterowania<br/>9443 dla danych transportu

## <a name="replicated-machines"></a>Replikowane maszyny

Usługa Site Recovery obsługuje dowolne obciążenia uruchomione na obsługiwanej maszynie replikacji.

**Składnik** | **Szczegóły**
--- | ---
Ustawienia komputera | Maszyny, które są replikowane do platformy Azure muszą spełniać [wymagania dotyczące usługi Azure](#azure-vm-requirements).
Obciążenie maszyny | Usługa Site Recovery obsługuje dowolne obciążenia uruchomione na obsługiwanej maszynie replikacji. [Dowiedz się więcej](https://aka.ms/asr_workload).
Windows | -Windows Server 2019 (obsługiwane przez [34 pakiet zbiorczy aktualizacji](https://support.microsoft.com/help/4490016) (wersja 9.22 usługi mobilności) i nowszych wersjach.<br/> -Windows Server 2016 (64-bitowych Server Core, serwer ze środowiskiem pulpitu)<br/> -Windows Server 2012 R2, Windows Server 2012<br/> -Windows Server 2008 R2 z na co najmniej z dodatkiem SP1.<br/> -Windows Server 2008, 64- i 32-bitowe z co najmniej z dodatkiem SP2]. Obsługiwane na potrzeby migracji tylko. [Dowiedz się więcej](migrate-tutorial-windows-server-2008.md).<br/> — System Windows 10, Windows 8.1, Windows 8, Windows 7 64-bitowy (obsługiwane przez [36 pakiet zbiorczy aktualizacji](https://support.microsoft.com/help/4503156) (wersja 9.22 usługi mobilności i nowszych wersjach). Windows 7 RTM nie jest obsługiwane. 
Linux | Obsługiwane jest tylko 64-bitowym systemie. 32-bitowy system nie jest obsługiwane.<br/><br/> Usługa Site Recovery organizuje trybu failover, aby uruchomić serwerów z systemem Linux na platformie Azure. Jednak dostawcy systemu Linux może ograniczyć obsługę tylko wersje dystrybucji, które jeszcze nie osiągnęły wycofanych z eksploatacji.<br/><br/> W dystrybucjach systemu Linux są obsługiwane tylko podstawowe jądra, które są częścią dystrybucji wersji pomocniczej wersji/aktualizacji.<br/><br/> Uaktualnianie chronionych maszyn w głównych systemu Linux, wersji dystrybucji nie jest obsługiwane. Aby przeprowadzić uaktualnienie, wyłącz replikację, Uaktualnij system operacyjny i następnie włącz ponownie replikację.<br/><br/> [Dowiedz się więcej](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) dotyczące pomocy technicznej dla systemu Linux i technologii typu open source na platformie Azure.
Linux Red Hat Enterprise | 5.2 i 5.11</b><br/> 6.1, 6.10</b><br/> 7.0 lub 7.6<br/> <br/> Serwery z systemem Red Hat Enterprise Linux 5.2 5.11 powinny mieć [składniki usługi LIS (Linux Integration)](https://www.microsoft.com/download/details.aspx?id=55106) zainstalowane maszyn rozruchu na platformie Azure.
W systemie Linux: CentOS | 5.2 i 5.11</b><br/> 6.1, 6.10</b><br/> 7.0 lub 7.6<br/> <br/> Serwery z systemem CentOS 5.2 5.11 powinny mieć [składniki usługi LIS (Linux Integration)](https://www.microsoft.com/download/details.aspx?id=55106) zainstalowane maszyn rozruchu na platformie Azure.
Ubuntu | Ubuntu 14.04 LTS server [(Sprawdź obsługiwane wersje jądra)](#ubuntu-kernel-versions)<br/><br/>Serwer Ubuntu 16.04 LTS [(Sprawdź obsługiwane wersje jądra)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(Sprawdź obsługiwane wersje jądra)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 z dodatkiem SP1, SP2, z dodatkiem SP3, z dodatkiem SP4 [(Sprawdź obsługiwane wersje jądra)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Uaktualnianie replikowanych maszyn z systemem SUSE Linux Enterprise Server 11 SP3 do SP4 nie jest obsługiwane. Aby przeprowadzić uaktualnienie, wyłącz replikację i ponownie włączyć po uaktualnieniu.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> System Red Hat zgodne jądra lub podzielenie Enterprise jądra w wersji 3, 4 i 5 (UEK3 UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Wersje jądra systemu Ubuntu


**Obsługiwana wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
14.04 LTS | [9.24][9.25 UR]  | 3.13.0-24-Generic do 3.13.0-169-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-146-generic,<br/>4.15.0-1023-Azure do 4.15.0-1042-azure |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-Generic do 3.13.0-167-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-143-generic,<br/>4.15.0-1023-Azure do 4.15.0-1040-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-Generic do 3.13.0-165-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-142-generic,<br/>4.15.0-1023-Azure do 4.15.0-1037-azure |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-Generic do 3.13.0-164-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-140-generic,<br/>4.15.0-1023-Azure do 4.15.0-1036-azure |
|||
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-Generic do 4.4.0-146-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-48-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1042-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-Generic do 4.4.0-143-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-46-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1040-azure|
16.04 LTS | [9.23][9.23 UR] | 4.4.0-21-Generic do 4.4.0-142-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-45-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1037-azure|
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-Generic do 4.4.0-140-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-43-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1036-azure|

### <a name="debian-kernel-versions"></a>Wersje jądra systemu debian


**Obsługiwana wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR],[9.23][9.23 Z PAKIETEM ZBIORCZYM AKTUALIZACJI], [9,24][9.24 UR]| 3.2.0-4-AMD64 do 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.25][9.25 UR] | 3.16.0-4-AMD64 do 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.8-amd64 |
Debian 8 | [9.22][9.22 UR],[9.23][9.23 UR], [9,24][9.24 Z PAKIETEM ZBIORCZYM AKTUALIZACJI] | 3.16.0-4-AMD64 do 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12. obsługiwane wersje jądra

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.25][9.25 UR] | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3\.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4\.4.121-92.73-default SP2(LTSS) do 4.4.121-92.104-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.176-94.88-default</br></br>Z dodatkiem SP3 4.4.138-4.7-azure do 4.4.176-4.25-azure</br></br>Z dodatkiem SP4 4.12.14-94.41-default do 4.12.14-95.13-default</br>Z dodatkiem SP4 4.12.14-6.3-azure do 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.24][9.24 UR] | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3\.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4\.4.121-92.73-default SP2(LTSS) do 4.4.121-92.101-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.175-94.79-default</br></br>Z dodatkiem SP4 4.12.14-94.41-default do 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.23][9.23 UR] | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3\.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4\.4.121-92.73-default SP2(LTSS) do 4.4.121-92.101-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.162-94.69-default</br></br>Z dodatkiem SP4 4.12.14-94.41-default do 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | [9.22][9.22 UR] | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3\.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4\.4.121-92.73-default SP2(LTSS) do 4.4.121-92.98-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.162-94.72-default |


## <a name="linux-file-systemsguest-storage"></a>Magazyn gościa/systemów plików systemu Linux

**Składnik** | **Obsługiwane**
--- | ---
Systemy plików | ext3, ext4, XFS
Menedżer woluminów | -LVM jest obsługiwane.<br/> - / rozruch na LVM jest obsługiwany z [31 pakiet zbiorczy aktualizacji](https://support.microsoft.com/help/4478871/) (wersja 9,20 usługi mobilności) i nowszych wersjach. Nie jest ono obsługiwane we wcześniejszych wersjach usługi mobilności.<br/> -Wiele dysków systemu operacyjnego nie są obsługiwane.
Urządzenia magazynujące Parawirtualne | Urządzenia eksportowane przez sterowniki parawirtualne nie są obsługiwane.
Blokuj wielu kolejek operacji We/Wy urządzenia | Nieobsługiwane.
Serwery fizyczne z kontrolera magazynu HP CCISS | Nieobsługiwane.
Konwencja nazewnictwa punktu instalacji/urządzenia | Nazwa urządzenia lub nazwę punktu instalacji powinny być unikatowe.<br/> Upewnij się, że żadne dwa punkty urządzeń/instalacji uwzględniana wielkość liter nazwy. Na przykład nazewnictwa urządzeń dla tej samej maszyny Wirtualnej jako *urządzenia 1* i *urządzenia 1* nie jest obsługiwane.
Katalogi | Jeśli uruchomiona wersja usługi mobilności wcześniejszej niż wersja 9,20 (wydany w [31 pakiet zbiorczy aktualizacji](https://support.microsoft.com/help/4478871/)), te ograniczenia mają zastosowanie, a następnie:<br/><br/> — Te katalogi (jeśli jest to konfigurowanie jako oddzielne partycje/pliku systems) musi znajdować się na tym samym dysku systemu operacyjnego na serwerze źródłowym: / (root), / Boot/usr, /usr/local, /var, etc.</br> -W katalogu/Boot powinny znajdować się na partycji dysku i nie być woluminem LVM.<br/><br/> Z wersji 9,20 lub nowszym te ograniczenia nie mają zastosowania. 
Katalog rozruchowy | -Wiele dysków rozruchowych są obsługiwane na maszynie Wirtualnej <br/> - / rozruch na woluminie LVM w więcej niż jeden dysk nie jest obsługiwany.<br/> -Nie można zreplikować maszynę bez dysku rozruchowego.
Wymagania dotyczące wolnego miejsca| 2 GB na partycję/root <br/><br/> 250 MB do folderu instalacji
XFSv5 | Funkcje XFSv5 w systemach plików XFS, takie jak metadane sumy kontrolnej, są obsługiwane (wersja usługi mobilności 9.10 lub nowszy).<br/> Użyj narzędzia xfs_info, aby sprawdzić superblock XFS dla partycji. Jeśli `ftype` jest równa 1, a następnie są używane funkcje XFSv5.
BTRFS | BTRFS jest obsługiwana przez [34 pakiet zbiorczy aktualizacji](https://support.microsoft.com/help/4490016) (wersja 9.22 usługi mobilności) i nowszych wersjach. BTRFS nie jest obsługiwane, gdy:<br/><br/> Subvolume system plików BTRFS została zmieniona po włączeniu ochrony.</br> — System plików BTRFS jest rozłożona na wiele dysków.</br> — System plików BTRFS obsługuje RAID.

## <a name="vmdisk-management"></a>Zarządzanie maszyna wirtualna/dysk

**Akcja** | **Szczegóły**
--- | ---
Zmiana rozmiaru dysku na zreplikowanej maszyny Wirtualnej | Obsługiwane.
Dodaj dysk na replikowanej maszynie Wirtualnej | Nieobsługiwane.<br/> Wyłącz replikację dla maszyny Wirtualnej, a następnie dodać dysk i ponownie włączyć replikację.

## <a name="network"></a>Sieć

**Składnik** | **Obsługiwane**
--- | ---
Sieci hostów z zespołu kart interfejsu Sieciowego | Obsługiwane w przypadku maszyn wirtualnych VMware. <br/><br/>Nieobsługiwane dla replikacji maszyna fizyczna.
Host sieci VLAN | Tak.
Host sieci IPv4 | Tak.
IPv6 w sieci hosta | Nie.
Sieci gościa/serwera zespołu kart interfejsu Sieciowego | Nie.
Gość/serwera w sieci IPv4 | Tak.
Gość/serwera IPv6 w sieci | Nie.
Gość/sieci statyczny adres IP serwera (Windows) | Tak.
Gość/sieci statyczny adres IP serwera (Linux) | Tak. <br/><br/>Maszyny wirtualne są skonfigurowane do używania protokołu DHCP w przypadku powrotu po awarii.
Wiele kart sieciowych sieci gościa/serwera | Tak.


## <a name="azure-vm-network-after-failover"></a>Sieć maszyny Wirtualnej platformy Azure (po włączeniu trybu failover)

**Składnik** | **Obsługiwane**
--- | ---
Usługa ExpressRoute systemu Azure | Yes
ILB | Tak
ELB | Yes
Azure Traffic Manager | Tak
Multi-NIC | Tak
Zastrzeżony adres IP | Tak
IPv4 | Yes
Zachowaj źródłowy adres IP | Tak
Punkty końcowe usługi dla sieci wirtualnej platformy Azure<br/> | Yes
Wydajniejsze sieci | Nie

## <a name="storage"></a>Magazyn
**Składnik** | **Obsługiwane**
--- | ---
Dysk dynamiczny | Dysk systemu operacyjnego musi być dyskiem podstawowym. <br/><br/>Dyski danych mogą być dyskami dynamicznymi
Konfiguracja dysku platformy docker | Nie
Host NFS | Tak dla oprogramowania VMware<br/><br/> Nie dla serwerów fizycznych
Sieć SAN (iSCSI/FC) hosta | Yes
Host vSAN | Tak dla oprogramowania VMware<br/><br/> N/d dla serwerów fizycznych
Host wielościeżkowego (MPIO) | Tak, przetestowane za pomocą DSM firmy Microsoft, EMC PowerPath 5.7 z dodatkiem SP4 EMC PowerPath DSM dla CLARiiON
Woluminy wirtualnego hosta (VVols) | Tak dla oprogramowania VMware<br/><br/> N/d dla serwerów fizycznych
Gość/serwera VMDK | Yes
Dysku udostępnionego klastra gościa/serwera | Nie
Gość/serwera zaszyfrowanego dysku | Nie
Gość/serwer systemu plików NFS | Nie
Gość/serwer iSCSI | Nie
Guest/server SMB 3.0 | Nie
Gość/serwera RDM | Tak<br/><br/> N/d dla serwerów fizycznych
Gość/serwera dysku > 1 TB | Tak<br/><br/>Do 4095 GB.<br/><br/> Dysk musi być większa niż 1024 MB.
Gość/serwera na dysku o rozmiarze sektora fizycznego logicznych i 4 k 4K | Tak
Dysk gościa/serwera z 4K logicznych i rozmiar sektora fizycznego 512 bajtów | Tak
Wolumin gościa/serwera z dysku rozłożonego > 4 TB <br/><br/>Zarządzanie woluminami logicznych (LVM)| Tak
Gość/server - miejsca do magazynowania | Nie
Gość/serwer Dodaj lub usuń gorąco dysku | Nie
Gość/server - wykluczyć dysk | Tak
Gość/serwera wielościeżkowego (MPIO) | Nie
Gość/serwera partycji GPT | Pięć partycjami są obsługiwane z poziomu [37 pakiet zbiorczy aktualizacji](https://support.microsoft.com/help/4508614/) (wersja 9,25 usługi mobilności) i nowszych wersjach. Wcześniej były cztery obsługiwane.
Rozruchu interfejsu EFI/UEFI gościa/serwera | -Obsługiwana, gdy używasz wersja usługi mobilności 9.13 lub nowszej.<br/> -Obsługiwana podczas migracji maszyn wirtualnych VMware lub serwery fizyczne z systemem Windows Server 2012 lub nowszym na platformie Azure.<br/> — Można replikować tylko maszyny wirtualne do migracji. Powrót po awarii do środowiska lokalnego nie jest obsługiwane.<br/> -NTFS tylko jest obsługiwane. <br/> — Rozmiar sektora dysku powinna być 512 bajtów na sektor fizycznych.

## <a name="replication-channels"></a>Kanały replikacji

|**Typ replikacji**   |**Obsługiwane**  |
|---------|---------|
|Odciążonych transferów danych (ODX)    |       Nie  |
|Rozmieszczanie w trybie offline        |   Nie      |
| Azure Data Box | Nie

## <a name="azure-storage"></a>Azure Storage

**Składnik** | **Obsługiwane**
--- | ---
Magazyn lokalnie nadmiarowy | Tak
Magazyn geograficznie nadmiarowy | Tak
Magazyn geograficznie nadmiarowy z dostępem do odczytu | Tak
Chłodny Magazyn | Nie
Magazynu gorącego| Nie
Obiekty BLOB typu Block | Nie
Szyfrowanie w spoczynku (SSE)| Tak
Premium Storage | Tak
Usługa import/export | Nie
Usługa Azure zapór usługi Storage dla sieci wirtualnych | Tak.<br/> Skonfigurowane na docelowe konto magazynu dla magazynu/pamięci podręcznej (używane do przechowywania danych replikacji).
Kont magazynu ogólnego przeznaczenia w wersji 2 (gorące i chłodne warstwy) | Nie

## <a name="azure-compute"></a>Usługa Azure compute

**Funkcja** | **Obsługiwane**
--- | ---
Zestawy dostępności | Tak
Strefy dostępności | Nie
HUB | Tak
Dyski zarządzane | Yes

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny Wirtualnej platformy Azure

Lokalne maszyny wirtualne replikowane na platformie Azure muszą spełniać wymagania maszyny Wirtualnej platformy Azure podsumowane w poniższej tabeli. Po uruchomieniu sprawdzania wymagań wstępnych w przypadku replikacji usługa Site Recovery wyboru zakończy się niepowodzeniem, jeśli niektóre wymagania nie są spełnione.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Sprawdź [obsługiwane systemy operacyjne](#replicated-machines) dla replikowane maszyny. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Architektura systemu operacyjnego gościa | 64-bitowych. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Rozmiar dysku systemu operacyjnego | Do 2048 GB. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Liczba dysków systemu operacyjnego | 1 | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Liczba dysków danych | 64 lub mniej. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Rozmiar dysku danych | Do 4095 GB. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Karty sieciowe | Wiele kart sieciowych, są obsługiwane. |
Udostępniony wirtualny dysk twardy | Nieobsługiwane. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Dysk FC | Nieobsługiwane. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
BitLocker | Nieobsługiwane. | Przed włączeniem replikacji dla maszyny, należy wyłączyć funkcję BitLocker. |
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/><br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa maszyny musi zaczynać i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach maszyny w usłudze Site Recovery.

## <a name="churn-limits"></a>Postęp dokonany w granicach

W poniższej tabeli przedstawiono limity usługi Azure Site Recovery. 
- Te limity są oparte na naszych testach, ale nie obejmują wszystkich możliwych aplikacji we/wy kombinacji.
- Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji.
- Aby uzyskać najlepsze wyniki, firma Microsoft zdecydowanie zaleca się uruchomienie [narzędzia planista wdrażania](site-recovery-deployment-planner.md)i dokładne przetestowanie aplikacji przy użyciu testy trybu failover w celu uzyskania prawdziwych informacji o wydajności dla twojej aplikacji.

**Lokalizacją docelową replikacji** | **Średni rozmiar źródłowych operacji we/wy na dysku** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB  | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |  336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |20 MB/s | 1684 GB na dysk


**Źródłowy współczynnik zmian danych** | **Limit maksymalny**
---|---
Średni współczynnik zmian danych na maszynę wirtualną| 25 MB/s
Szczytowy współczynnik zmian danych na wszystkich dyskach na maszynie wirtualnej | 54 MB/s
Maksymalny współczynnik zmian danych dziennie obsługiwany przez serwer przetwarzania | 2 TB

- Są to średnie wartości przy założeniu 30-procentowego nakładania się operacji we/wy.
- Usługa Site Recovery może obsługiwać większą przepływność na podstawie zakresu nakładania się na siebie, większego rozmiaru operacji zapisu i rzeczywistego zachowania związanego z obciążeniem operacji we/wy.
- Te liczby zakładają typowe zaległości wynoszące około pięć minut. Oznacza to, że przekazane dane są przetwarzane i punkt odzyskiwania jest tworzony w ciągu pięciu minut.

## <a name="vault-tasks"></a>Zadania magazynu

**Akcja** | **Obsługiwane**
--- | ---
Przenoszenie magazynu między grupami zasobów | Nie
Przenoszenie magazynu w obrębie i między subskrypcjami | Nie
Przenieś magazyn, sieci, maszyn wirtualnych platformy Azure między grupami zasobów | Nie
Przeniesienie magazynu, sieci, maszyn wirtualnych platformy Azure w ramach i w różnych subskrypcjach. | Nie


## <a name="obtain-latest-components"></a>Uzyskaj najnowsze składniki

**Nazwa** | **Opis** | **Szczegóły**
--- | --- | ---
Serwer konfiguracji | Zainstalowane w środowisku lokalnym.<br/> Służy do koordynowania komunikacji między serwerami VMware w środowisku lokalnym lub maszyny fizyczne i platformą Azure. | - [Dowiedz się więcej o](vmware-physical-azure-config-process-server-overview.md) serwera konfiguracji.<br/> - [Dowiedz się więcej o](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) uaktualnienie do najnowszej wersji.<br/> - [Dowiedz się więcej o](vmware-azure-deploy-configuration-server.md) Konfigurowanie serwera konfiguracji. 
Serwer przetwarzania | Domyślnie instalowany na serwerze konfiguracji.<br/> Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do platformy Azure.<br/> Wraz z rozwojem wdrożenia, można dodać dodatkowych serwerów przetwarzania do obsługi większych ilości ruchu związanego z replikacją. | - [Dowiedz się więcej o](vmware-physical-azure-config-process-server-overview.md) serwera przetwarzania.<br/> - [Dowiedz się więcej o](vmware-azure-manage-process-server.md#upgrade-a-process-server) uaktualnienie do najnowszej wersji.<br/> - [Dowiedz się więcej o](vmware-physical-large-deployment.md#set-up-a-process-server) Konfigurowanie serwerów przetwarzania skalowalnego w poziomie.
Usługa mobilności | Zainstalowana na maszynie Wirtualnej VMware lub serwery fizyczne, które mają być replikowane.<br/> Koordynuje replikację między lokalną VMware serwery/serwery fizyczne i platformą Azure.| - [Dowiedz się więcej o](vmware-physical-mobility-service-overview.md) usługi mobilności.<br/> - [Dowiedz się więcej o](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) uaktualnienie do najnowszej wersji.<br/> 



## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się, jak](tutorial-prepare-azure.md) przygotować platformę Azure na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware.

[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
