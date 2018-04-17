---
title: Tabela wsparcia dla replikację maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Podsumowanie obsługiwanych systemów operacyjnych oraz składników do replikowania maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2018
ms.author: raynew
ms.openlocfilehash: b2a6e3052c64ab6a2865a0c24a4876cb2b98d1a8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Tabela wsparcia dla VMware i replikacja serwerów fizycznych do platformy Azure

Ten artykuł zawiera podsumowanie obsługiwanych składników i ustawienia odzyskiwania po awarii maszyn wirtualnych VMware do platformy Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

## <a name="replication-scenario"></a>Scenariusz replikacji

**Scenariusz** | **Szczegóły**
--- | ---
Maszyny wirtualne VMware | Replikacja lokalnych maszyn wirtualnych VMware do platformy Azure. Można wdrożyć ten scenariusz, w portalu Azure lub za pomocą programu PowerShell.
Serwerów fizycznych | Replikacja serversto fizycznych lokalnego systemu Windows i Linux Azure. Można wdrożyć ten scenariusz, w portalu Azure.

## <a name="on-premises-virtualization-servers"></a>Lokalnych serwerów wirtualizacji

**Serwer** | **Wymagania** | **Szczegóły**
--- | --- | ---
VMware | vCenter Server 6.5 w wersji 6.0 lub 5.5 lub vSphere 6.5, 6.0 lub 5.5 | Firma Microsoft zaleca użycie serwera vCenter.<br/><br/> Zaleca się, że hostach vSphere i vCenter Server znajdują się w tej samej sieci co serwer przetwarzania. Domyślnie proces składniki serwera jest uruchamiany na serwerze konfiguracji, chyba że jest skonfigurowanie serwera dedykowanego procesu będzie sieci, w którym skonfigurować serwer konfiguracji. 
Fizyczne | ND

## <a name="site-recovery-configuration-server"></a>Serwer konfiguracji odzyskiwania lokacji

Serwer konfiguracji jest na lokalnej maszynie z systemem składników usługi Site Recovery, w tym konfiguracji serwera, serwer przetwarzania i główny serwer docelowy. Dla replikacji maszyn wirtualnych VMware należy skonfigurować serwer konfiguracji wszystkich wymagań, za pomocą szablonu pakietu OVF do utworzenia maszyny Wirtualnej VMware. Replikacja serwerów fizycznych należy skonfigurować z maszyną serwera konfiguracji ręcznie.

**Składnik** | **Wymagania**
--- |---
Rdzenie procesora CPU | 8 
Pamięć RAM | 12 GB
Liczba dysków | 3 dysków<br/><br/> Dyski zawierają dysku systemu operacyjnego, dysku pamięci podręcznej serwera przetwarzania i przechowywania dysku powrotu po awarii.
Wolne miejsce na dysku | 600 GB miejsca wymaganego dla pamięci podręcznej serwera przetwarzania.
Wolne miejsce na dysku | 600 GB miejsca wymaganego dla dysku przechowywania.
System operacyjny  | Windows Server 2012 R2 lub Windows Server 2016 | 
Ustawienia regionalne systemu operacyjnego | Angielski (en-us) 
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") powinna zostać zainstalowana.
Role systemu Windows Server | Nie włączaj: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
Zasady grupy| Nie włączaj: <br> -Uniemożliwić dostęp do wiersza polecenia. <br> -Uniemożliwić dostęp do narzędzia do edycji rejestru. <br> — Logika zaufania dla załączników. <br> -Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Upewnij się, że:<br/><br/> -Nie masz istniejących domyślnej witryny sieci Web <br> -Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie  <br> -Nie masz istniejących witryny sieci Web/aplikacja nasłuchuje na porcie 443<br>
Typ karty Sieciowej | VMXNET3 (jeśli są wdrażane jako maszyny Wirtualnej VMware) 
Typ adresu IP | Statyczny 
Porty | używane do sterowania kanału aranżacji 443)<br>9443 używany do transportu danych

## <a name="replicated-machines"></a>Replikowane maszyny

Usługa Site Recovery obsługuje replikację dowolne obciążenia uruchomione na obsługiwanej maszynie.

**Składnik** | **Szczegóły**
--- | ---
Ustawienia komputera | Komputery, które są replikowane do platformy Azure musi spełniać [wymagania dotyczące usługi Azure](#azure-vm-requirements).
System operacyjny Windows | 64-bitowego systemu Windows Server 2016 (instalacja Server Core, serwer z środowisko pulpitu), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z na co najmniej z dodatkiem SP1. Windows 2016 Nano Server nie jest obsługiwana.
System operacyjny Linux | Red Hat Enterprise Linux: 5.2-5.11, 6.1-6.9, 7.0 do 7,4 <br/><br/>CentOS: 5.2-5.11, 6.1-6.9, 7.0 do 7,4 <br/><br/>Ubuntu 14.04 LTS serwera[ (obsługiwane wersje jądra)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server[ (obsługiwane wersje jądra)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (obsługiwane wersje jądra)](#debian-kernel-versions)<br/><br/>Oracle Linux przedsiębiorstwa 6.4, 6.5 systemem Red Hat jądra zgodny lub podzielenie Enterprise jądra wersji 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4 <br/><br/>Uaktualnienie replikowanych maszyn z dodatkiem SP3 do SP4 nie jest obsługiwane. Aby przeprowadzić uaktualnienie, wyłącz replikacji i włącz ją ponownie po uaktualnieniu.

>[!NOTE]
>
> - Na dystrybucje systemu Linux obsługiwane są tylko standardowych jądra, które są częścią dystrybucji wersja pomocnicza wersji/aktualizacji.
>
> - Uaktualnianie chronionych maszyn między Linux główne wersje dystrybucji nie jest obsługiwane. Do uaktualnienia, wyłącz replikację, Uaktualnij system operacyjny, a następnie włącz ponownie replikacji.
>

### <a name="ubuntu-kernel-versions"></a>Ubuntu wersji jądra


**Obsługiwana wersja** | **Wersja usługi mobilności odzyskiwania lokacji Azure** | **Wersja jądra** |
--- | --- | --- |
14.04 LTS | 9.11 | 3.13.0-24-Generic do 3.13.0-128-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-Generic do 3.13.0-132-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-Generic do 3.13.0-137-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-104-generic |
14.04 LTS | : 9,14 | 3.13.0-24-Generic do 3.13.0-142-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-116-generic |
16.04 LTS | 9.11 | 4.4.0-21-Generic do 4.4.0-91-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-Generic do 4.4.0-96-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-Generic do 4.4.0-104-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic |
16.04 LTS | : 9,14 | 4.4.0-21-Generic do 4.4.0-116-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-36-generic,<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1011-azure |


### <a name="debian-kernel-versions"></a>Wersje debian jądra


**Obsługiwana wersja** | **Wersja usługi mobilności odzyskiwania lokacji Azure** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | : 9,14 | 3.2.0-4-AMD64 do 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 8 | : 9,14 | 3.16.0-4-AMD64 do 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.5-amd64 |


## <a name="linux-file-systemsguest-storage"></a>Magazyn gościa/systemy plików systemu Linux

**Składnik** | **Obsługiwane**
--- | ---
systemy plików | ext3, ext4, XFS.
Menedżer woluminów | LVM2.
Oprogramowanie wielościeżkowego | Mapowanie urządzeń.
Urządzenia magazynujące parawirtualnego systemu | Urządzenia eksportowane przez sterowniki parawirtualne nie są obsługiwane.
Blokuj wielu kolejki We/Wy urządzenia | Nieobsługiwane.
Serwery fizyczne z kontrolera magazynu HP CCISS | Nieobsługiwane.
Katalogi | Te katalogi (jeśli skonfigurowany jako osobne partycje /-systemów plików) wszystkie muszą być na tym samym dysku systemu operacyjnego na serwerze źródłowym: / (root), / Boot usr, /usr/local, /var, etc.</br></br> / Boot powinien znajdować się na partycji dysku i nie LVM woluminu.<br/><br/>
Wymagania dotyczące wolnego miejsca| 2 GB na partycji/root <br/><br/> 250 MB na folder instalacji
XFSv5 | Funkcje XFSv5 na XFS systemów plików, takich jak metadanych sumy kontrolnej, są obsługiwane z usługą mobilności wersji 9.10 ponownego udostępnienia. Użyj narzędzia xfs_info, aby sprawdzić superblock XFS dla partycji. Jeśli ftype jest ustawiona na 1, XFSv5 funkcje są w użyciu.



## <a name="network"></a>Sieć

**Składnik** | **Obsługiwane**
--- | ---
Tworzenie zespołu kart sieciowych sieci hosta | Obsługiwane w maszynach wirtualnych VMware. <br/><br/>Nie jest obsługiwane dla komputera fizycznego replikacji.
Host sieci VLAN | Tak.
Sieci hostów protokołu IPv4 | Tak.
Sieci hostów protokołu IPv6 | Nie.
Tworzenie zespołu kart sieciowych sieci gościa serwer | Nie.
Gość serwera sieci IPv4 | Tak.
Gość serwera sieci IPv6 | Nie.
Gość serwera sieci statycznego adresu IP (z systemem Windows) | Tak.
Gość serwera sieci statycznego adresu IP (Linux) | Tak. <br/><br/>Maszyny wirtualne są skonfigurowane do korzystania z protokołu DHCP w przypadku powrotu po awarii.
Wiele kart sieciowych sieci z serwerem gościa | Tak.


## <a name="azure-vm-network-after-failover"></a>Sieć maszyny Wirtualnej platformy Azure (po trybu failover)

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
Punkty końcowe usługi sieci wirtualnej platformy Azure<br/><br/> (Usługa azure Storage zapory i sieci wirtualne) | Nie

## <a name="storage"></a>Magazyn
**Składnik** | **Obsługiwane**
--- | ---
Host systemu plików NFS | Tak, aby VMware<br/><br/> Nie dla serwerów fizycznych
Sieć SAN (ISCSI) hosta | Yes
Wielościeżkowe hosta (MPIO) | Tak, poddane DSM firmy Microsoft, EMC PowerPath 5.7 z dodatkiem SP4 EMC PowerPath DSM dla CLARiiON
Gość i serwerem VMDK | Yes
Gość i serwerem interfejsem EFI/UEFI| Partial (migracja do platformy Azure dla systemu Windows Server 2012 i nowszych maszyn wirtualnych VMware tylko) </br></br> Zobacz uwagę na końcu tabeli
Dysk udostępniony klaster gościa serwera | Nie
Gość i serwerem zaszyfrowanego dysku | Nie
Gość serwera systemu plików NFS | Nie
Gość serwera SMB 3.0 | Nie
Gość i serwerem RDM | Yes<br/><br/> Brak serwerów fizycznych
Gość i serwerem dysku > 1 TB | Yes<br/><br/>Do 4,095 GB
Gość i serwerem dysku o rozmiarze sektora fizycznego logicznych i 4 k 4K | Yes
Dysk serwera gościa z logicznych 4K i rozmiar sektora fizycznego 512 bajtów | Yes
Wolumin serwera gościa z dysku rozłożone > 4 TB <br><br/>Zarządzanie woluminami logiczne (LVM)| Yes
Gość/server - miejsca do magazynowania | Nie
Gość i serwerem dodawania i usuwania gorących dysku | Nie
Gość/server — Wyklucz dysku | Yes
Wielościeżkowe gościa i serwerem (MPIO) | ND

> [!NOTE]
> UEFI rozruchu maszyn wirtualnych VMware z systemem Windows Server 2012 lub nowszym można poddać migracji do usługi Azure. Następujące ograniczenia:

> - Tylko migracja do systemu Azure jest obsługiwana. Powrót po awarii do lokacji lokalnej VMware nie jest obsługiwana.
> - Serwer nie powinien zawierać więcej niż czterech partycji na dysku systemu operacyjnego.
> - Wymagana wersja usługi mobilności 9.13 lub nowszej.
> - Nie jest obsługiwana na serwerach fizycznych.

## <a name="azure-storage"></a>Azure Storage

**Składnik** | **Obsługiwane**
--- | ---
Magazyn lokalnie nadmiarowy | Yes
Magazyn geograficznie nadmiarowy | Yes
Dostęp do odczytu magazynu geograficznie nadmiarowego | Yes
Magazynu chłodnego | Nie
Magazynu gorącego| Nie
Obiekty BLOB typu Block | Nie
Szyfrowanie magazynowanych (szyfrowanie usługi Magazyn)| Yes
Premium Storage | Yes
Import/Eksport usługi | Nie
Punkty końcowe usługi sieci wirtualnej<br/><br/> Zapory magazynu i sieci wirtualne skonfigurowane na docelowe konto magazynu pamięci podręcznej/magazynów (używane do przechowywania danych replikacji) | Nie
Konta magazynu ogólnego przeznaczenia v2 (zarówno gorącego i chłodnego warstw) | Nie

## <a name="azure-compute"></a>Obliczeń platformy Azure

**Funkcja** | **Obsługiwane**
--- | ---
Zestawy dostępności | Yes
HUB | Yes
Dyski zarządzane | Yes

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny Wirtualnej platformy Azure

Lokalnych maszyn wirtualnych, które są replikowane do platformy Azure musi spełniać wymagania maszyny Wirtualnej Azure podsumowaniem w poniższej tabeli. Po uruchomieniu sprawdzania wymagań wstępnych usługi Site Recovery zakończy się niepowodzeniem, jeśli niektórych wymagań nie są spełnione.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Sprawdź [obsługiwanych systemów operacyjnych](#replicated machines). | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany. 
Architektura systemu operacyjnego gościa | 64-bitowych. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany. 
Rozmiar dysku systemu operacyjnego | Do 2048 GB. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany. 
Liczba dysków systemu operacyjnego | 1 | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.  
Liczba dysków danych | 64 lub mniej. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.  
Rozmiar dysku danych | Do 4,095 GB | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany. 
Karty sieciowe | Wiele kart sieciowych są obsługiwane. | 
Udostępniony wirtualny dysk twardy | Nieobsługiwane. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany. 
FC dysku | Nieobsługiwane. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany. 
BitLocker | Nieobsługiwane. | Przed włączeniem replikacji dla maszyny, należy wyłączyć funkcję BitLocker. | 
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/><br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa maszyny musi zaczynać i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach maszyny w usłudze Site Recovery.


## <a name="vault-tasks"></a>Magazyn zadań

**Akcja** | **Obsługiwane**
--- | ---
Przenieś magazyn między grupami zasobów<br/><br/> W obrębie subskrypcji oraz | Nie
Przenieść magazyn, sieć, maszyn wirtualnych platformy Azure w grupach zasobów<br/><br/> W obrębie subskrypcji oraz | Nie


## <a name="mobility-service"></a>Usługa mobilności

**Nazwa** | **Opis** | **Najnowsza wersja** | **Szczegóły**
--- | --- | --- | --- | ---
Instalator Unified usługi Azure Site Recovery | Współrzędne komunikacji między serwerami lokalnymi VMware i Azure <br/><br/> Zainstalowana na lokalnych serwerach VMware | 9.12.4653.1 (dostępne w portalu) | [Najnowsze funkcje i poprawki](https://aka.ms/latest_asr_updates)
Usługa mobilności | Koordynuje replikację między lokalnymi VMware serwerów/serwery fizyczne i Azure/dodatkowej lokacji<br/><br/> Zainstalowana na maszynie Wirtualnej VMware lub serwerów fizycznych, które chcesz replikować | 9.12.4653.1 (dostępne w portalu) | [Najnowsze funkcje i poprawki](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się, jak](tutorial-prepare-azure.md) do przygotowania Azure odzyskiwania po awarii maszyn wirtualnych VMware.
