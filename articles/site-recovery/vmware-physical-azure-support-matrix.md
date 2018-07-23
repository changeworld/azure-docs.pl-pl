---
title: Macierz obsługi replikowania maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie obsługiwanych systemów operacyjnych oraz składników na potrzeby replikacji maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: raynew
ms.openlocfilehash: 516cb69042e923a46168c7655dc3e3010d9557e6
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173796"
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Macierz obsługi dla oprogramowania VMware oraz replikacji serwera fizycznego do platformy Azure

Ten artykuł zawiera podsumowanie obsługiwanych składników i ustawienia na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware na platformę Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).

## <a name="replication-scenario"></a>Scenariusz replikacji

**Scenariusz** | **Szczegóły**
--- | ---
Maszyny wirtualne VMware | Replikacji lokalnych maszyn wirtualnych VMware na platformę Azure. Można wdrożyć ten scenariusz, w witrynie Azure portal lub za pomocą [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Serwerów fizycznych | Replikacji lokalnych serwerów fizycznych systemu Windows/Linux na platformie Azure. Można wdrożyć ten scenariusz, w witrynie Azure portal.

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
Interfejs PowerCLI | [Interfejs PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") powinien być zainstalowany.
Role systemu Windows Server | Nie włączaj: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
Zasady grupy| Nie włączaj: <br> -Zapobiegaj dostępowi do wiersza polecenia. <br> -Uniemożliwić dostęp do narzędzi edycji rejestru. <br> — Logika zaufania dla plików załączników. <br> -Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Upewnij się, że:<br/><br/> -Nie masz istniejących domyślnej witryny sieci Web <br> -Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie  <br> -Nie masz istniejących witryny sieci Web/aplikacja nasłuchuje na porcie 443<br>
Typ karty NIC | Innego VMXNET3 (jeśli jest wdrażane jako maszyny Wirtualnej VMware)
Typ adresu IP | Statyczny
Porty | port 443 jest używany do organizowanie kanału sterowania)<br>9443 umożliwiający transport danych

## <a name="replicated-machines"></a>Replikowane maszyny

Usługa Site Recovery obsługuje dowolne obciążenia uruchomione na obsługiwanej maszynie replikacji.

**Składnik** | **Szczegóły**
--- | ---
Ustawienia komputera | Maszyny, które są replikowane do platformy Azure muszą spełniać [wymagania dotyczące usługi Azure](#azure-vm-requirements).
System operacyjny Windows | 64-bitowym systemie Windows Server 2016 (Server Core, serwer ze środowiskiem pulpitu), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z w co najmniej z dodatkiem SP1. </br></br>  [Windows Server 2008 z na co najmniej z dodatkiem SP2 — 32-bitowe i 64-bitowej](migrate-tutorial-windows-server-2008.md) (tylko migracji). </br></br> Windows 2016 Nano Server nie jest obsługiwane.
System operacyjny Linux | Red Hat Enterprise Linux: 5.2 do 5.11, 6.1-6.9, 7.0, 7.5 <br/><br/>CentOS: 5.2 do 5.11, 6.1-6.9, 7.0, 7.5 <br/><br/>Ubuntu 14.04 LTS server[ (obsługiwane wersje jądra)](#ubuntu-kernel-versions)<br/><br/>Serwer Ubuntu 16.04 LTS[ (obsługiwane wersje jądra)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (obsługiwane wersje jądra)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 z dodatkiem SP1, SP2, z dodatkiem SP3 [ (obsługiwane wersje jądra)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 z dodatkiem SP3, SUSE Linux Enterprise Server 11 z dodatkiem SP4 * </br></br>Oracle Enterprise Linux 6.4, 6.5 systemem Red Hat zgodne jądra lub podzielenie Enterprise jądra wersji 3 (UEK3) <br/><br/></br>* *Uaktualnianie replikowanych maszyn z systemem SUSE Linux Enterprise Server 11 SP3 do SP4 nie jest obsługiwane. Aby przeprowadzić uaktualnienie, wyłącz replikację i włącz go ponownie po uaktualnieniu.*


>[!NOTE]
>
> - W dystrybucjach systemu Linux są obsługiwane tylko podstawowe jądra, które są częścią dystrybucji wersji pomocniczej wersji/aktualizacji.
>
> - Uaktualnianie chronionych maszyn w głównych systemu Linux, wersji dystrybucji nie jest obsługiwane. Aby przeprowadzić uaktualnienie, wyłącz replikację, Uaktualnij system operacyjny i następnie włącz ponownie replikację.
>
> - Serwery z systemem Red Hat Enterprise Linux 5.2 5.11 lub CentOS 5.2 do 5.11 powinny mieć składniki Services(LIS) integracji systemu Linux zainstalowane w kolejności rozruchu na platformie Azure maszyn.

### <a name="ubuntu-kernel-versions"></a>Wersje jądra systemu Ubuntu


**Obsługiwana wersja** | **Wersja usługi Azure usługę mobilności usługi Site Recovery** | **Wersja jądra** |
--- | --- | --- |
14.04 LTS | 9.18 | 3.13.0-24-Generic do 3.13.0-153-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-130-generic |
14.04 LTS | 9.17 | 3.13.0-24-Generic do 3.13.0-149-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-127-generic |
14.04 LTS | 9.16 | 3.13.0-24-Generic do 3.13.0-144-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-119-generic |
14.04 LTS | 9.15. | 3.13.0-24-Generic do 3.13.0-144-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-119-generic |
|||
16.04 LTS | 9.18 | 4.4.0-21-Generic do 4.4.0-130-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic |
16.04 LTS | 9.17 | 4.4.0-21-Generic do 4.4.0-127-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-43-generic |
16.04 LTS | 9.16 | 4.4.0-21-Generic do 4.4.0-119-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-38-generic |
16.04 LTS | 9.15. | 4.4.0-21-Generic do 4.4.0-119-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-38-generic |


### <a name="debian-kernel-versions"></a>Wersje jądra systemu debian


**Obsługiwana wersja** | **Wersja usługi Azure usługę mobilności usługi Site Recovery** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | 9.17, 9.18 | 3.2.0-4-AMD64 do 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.15., 9.16 | 3.2.0-4-AMD64 do 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.17, 9.18 | 3.16.0-4-AMD64 do 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-AMD64 do 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.15. | 3.16.0-4-AMD64 do 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.5-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12. obsługiwane wersje jądra

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP3 z dodatkiem SP1, SP2) | 9.18 | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.96-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.85-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.138-94.39-default |

## <a name="linux-file-systemsguest-storage"></a>Magazyn gościa/systemów plików systemu Linux

**Składnik** | **Obsługiwane**
--- | ---
Systemy plików | ext3, ext4, XFS.
Menedżer woluminów | LVM2.
Urządzenia magazynujące Parawirtualne | Urządzenia eksportowane przez sterowniki parawirtualne nie są obsługiwane.
Blokuj wielu kolejek operacji We/Wy urządzenia | Nieobsługiwane.
Serwery fizyczne z kontrolera magazynu HP CCISS | Nieobsługiwane.
Katalogi | Te katalogi (jeśli skonfigurowany jako oddzielne partycje /-systemów plików) wszystkie muszą znajdować się na tym samym dysku systemu operacyjnego na serwerze źródłowym: / (root), / Boot/usr, /usr/local, /var, etc.</br></br> / Boot powinny znajdować się na partycji dysku i nie być woluminem LVM.<br/><br/>
Wymagania dotyczące wolnego miejsca| 2 GB na partycję/root <br/><br/> 250 MB do folderu instalacji
XFSv5 | Funkcje XFSv5 w systemach plików XFS, takie jak metadane sumy kontrolnej, są obsługiwane z usługi mobilności wersji 9.10 wartości. Użyj narzędzia xfs_info, aby sprawdzić superblock XFS dla partycji. Jeśli ftype jest ustawiona na 1, XFSv5 funkcje są w użyciu.

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
Punkty końcowe usługi sieci wirtualnej platformy Azure<br/> (bez zapór usługi Azure Storage) | Yes
Accelerated Networking | Nie

## <a name="storage"></a>Magazyn
**Składnik** | **Obsługiwane**
--- | ---
Host systemu plików NFS | Tak dla oprogramowania VMware<br/><br/> Nie dla serwerów fizycznych
Sieć SAN (iSCSI/FC) hosta | Yes
Host sieci vSAN | Tak dla oprogramowania VMware<br/><br/> N/d dla serwerów fizycznych
Host wielościeżkowego (MPIO) | Tak, przetestowane za pomocą DSM firmy Microsoft, EMC PowerPath 5.7 z dodatkiem SP4 EMC PowerPath DSM dla CLARiiON
Woluminy wirtualnego hosta (VVols) | Tak dla oprogramowania VMware<br/><br/> N/d dla serwerów fizycznych
Gość/serwera VMDK | Yes
Gość/serwera interfejsem EFI/UEFI| Partial (migracji do platformy Azure dla systemu Windows Server 2012 i nowsze maszyny wirtualne VMware tylko) </br></br> Patrz Uwaga na koniec tabeli
Dysku udostępnionego klastra gościa/serwera | Nie
Gość/serwera zaszyfrowanego dysku | Nie
Gość/serwer systemu plików NFS | Nie
Gość/serwera SMB 3.0 | Nie
Gość/serwera RDM | Yes<br/><br/> N/d dla serwerów fizycznych
Gość/serwera dysku > 1 TB | Yes<br/><br/>Do 4095 GB.
Gość/serwera na dysku o rozmiarze sektora fizycznego logicznych i 4 k 4K | Yes
Dysk gościa/serwera z 4K logicznych i rozmiar sektora fizycznego 512 bajtów | Yes
Wolumin gościa/serwera z dysku rozłożonego > 4 TB <br><br/>Zarządzanie woluminami logicznych (LVM)| Yes
Gość/server - miejsca do magazynowania | Nie
Gość/serwer Dodaj lub usuń gorąco dysku | Nie
Gość/server - wykluczyć dysk | Yes
Gość/serwera wielościeżkowego (MPIO) | Nie

> [!NOTE]
> UEFI rozruchu maszyn wirtualnych VMware z systemem Windows Server 2012 lub nowszej można poddać migracji do platformy Azure. Obowiązują następujące ograniczenia:

> - Jest obsługiwana tylko migracja na platformę Azure. Powrót po awarii do lokacji programu VMware w środowisku lokalnym nie jest obsługiwane.
> - Serwer nie może mieć więcej niż cztery partycje na dysku systemu operacyjnego.
> - Wymagana wersja usługi mobilności 9.13 lub nowszej.
> - Nie jest obsługiwane dla serwerów fizycznych.

## <a name="azure-storage"></a>Azure Storage

**Składnik** | **Obsługiwane**
--- | ---
Magazyn lokalnie nadmiarowy | Yes
Magazyn geograficznie nadmiarowy | Yes
Magazyn geograficznie nadmiarowy z dostępem do odczytu | Yes
Chłodny Magazyn | Nie
Magazynu gorącego| Nie
Obiekty BLOB typu Block | Nie
Szyfrowanie danych magazynowanych (szyfrowanie usługi Storage)| Yes
Premium Storage | Yes
Usługa import/export | Nie
Usługa Azure Storage zapory dla sieci wirtualnych skonfigurowanych na docelowe konto magazynu dla magazynu/pamięci podręcznej (używane do przechowywania danych replikacji) | Nie
Konta magazynu ogólnego przeznaczenia w wersji 2 (zarówno gorące i chłodne warstwy) | Nie

## <a name="azure-compute"></a>Usługa Azure compute

**Funkcja** | **Obsługiwane**
--- | ---
Zestawy dostępności | Yes
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


## <a name="vault-tasks"></a>Zadania magazynu

**Akcja** | **Obsługiwane**
--- | ---
Przenoszenie magazynu między grupami zasobów<br/><br/> W ramach i między subskrypcjami | Nie
Przenieś magazyn, sieci, maszyn wirtualnych platformy Azure między grupami zasobów<br/><br/> W ramach i między subskrypcjami | Nie


## <a name="mobility-service"></a>Usługa mobilności

**Nazwa** | **Opis** | **Najnowsza wersja** | **Szczegóły**
--- | --- | --- | --- | ---
Usługa Azure Site Recovery ujednoliconej konfiguracji | Służy do koordynowania komunikacji między serwerami VMware w środowisku lokalnym i platformą Azure <br/><br/> Zainstalowana na lokalnych serwerów VMware | 9.12.4653.1 (dostępnej w portalu) | [Najnowsze funkcje i poprawki](https://aka.ms/latest_asr_updates)
Usługa mobilności | Koordynuje replikację między lokalną VMware serwery/serwery fizyczne i witryną Azure/pomocniczy<br/><br/> Zainstalowana na maszynie Wirtualnej VMware lub serwery fizyczne, które mają być replikowane | 9.12.4653.1 (dostępnej w portalu) | [Najnowsze funkcje i poprawki](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się, jak](tutorial-prepare-azure.md) przygotować platformę Azure na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware.
