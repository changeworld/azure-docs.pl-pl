---
title: Macierz obsługi na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie obsługiwanych systemów operacyjnych i składników do odzyskiwania po awarii maszyn wirtualnych VMware i serwera fizycznego na platformie Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: raynew
ms.openlocfilehash: 8be028d11d0778c2b67788029aa400ffd3b98cb4
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872919"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Macierz obsługi dla odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure

Ten artykuł zawiera podsumowanie obsługiwanych składników i ustawienia na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware na platformę Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).

Aby rozpocząć korzystanie z usługi Azure Site Recovery przy użyciu najprostszy scenariusz wdrażania, odwiedź nasz [samouczki](tutorial-prepare-azure.md). Dowiedz się więcej na temat architektury usługi Azure Site Recovery [tutaj](vmware-azure-architecture.md).

## <a name="deployment-scenario"></a>Scenariusz wdrażania

**Scenariusz** | **Szczegóły**
--- | ---
Odzyskiwanie po awarii maszyn wirtualnych programu VMware | Replikacji lokalnych maszyn wirtualnych VMware na platformę Azure. Można wdrożyć ten scenariusz, w witrynie Azure portal lub za pomocą [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Odzyskiwanie po awarii serwerów fizycznych | Replikacji lokalnych serwerów fizycznych systemu Windows/Linux na platformie Azure. Można wdrożyć ten scenariusz, w witrynie Azure portal.

## <a name="on-premises-virtualization-servers"></a>Lokalne serwery wirtualizacji

**Serwer** | **Wymagania** | **Szczegóły**
--- | --- | ---
VMware | 6.7 serwera vCenter 6.5, 6.0 lub 5.5 lub vSphere 6.7, 6.5, 6.0 lub 5.5 | Firma Microsoft zaleca użycie serwera vCenter.<br/><br/> Zaleca się, że hosty vSphere i vCenter Server znajdują się w tej samej sieci, co serwer przetwarzania. Domyślnie składniki serwera przetwarzania działa na serwerze konfiguracji, więc będzie to sieci, która umożliwia skonfigurowanie serwera konfiguracji, chyba że jest skonfigurowanie serwera dedykowanego procesu.
Fizyczne | ND

## <a name="site-recovery-configuration-server"></a>Serwera konfiguracji usługi Site Recovery

Serwer konfiguracji jest maszyna w środowisku lokalnym, na której działa składniki usługi Site Recovery, w tym serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy. Dla replikacji VMware należy skonfigurować serwer konfiguracji ze wszystkimi wymaganiami za pomocą szablonu pakietu OVF do utworzenia maszyny Wirtualnej VMware. W przypadku replikacji serwera fizycznego konfigurowania komputera serwera konfiguracji ręcznie.

**Składnik** | **Wymagania**
--- |---
Rdzenie procesora CPU | 8
Pamięć RAM | 16 GB
Liczba dysków | 3 dyskami<br/><br/> Dyski obejmują dysku systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania na potrzeby powrotu po awarii.
Wolne miejsce na dysku | 600 GB miejsca na wymaganą dla pamięci podręcznej serwera przetwarzania.
Wolne miejsce na dysku | 600 GB miejsca na wymaganą do przechowywania dysków.
System operacyjny  | Windows Server 2012 R2 lub Windows Server 2016 |
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
Interfejs PowerCLI | [Interfejs PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") nie jest wymagane dla serwera konfiguracji z wersjami z [: 9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery).
Role systemu Windows Server | Nie włączaj: <br/> - Active Directory Domain Services <br/>- Internet Information Services <br/> - Hyper-V |
Zasady grupy| Nie włączaj: <br/> -Zapobiegaj dostępowi do wiersza polecenia. <br/> -Uniemożliwić dostęp do narzędzi edycji rejestru. <br/> — Logika zaufania dla plików załączników. <br/> -Włącz wykonywanie skryptu. <br/> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Upewnij się, że:<br/><br/> -Nie masz istniejących domyślnej witryny sieci Web <br/> -Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie  <br/> -Nie masz istniejących witryny sieci Web/aplikacja nasłuchuje na porcie 443<br/>
Typ karty NIC | Innego VMXNET3 (jeśli jest wdrażane jako maszyny Wirtualnej VMware)
Typ adresu IP | Statyczny
Porty | port 443 jest używany do organizowanie kanału sterowania)<br/>9443 umożliwiający transport danych

## <a name="replicated-machines"></a>Replikowane maszyny

Usługa Site Recovery obsługuje dowolne obciążenia uruchomione na obsługiwanej maszynie replikacji.

**Składnik** | **Szczegóły**
--- | ---
Ustawienia komputera | Maszyny, które są replikowane do platformy Azure muszą spełniać [wymagania dotyczące usługi Azure](#azure-vm-requirements).
Obciążenie maszyny | Usługa Site Recovery obsługuje replikację z dowolnego obciążenia (np. usługi Active Directory, programu SQL server itp.,) uruchomione na obsługiwanej maszynie. [Dowiedz się więcej](https://aka.ms/asr_workload).
System operacyjny Windows | 64-bitowym systemie Windows Server 2016 (Server Core, serwer ze środowiskiem pulpitu), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z w co najmniej z dodatkiem SP1. </br></br>  [Windows Server 2008 z na co najmniej z dodatkiem SP2 — 32-bitowe i 64-bitowej](migrate-tutorial-windows-server-2008.md) (tylko migracji). </br></br> Windows 2016 Nano Server nie jest obsługiwane.
Architektura systemu operacyjnego Linux | Obsługiwane jest tylko 64-bitowym systemie. 32-bitowy system nie jest obsługiwana.
System operacyjny Linux | Red Hat Enterprise Linux: 5.2 i 5.11<b>\*\*</b>, 6.1, 6.10<b>\*\*</b>, 7.0, 7.6 <br/><br/>CentOS: 5.2 i 5.11<b>\*\*</b>, 6.1, 6.10<b>\*\*</b>, 7.0, 7.6 <br/><br/>Ubuntu 14.04 LTS server [(obsługiwane wersje jądra)](#ubuntu-kernel-versions)<br/><br/>Serwer Ubuntu 16.04 LTS [(obsługiwane wersje jądra)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8 [(obsługiwane wersje jądra)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 z dodatkiem SP1, SP2, z dodatkiem SP3, z dodatkiem SP4 [(obsługiwane wersje jądra)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>, SUSE Linux Enterprise Server 11 z dodatkiem SP4 * </br></br>Oracle Linux 6.4, 6.5, 6.6, 6.7, 6.8, 6,9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6 systemem Red Hat zgodne jądra lub podzielenie Enterprise jądra w wersji 3, 4 i 5 (UEK3, UEK4, UEK5) <br/><br/></br>— Uaktualnianie replikowanych maszyn z systemem SUSE Linux Enterprise Server 11 SP3 do SP4 nie jest obsługiwane. Aby przeprowadzić uaktualnienie, wyłącz replikację i włącz go ponownie po uaktualnieniu.</br></br> - [Dowiedz się więcej](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) dotyczące pomocy technicznej dla systemu Linux i technologii typu open source na platformie Azure. Usługa Site Recovery organizuje trybu failover, aby uruchomić serwerów z systemem Linux na platformie Azure. Jednak dostawcy systemu Linux może ograniczyć obsługę tylko wersje dystrybucji, które jeszcze nie osiągnęły wycofanych z eksploatacji.<br/><br/> -W dystrybucjach systemu Linux obsługiwane są tylko podstawowe jądra, które są częścią dystrybucji wersji pomocniczej wersji/aktualizacji.<br/><br/> — Uaktualnianie chronionych maszyn w głównych systemu Linux, wersji dystrybucji nie jest obsługiwane. Aby przeprowadzić uaktualnienie, wyłącz replikację, Uaktualnij system operacyjny i następnie włącz ponownie replikację.<br/><br/> — Serwerach z systemem Red Hat Enterprise Linux 5.2 5.11 lub CentOS 5.2 5.11 powinny mieć [składniki usługi LIS (Linux Integration)](https://www.microsoft.com/download/details.aspx?id=55106) zainstalowane maszyn rozruchu na platformie Azure.


### <a name="ubuntu-kernel-versions"></a>Wersje jądra systemu Ubuntu


**Obsługiwana wersja** | **Wersja usługi Azure usługę mobilności usługi Site Recovery** | **Wersja jądra** |
--- | --- | --- |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-Generic do 3.13.0-167-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-143-generic,<br/>4.15.0-1023-Azure do 4.15.0-1040-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-Generic do 3.13.0-165-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-142-generic,<br/>4.15.0-1023-Azure do 4.15.0-1037-azure |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-Generic do 3.13.0-164-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-140-generic,<br/>4.15.0-1023-Azure do 4.15.0-1036-azure |
14.04 LTS | [9.21][9.21 UR] | 3.13.0-24-Generic do 3.13.0-163-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-140-generic,<br/>4.15.0-1023-Azure do 4.15.0-1035-azure |
|||
16.04 LTS | [9.23] [9,24 Z PAKIETEM ZBIORCZYM AKTUALIZACJI] | 4.4.0-21-Generic do 4.4.0-143-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-46-generic<br/>4.11.0-1009-Azure do 4.11.0-1018-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1040-azure|
16.04 LTS | [9.23][9.23 UR] | 4.4.0-21-Generic do 4.4.0-142-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-45-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1037-azure|
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-Generic do 4.4.0-140-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-43-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1036-azure|
16.04 LTS | [9.21][9.21 UR] | 4.4.0-21-Generic do 4.4.0-140-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-42-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1035-azure|
16.04 LTS | [9.20][9.20 UR] | 4.4.0-21-Generic do 4.4.0-138-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-38-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1025-azure|

### <a name="debian-kernel-versions"></a>Wersje jądra systemu debian


**Obsługiwana wersja** | **Wersja usługi Azure usługę mobilności usługi Site Recovery** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR],[9.23][9.23 UR], [9,24] [9.24 Z PAKIETEM ZBIORCZYM AKTUALIZACJI]| 3.2.0-4-AMD64 do 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.21][9.21 UR],[9.22][9.22 UR],[9.23][9.23 UR], [9,24] [9.24 Z PAKIETEM ZBIORCZYM AKTUALIZACJI] | 3.16.0-4-AMD64 do 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12. obsługiwane wersje jądra

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.24][9.24 UR] | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.101-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.175-94.79-default</br></br>Z dodatkiem SP4 4.12.14-94.41-default do 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.23][9.23 UR] | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.101-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.162-94.69-default</br></br>Z dodatkiem SP4 4.12.14-94.41-default do 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | [9.22][9.22 UR] | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.98-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | [9.21][9.21 UR] | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.98-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.156-94.72-default |


## <a name="linux-file-systemsguest-storage"></a>Magazyn gościa/systemów plików systemu Linux

**Składnik** | **Obsługiwane**
--- | ---
Systemy plików | ext3, ext4, XFS
Menedżer woluminów | Przed [9.20 wersji](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. LVM jest obsługiwane. <br/> 2. / boot na woluminie LVM nie jest obsługiwane. <br/> 3. Wiele dysków systemu operacyjnego nie są obsługiwane.<br/><br/>Z [9.20 wersji](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) lub nowszy, / Boot na LVM jest obsługiwana. Wiele dysków systemu operacyjnego nie są obsługiwane.
Urządzenia magazynujące Parawirtualne | Urządzenia eksportowane przez sterowniki parawirtualne nie są obsługiwane.
Blokuj wielu kolejek operacji We/Wy urządzenia | Nieobsługiwane.
Serwery fizyczne z kontrolera magazynu HP CCISS | Nieobsługiwane.
Konwencja nazewnictwa punktu instalacji/urządzenia | Nazwa urządzenia lub nazwę punktu instalacji powinny być unikatowe. Upewnij się, że żadna para urządzeń/punktów instalacji nie ma takich samych nazw, z uwzględnieniem wielkości liter. </br> Przykład: Nazewnictwo dwóch urządzeń z tej samej maszyny wirtualnej jako *urządzenia 1* i *urządzenia 1* jest niedozwolone.
Katalogi | Przed [9.20 wersji](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. Następujące katalogi (jeśli skonfigurowany jako oddzielne partycje /-systemów plików) wszystkie muszą znajdować się na tym samym dysku systemu operacyjnego na serwerze źródłowym: / (root), / Boot/usr, /usr/local, /var, etc.</br>2. / boot powinny znajdować się na partycji dysku i nie LVM woluminu.<br/><br/> Z [9.20 wersji](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) lub nowszy, powyżej ograniczenia nie mają zastosowania. / Boot woluminie LVM na więcej niż jeden dysków nie jest obsługiwane.
Katalog rozruchowy | Wiele dysków rozruchowych na maszynie wirtualnej nie jest obsługiwana. <br/><br/> Nie można chronić maszyny bez dysk rozruchowy
Wymagania dotyczące wolnego miejsca| 2 GB na partycję/root <br/><br/> 250 MB do folderu instalacji
XFSv5 | Funkcje XFSv5 w systemach plików XFS, takie jak metadane sumy kontrolnej, są obsługiwane z usługi mobilności wersji 9.10 wartości. Użyj narzędzia xfs_info, aby sprawdzić superblock XFS dla partycji. Jeśli `ftype` jest równa 1, a następnie są używane funkcje XFSv5.
BTRFS |Od wersji 9.22 BTRFS jest obsługiwana, z wyjątkiem następujących scenariuszy</br>Po zmianie po włączeniu ochrony podrzędnych wolumin systemu plików BTRFS BTRFS nie jest obsługiwane. </br>Jeśli system plików BTRFS jest rozłożona na wiele dysków, BTRFS nie jest obsługiwane.</br>Jeśli system plików BTRFS obsługuje RAID, BTRFS nie jest obsługiwane.

## <a name="vmdisk-management"></a>Zarządzanie maszyna wirtualna/dysk

**Akcja** | **Szczegóły**
--- | ---
Zmiana rozmiaru dysku na zreplikowanej maszyny Wirtualnej | Obsługiwane.
Dodaj dysk na replikowanej maszynie Wirtualnej | Wyłącz replikację dla maszyny Wirtualnej, Dodaj dysk, a następnie ponownie włączyć replikację. Dodawanie dysku na replikującej maszynie Wirtualnej nie jest obecnie obsługiwane.

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
Wewnętrzny moduł równoważenia obciążenia | Yes
ELB | Yes
Azure Traffic Manager | Yes
Multi-NIC | Yes
Zastrzeżony adres IP | Yes
Protokół IPv4 | Yes
Zachowaj źródłowy adres IP | Yes
Punkty końcowe usługi sieci wirtualnej platformy Azure<br/> | Yes
Accelerated Networking | Nie

## <a name="storage"></a>Magazyn
**Składnik** | **Obsługiwane**
--- | ---
Dysk dynamiczny | Operacja dysku systemowego musi być dyskiem podstawowym. <br/><br/>Dyski danych mogą być dyskami dynamicznymi
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
Guest/server SMB 3.0 | Nie
Gość/serwera RDM | Yes<br/><br/> N/d dla serwerów fizycznych
Gość/serwera dysku > 1 TB | Yes<br/><br/>Do 4095 GB.<br/><br/> Dysk musi być większa niż 1024 MB.
Gość/serwera na dysku o rozmiarze sektora fizycznego logicznych i 4 k 4K | Yes
Dysk gościa/serwera z 4K logicznych i rozmiar sektora fizycznego 512 bajtów | Yes
Wolumin gościa/serwera z dysku rozłożonego > 4 TB <br/><br/>Zarządzanie woluminami logicznych (LVM)| Yes
Gość/server - miejsca do magazynowania | Nie
Gość/serwer Dodaj lub usuń gorąco dysku | Nie
Gość/server - wykluczyć dysk | Yes
Gość/serwera wielościeżkowego (MPIO) | Nie
Rozruchu interfejsu EFI/UEFI gościa/serwera | Obsługiwane podczas migracji maszyn wirtualnych VMware lub serwery fizyczne z systemem Windows Server 2012 lub nowszego na platformie Azure.<br/><br/> Można replikować tylko maszyny wirtualne do migracji. Powrót po awarii do środowiska lokalnego nie jest obsługiwane.<br/><br/> Serwer nie może mieć więcej niż cztery partycje na dysku systemu operacyjnego.<br/><br/> Wymagana wersja usługi mobilności 9.13 lub nowszej.<br/><br/> Obsługiwane jest tylko systemu plików NTFS.

## <a name="replication-channels"></a>Kanały replikacji

|**Typ replikacji**   |**Obsługiwane**  |
|---------|---------|
|Odciążonych transferów danych (ODX)    |       Nie  |
|Rozmieszczanie w trybie offline        |   Nie      |
| Azure Data Box | Nie


## <a name="azure-storage"></a>Azure Storage

**Składnik** | **Obsługiwane**
--- | ---
Magazyn lokalnie nadmiarowy | Yes
Magazyn geograficznie nadmiarowy | Yes
Magazyn geograficznie nadmiarowy z dostępem do odczytu | Yes
Chłodny Magazyn | Nie
Magazynu gorącego| Nie
Blokowe obiekty blob | Nie
Szyfrowanie danych magazynowanych (szyfrowanie usługi Storage)| Yes
Premium Storage | Yes
Usługa import/export | Nie
Usługa Azure Storage zapory dla sieci wirtualnych skonfigurowanych na docelowe konto magazynu dla magazynu/pamięci podręcznej (używane do przechowywania danych replikacji) | Yes
Konta magazynu ogólnego przeznaczenia w wersji 2 (zarówno gorące i chłodne warstwy) | Nie

## <a name="azure-compute"></a>Usługa Azure compute

**Funkcja** | **Obsługiwane**
--- | ---
Zestawy dostępności | Yes
Strefy dostępności | Nie
HUB | Yes
Dyski zarządzane | Yes

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny Wirtualnej platformy Azure

Lokalnych maszyn wirtualnych, które są replikowane do platformy Azure muszą spełniać wymagania maszyny Wirtualnej platformy Azure podsumowane w poniższej tabeli. Po uruchomieniu sprawdzania wymagań wstępnych Site Recovery zakończy się niepowodzeniem, jeśli niektóre wymagania nie są spełnione.

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

## <a name="azure-site-recovery-churn-limits"></a>Usługa Azure Site Recovery postęp dokonany w granicach

W poniższej tabeli przedstawiono limity usługi Azure Site Recovery. Limity te są oparte na naszych testach, ale nie obejmują wszystkich możliwych kombinacji operacji we/wy aplikacji. Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji. Aby uzyskać najlepsze wyniki, zdecydowanie zaleca się [Uruchom narzędzie planista wdrażania](site-recovery-deployment-planner.md) i dokładne przetestowanie aplikacji przez wystawienie testowy tryb failover w celu uzyskania prawdziwych informacji o wydajności aplikacji.

**Cel magazynu replikacji** | **Średni rozmiar źródłowych operacji we/wy na dysku** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
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

Są to średnie wartości przy założeniu 30-procentowego nakładania się operacji we/wy. Usługa Site Recovery może obsługiwać większą przepływność na podstawie zakresu nakładania się na siebie, większego rozmiaru operacji zapisu i rzeczywistego zachowania związanego z obciążeniem operacji we/wy. Poprzednie liczby zakładają typowe zaległości wynoszące około pięć minut. Oznacza to, że przekazane dane są przetwarzane i punkt odzyskiwania jest tworzony w ciągu pięciu minut.

## <a name="vault-tasks"></a>Zadania magazynu

**Akcja** | **Obsługiwane**
--- | ---
Przenoszenie magazynu między grupami zasobów<br/><br/> W ramach i między subskrypcjami | Nie
Przenieś magazyn, sieci, maszyn wirtualnych platformy Azure między grupami zasobów<br/><br/> W ramach i między subskrypcjami | Nie


## <a name="download-latest-azure-site-recovery-components"></a>Pobierz najnowsze składniki usługi Azure Site Recovery

**Nazwa** | **Opis** | **Instrukcje jej pobrania najnowszej wersji**
--- | --- | ---
Serwer konfiguracji | Służy do koordynowania komunikacji między serwerami VMware w środowisku lokalnym i platformą Azure <br/><br/> Zainstalowana na lokalnych serwerów VMware | Aby uzyskać więcej informacji, odwiedź nasze wskazówki na [nową instalację](vmware-azure-deploy-configuration-server.md) i [Uaktualnij istniejący składnik do najnowszej wersji](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).
Serwer przetwarzania|Domyślnie instalowany na serwerze konfiguracji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła je do usługi Azure Storage. Wraz z rozwojem wdrożenia, możesz dodać dodatkowe, oddzielny proces serwerów w celu obsługi większych ilości ruchu związanego z replikacją.| Aby uzyskać więcej informacji, odwiedź nasze wskazówki na [nową instalację](vmware-azure-set-up-process-server-scale.md) i [Uaktualnij istniejący składnik do najnowszej wersji](vmware-azure-manage-process-server.md#upgrade-a-process-server).
Usługa mobilności | Koordynuje replikację między lokalną VMware serwery/serwery fizyczne i witryną Azure/pomocniczy<br/><br/> Zainstalowana na maszynie Wirtualnej VMware lub serwery fizyczne, które mają być replikowane | Aby uzyskać więcej informacji, odwiedź nasze wskazówki na [nową instalację](vmware-azure-install-mobility-service.md) i [Uaktualnij istniejący składnik do najnowszej wersji](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal).

Aby dowiedzieć się więcej na temat najnowszych funkcji, odwiedź stronę [najnowsze informacje o wersji](https://aka.ms/ASR_latest_release_notes).


## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się, jak](tutorial-prepare-azure.md) przygotować platformę Azure na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware.

[9.23 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
