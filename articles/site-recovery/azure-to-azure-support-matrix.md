---
title: Macierz obsługi usługi Azure Site Recovery na potrzeby replikacji z platformy Azure do platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie obsługiwanych systemów operacyjnych i konfiguracji do usługi Azure Site Recovery replikacji maszyn wirtualnych (VM) z jednego regionu do innego na potrzeby odzyskiwania po awarii.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: sujayt
ms.openlocfilehash: 1db54cfd32a0d598d0079ddaec9aab5d6b2f84c1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039296"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Macierz obsługi replikacji między regionami platformy Azure do innego



Ten artykuł zawiera podsumowanie obsługiwanych konfiguracji i składników podczas replikacji i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego regionu za pomocą [usługi Azure Site Recovery](site-recovery-overview.md) usługi.

## <a name="user-interface-options"></a>Opcje interfejsu użytkownika

**Interfejs użytkownika** |  **Obsługiwane / nieobsługiwane**
--- | ---
**Azure Portal** | Obsługiwane
**Program PowerShell** | [Replikacji Azure – Azure przy użyciu programu PowerShell](azure-to-azure-powershell.md)
**Interfejs API REST** | Nie jest obecnie obsługiwany
**Interfejs wiersza polecenia** | Nie jest obecnie obsługiwany


## <a name="resource-support"></a>Obsługa zasobów

**Typ przenoszenia zasobów** | **Szczegóły**
--- | --- | ---
**Przenoszenie magazynu między grupami zasobów** | Nieobsługiwane<br/><br/> Magazyn usługi Recovery services nie można przenosić między grupami zasobów.
**Przenoszenie zasobów obliczeniowych i magazynu/sieci między grupami zasobów** | Nieobsługiwane.<br/><br/> Jeśli po replikuje, przenieść Maszynę wirtualną lub skojarzone składniki, takie jak storage i sieci, należy wyłączyć replikację i ponownie włączyć replikację dla maszyny Wirtualnej.
**Replikowanie maszyn wirtualnych platformy Azure z jednej subskrypcji do innej na potrzeby odzyskiwania po awarii** | Obsługiwane w ramach tej samej dzierżawie usługi Azure Active Directory. Nie jest obsługiwane dla klasycznych maszyn wirtualnych.
**Migrowanie maszyn wirtualnych między regionami w ramach obsługiwanej klastrów geograficznych (wewnątrz i między subskrypcjami)** | Obsługiwane w ramach tej samej dzierżawie usługi Azure Active Directory dla maszyn wirtualnych "Modelu wdrażania usługi Resource manager". Nieobsługiwane w przypadku klasycznego modelu wdrażania maszyn wirtualnych.
**Migrowanie maszyn wirtualnych w tym samym regionie** | Nieobsługiwane.


## <a name="support-for-replicated-machine-os-versions"></a>Obsługa wersji systemu operacyjnego replikowanych maszyn

Poniżej pomocy technicznej jest dostępna w przypadku dowolne obciążenia uruchomione na wymienionych systemu operacyjnego.

#### <a name="windows"></a>Windows

- System Windows Server 2016 (Server Core, serwer ze środowiskiem pulpitu) *
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 z na co najmniej z dodatkiem SP1

>[!NOTE]
>
> \* Windows Server 2016 Nano Server nie jest obsługiwane.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8 6,9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5   
- CentOS 6.5, 6.6, 6.7, 6.8, 6,9, 6.10, 7.0, 7.1, 7.2, 7.3,7.4, 7.5
- Ubuntu 14.04 LTS Server [ (obsługiwane wersje jądra)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS Server [ (obsługiwane wersje jądra)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7 [ (obsługiwane wersje jądra)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8 [ (obsługiwane wersje jądra)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 12 z dodatkiem SP1, SP2, z dodatkiem SP3 [ (obsługiwane wersje jądra)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4
- Oracle Enterprise Linux 6.4, 6.5, 6.6, 6.7 systemem Red Hat zgodne jądra lub podzielenie Enterprise jądra wersji 3 (UEK3)

(Replikowanie maszyn z systemem SLES 11 z dodatkiem SP3 do SLES 11 z dodatkiem SP4 uaktualnienie nie jest obsługiwane. Jeśli replikowanej maszyny został uaktualniony z systemu SLES 11SP3 do SLES 11 z dodatkiem SP4, należy wyłączyć replikację i ochronę maszyny ponownie po uaktualnieniu.)

>[!NOTE]
>
> Serwery systemu Ubuntu przy użyciu uwierzytelniania opartego na hasłach i zaloguj się i korzystają z pakietu cloud-init do konfigurowania maszyn wirtualnych w chmurze, mogą mieć logowania opartego na hasłach, wyłączona po pracy awaryjnej (w zależności od konfiguracji cloudinit.) Logowania opartego na hasłach, można ponownie włączyć na maszynie wirtualnej poprzez zresetowanie hasła z poziomu menu ustawień (w obszarze Pomoc techniczna i rozwiązywanie problemów z sekcji) z nieudane przez maszynę wirtualną w witrynie Azure portal.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra systemu Ubuntu maszyn wirtualnych platformy Azure

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
14.04 LTS | 9.19 | 3.13.0-24-Generic do 3.13.0-153-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-Generic do 3.13.0-151-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-128-generic |
14.04 LTS | 9.17 | 3.13.0-24-Generic do 3.13.0-147-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-124-generic |
14.04 LTS | 9.16 | 3.13.0-24-Generic do 3.13.0-144-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-119-generic |
|||
16.04 LTS | 9.19 | 4.4.0-21-Generic do 4.4.0-131-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-30-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1019-azure|
16.04 LTS | 9.18 | 4.4.0-21-Generic do 4.4.0-128-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure |
16.04 LTS | 9.17 | 4.4.0-21-Generic do 4.4.0-124-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-41-generic,<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1016-azure |
16.04 LTS | 9.16 | 4.4.0-21-Generic do 4.4.0-119-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-38-generic,<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1012-azure |


### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra systemu Debian dla maszyn wirtualnych platformy Azure

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | 9.17,9.18,9.19 | 3.2.0-4-AMD64 do 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | 3.2.0-4-AMD64 do 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | 3.16.0-4-AMD64 do 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17, 9.18 | 3.16.0-4-AMD64 do 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-AMD64 do 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.5-amd64 |

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra systemu SUSE Linux Enterprise Server 12 maszyn wirtualnych platformy Azure

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP3 z dodatkiem SP1, SP2) | 9.18 | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.93-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.80-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.138-94.39-default |
SUSE Linux Enterprise Server 12 (SP3 z dodatkiem SP1, SP2) | 9.17 | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.88-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.126-94.22-default |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Systemy plików obsługiwanych i konfiguracje magazynu gościa na maszynach wirtualnych platformy Azure z systemem operacyjnym Linux

* Systemy plików: ext3, ext4, ReiserFS (Suse Linux Enterprise Server tylko), XFS
* Menedżer woluminów: LVM2
* Oprogramowania wielościeżkowego: mapowania urządzenia

## <a name="region-support"></a>Obsługa regionu

Można replikować i odzyskiwanie maszyn wirtualnych między wszystkie dwóch regionach w obrębie tego samego klastra geograficznego.

**Geograficzne klastra** | **Regiony platformy Azure**
-- | --
Ameryka | Kanada Wschodnia, środkowe stany USA Kanada środkowe, południowo-, Zachodnia środkowe stany USA, wschodnie stany USA, wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2, środkowe stany USA, Północnośrodkowa
Europa | Zachodnie Zjednoczone Królestwo, południowe Zjednoczone Królestwo, Europa Północna, Europa Zachodnia, Francja środkowa, Francja Południowa
Azja | Indie Południowe, Indie środkowe, Azja południowo-wschodnia, Azja Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa
Australia   | Australia Wschodnia, Australia południowo-wschodnia
Azure Government    | Administracja USA — Wirginia, administracja USA — Iowa, administracja USA — Arizona, administracja USA — Teksas, US DOD wschodnie stany, US dod — środkowe
Niemcy | Niemcy środkowe, Niemcy północno-wschodnie
Chiny | Chiny wschodnie, Chiny Północne

>[!NOTE]
>
> Dla regionu Brazylia Południowa można tylko replikacji i pracy awaryjnej do jednego z południowo-środkowe stany USA, zachodnio-środkowe stany USA, wschodnie stany USA, wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2 i regionów północno-środkowe stany USA i powrót po awarii.

## <a name="support-for-vmdisk-management"></a>Obsługa zarządzania maszyna wirtualna/dysk

**Akcja** | **Szczegóły**
-- | ---
Zmiana rozmiaru dysku na zreplikowanej maszyny Wirtualnej | Obsługiwane
Dodaj dysk do zreplikowanej maszyny Wirtualnej | Nieobsługiwane. Użytkownik musi można wyłączyć replikacji dla maszyny Wirtualnej, Dodaj dysk, a następnie włącz ponownie replikację.


## <a name="support-for-compute-configuration"></a>Obsługa konfiguracji obliczeniowej

**Konfiguracja** | **Obsługiwane/nieobsługiwane** | **Uwagi**
--- | --- | ---
Rozmiar | Dowolny rozmiar maszyny Wirtualnej platformy Azure, co najmniej 2 rdzeni procesora CPU i 1 GB pamięci RAM | Zapoznaj się [rozmiarów maszyn wirtualnych platformy Azure](../virtual-machines/windows/sizes.md)
Zestawy dostępności | Obsługiwane | Jeśli używasz opcji domyślnej, podczas wykonywania kroku "Włącz replikację" w portalu, zestaw dostępności jest automatycznie utworzona w oparciu o konfigurację region źródła. Można zmienić docelowy zestaw dostępności w "zreplikowany element > Ustawienia > obliczenia i sieć > zestaw dostępności" dowolnym momencie.
Strefy dostępności | Nieobsługiwane | Maszyny wirtualne wdrożone w strefach dostępności nie są obecnie obsługiwane.
Maszyn wirtualnych z hybrydowego użycia korzyści (HUB) | Obsługiwane | Jeśli źródłowa maszyna wirtualna ma licencję Centrum włączone, Test trybu failover lub maszyny Wirtualnej w tryb Failover korzysta również licencji KONCENTRATORA.
Zestawy skalowania maszyn wirtualnych | Nieobsługiwane |
Obrazy galerii platformy Azure — Microsoft opublikowane | Obsługiwane | Obsługiwane tak długo, jak maszyna wirtualna działa na system operacyjny obsługiwany przez usługę Site Recovery
Obrazy galerii platformy Azure — innych firm opublikowane | Obsługiwane | Obsługiwane tak długo, jak maszyna wirtualna działa na system operacyjny obsługiwany przez usługę Site Recovery.
Obrazy niestandardowe - innych firm opublikowane | Obsługiwane | Obsługiwane tak długo, jak maszyna wirtualna działa na system operacyjny obsługiwany przez usługę Site Recovery.
Maszyny wirtualne migrowane przy użyciu Site Recovery | Obsługiwane | Jeśli tak jest, że maszyn fizycznych/VMware migracji na platformę Azure za pomocą Site Recovery, musisz odinstalować starszej wersji usługi mobilności i uruchom ponownie maszynę przed zreplikowaniem go do innego regionu platformy Azure.

## <a name="support-for-storage-configuration"></a>Obsługa konfiguracji magazynu

**Konfiguracja** | **Obsługiwane/nieobsługiwane** | **Uwagi**
--- | --- | ---
Maksymalny rozmiar dysku systemu operacyjnego | 2048 GB | Zapoznaj się [dysków używanych przez maszyny wirtualne.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Rozmiar dysku danych maksymalna | 4095 GB | Zapoznaj się [dysków używanych przez maszyny wirtualne.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Liczba dysków danych | Maksymalnie 64 jako obsługiwane przez określony rozmiar maszyny Wirtualnej platformy Azure | Zapoznaj się [rozmiarów maszyn wirtualnych platformy Azure](../virtual-machines/windows/sizes.md)
Dysk tymczasowy | Zawsze jest wykluczony z replikacji | Dysk tymczasowy jest wykluczony z replikacji zawsze. Nie należy umieszczać żadnych trwałych danych na dysku tymczasowym, zgodnie z wskazówki dotyczące platformy Azure. Zapoznaj się [dysku tymczasowego na maszynach wirtualnych Azure](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) Aby uzyskać więcej informacji.
Współczynnik zmian danych na dysku | Maksymalnie 10 MB/s na dysk usługi Premium storage i 2 MB/s na dysk dla magazynu w warstwie standardowa | Średni współczynnik zmian danych na dysku wynosi ponad 10 MB/s (dla warstwy Premium) oraz 2 MB/s (dla warstwy standardowa) stale, replikacji nie będzie przechwytywać. Jeśli jest serii danych sporadycznie, współczynnik zmian danych jest większa niż 10 MB/s (dla warstwy Premium) oraz 2 MB/s (dla warstwy standardowa) przez pewien czas i sprowadza się replikacja będzie przechwytywać. W takim przypadku można napotkać punktów odzyskiwania nieco opóźnione.
Dyski na kontach magazynu w warstwie standardowa | Obsługiwane |
Dyski na kont usługi premium storage | Obsługiwane | Jeśli maszyna wirtualna ma dyski rozkładają się na konta magazynu w warstwie standardowa i premium, możesz wybrać innego docelowego konta magazynu dla każdego dysku upewnić się, że mają taką samą konfigurację magazynu w regionie docelowym
Dyski zarządzane w warstwie standardowa | Obsługiwane regiony systemu Azure, w których usługa Azure Site Recovery jest obsługiwany. |  
Premium Managed disks | Obsługiwane regiony systemu Azure, w których usługa Azure Site Recovery jest obsługiwany. |
Miejsca do magazynowania | Obsługiwane |         
Szyfrowanie danych magazynowanych (SSE) | Obsługiwane | Funkcja SSE jest ustawieniem domyślnym dla kont magazynu.   
Usługa Azure Disk Encryption (ADE) dla systemu operacyjnego Windows | Włączone dla maszyn wirtualnych [szyfrowanie za pomocą aplikacji usługi Azure AD](https://aka.ms/ade-aad-app) są obsługiwane |
Usługa Azure Disk Encryption (ADE) dla systemu Linux, systemu operacyjnego | Nieobsługiwane |
Dodaj lub usuń gorąco dysku | Nieobsługiwane | Dodaj lub Usuń dysk danych na maszynie Wirtualnej, należy wyłączyć replikację i włącz ponownie replikację maszyny Wirtualnej.
Wykluczanie dysku | Nieobsługiwane|   Dysk tymczasowy jest domyślnie wykluczony.
Bezpośrednie miejsca do magazynowania magazynu  | Nieobsługiwane|
Serwer plików skalowalny w poziomie  | Nieobsługiwane|
LRS | Obsługiwane |
GRS | Obsługiwane |
RA-GRS | Obsługiwane |
ZRS | Nieobsługiwane |  
Chłodnej i gorącej magazynu. | Nieobsługiwane | Dyski maszyny wirtualnej nie są obsługiwane w chłodnej i gorącej magazynu.
Usługa Azure Storage zapory sieci wirtualnej  | Nie | Zezwalanie na dostęp do określonych sieci wirtualnej platformy Azure dla kont magazynu pamięci podręcznej używane do przechowywania replikowanych danych nie jest obsługiwane.
Konta magazynu ogólnego przeznaczenia w wersji 2 (zarówno gorąca i chłodna warstwa) | Nie | Wzrost kosztów transakcji znacznie w porównaniu do ogólnego przeznaczenia w wersji 1 konta magazynu

>[!IMPORTANT]
> Upewnij się, że przestrzegasz maszyn wirtualnych dysku cele skalowalności i wydajności dla [Linux](../virtual-machines/linux/disk-scalability-targets.md) lub [Windows](../virtual-machines/windows/disk-scalability-targets.md) maszyn wirtualnych, aby uniknąć problemów z wydajnością. Jeśli stosujesz ustawienia domyślne, Usługa Site Recovery utworzy wymagane dyski i kont magazynu, w oparciu o konfigurację źródła. Dostosowywanie i wybrać własne ustawienia, upewnij się, wykonaj cele dotyczące skalowalności i wydajności dysku dla źródła maszyn wirtualnych.

## <a name="support-for-network-configuration"></a>Obsługa konfiguracji sieci
**Konfiguracja** | **Obsługiwane/nieobsługiwane** | **Uwagi**
--- | --- | ---
Interfejs sieciowy (karta sieciowa) | Maksymalnie maksymalna liczba kart sieciowych obsługiwanych przez określony rozmiar maszyny Wirtualnej platformy Azure | Karty sieciowe są tworzone, gdy maszyna wirtualna jest tworzona jako część operacji trybu Failover lub testu trybu failover. Liczba kart sieciowych na maszynie Wirtualnej w trybie failover jest zależna od liczbę kart sieciowych źródłowa maszyna wirtualna ma podczas włączania replikacji. Jeśli możesz dodać/usunąć kartę Sieciową po włączeniu replikacji, nie ma wpływu na liczbę kart Sieciowych na maszynie Wirtualnej w trybie failover.
Internetowy moduł równoważenia obciążenia | Obsługiwane | Musisz skojarzyć modułu równoważenia obciążenia wstępnie skonfigurowane, za pomocą skryptu usługi azure automation w planie odzyskiwania.
Wewnętrzny moduł równoważenia obciążenia | Obsługiwane | Musisz skojarzyć modułu równoważenia obciążenia wstępnie skonfigurowane, za pomocą skryptu usługi azure automation w planie odzyskiwania.
Publiczny adres IP| Obsługiwane | Należy ją skojarzyć istniejącego publicznego adresu IP do karty Sieciowej lub utworzyć i skojarzyć z kartą Sieciową, za pomocą skryptu usługi azure automation w planie odzyskiwania.
Sieciowa grupa zabezpieczeń na karcie Sieciowej (Resource Manager)| Obsługiwane | Musisz skojarzyć sieciową grupę zabezpieczeń z kartą Sieciową, za pomocą skryptu usługi azure automation w planie odzyskiwania.  
Sieciowa grupa zabezpieczeń w podsieci (Resource Manager i model klasyczny)| Obsługiwane | Musisz skojarzyć sieciową grupę zabezpieczeń do podsieci za pomocą skryptu usługi azure automation w planie odzyskiwania.
Sieciowa grupa zabezpieczeń na maszynie Wirtualnej (model klasyczny)| Obsługiwane | Musisz skojarzyć sieciową grupę zabezpieczeń z kartą Sieciową, za pomocą skryptu usługi azure automation w planie odzyskiwania.
Zarezerwowanych adresów IP (statyczny adres IP) / zachować źródłowy adres IP | Obsługiwane | Jeśli karta sieciowa źródłowej maszyny Wirtualnej ma konfiguracji statycznych adresów IP i podsieć docelowa ma ten sam adres IP dostępne, jest przypisany do trybu failover maszyny Wirtualnej. Jeśli podsieć docelowa nie ma tego samego adresu IP dostępnego, jeden z dostępnych adresów IP w podsieci jest zarezerwowany dla tej maszyny Wirtualnej. Można określić stałych adresów IP w wybranym "zreplikowany element > Ustawienia > obliczenia i sieć > interfejsy sieciowe. Można wybrać kartę Sieciową i określ podsieci i adresu IP w wybranym.
Dynamiczny adres IP| Obsługiwane | Jeśli karta sieciowa źródłowej maszyny Wirtualnej ma dynamiczną konfigurację adresu IP karty Sieciowej w tryb failover maszyny Wirtualnej jest również dynamiczne domyślnie. Można określić stałych adresów IP w wybranym "zreplikowany element > Ustawienia > obliczenia i sieć > interfejsy sieciowe. Można wybrać kartę Sieciową i określ podsieci i adresu IP w wybranym.
Integracja z usługą Traffic Manager | Obsługiwane | Można wstępnie skonfigurować usługi traffic manager w taki sposób, że ruch jest kierowany do punktu końcowego w regionie źródłowym na bieżąco do punktu końcowego w regionie docelowym w przypadku pracy awaryjnej.
Zarządzanego usługi Azure DNS | Obsługiwane |
Niestandardowe DNS  | Obsługiwane |    
Nieuwierzytelnione serwera Proxy | Obsługiwane | Zapoznaj się [dokument ze wskazówkami dotyczącymi sieci.](site-recovery-azure-to-azure-networking-guidance.md)    
Uwierzytelnionego serwera Proxy | Nieobsługiwane | Jeśli maszyna wirtualna korzysta z uwierzytelnionego serwera proxy dla połączenia wychodzącego, nie może być replikowane za pomocą usługi Azure Site Recovery.    
Sieć VPN między lokacjami, z lokalnych (z lub bez usługi ExpressRoute)| Obsługiwane | Upewnij się, że tras zdefiniowanych przez użytkownika i sieciowymi grupami zabezpieczeń są skonfigurowane w taki sposób, ruch odzyskiwania lokacji nie jest kierowany do sieci lokalnej. Zapoznaj się [dokument ze wskazówkami dotyczącymi sieci.](site-recovery-azure-to-azure-networking-guidance.md)  
Połączenie między sieciami Wirtualnymi | Obsługiwane | Zapoznaj się [dokument ze wskazówkami dotyczącymi sieci.](site-recovery-azure-to-azure-networking-guidance.md)  
Punkty końcowe usługi sieci wirtualnej | Obsługiwane | Usługa Azure zapór usługi Storage dla sieci wirtualnych nie są obsługiwane. Zezwalanie na dostęp do określonych sieci wirtualnej platformy Azure dla kont magazynu pamięci podręcznej używane do przechowywania replikowanych danych nie jest obsługiwane.
Accelerated Networking | Obsługiwane | Przyspieszona sieć musi być włączona na źródłowej maszynie Wirtualnej. [Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [sieć wskazówki dotyczące replikowania maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure-networking-guidance.md)
- Zacząć chronić swoje obciążenia przez [replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure.md)
