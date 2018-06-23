---
title: Azure Site Recovery macierz obsługi replikacji z platformy Azure na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie obsługiwanych systemów operacyjnych i konfiguracji dla usługi Azure Site Recovery replikacji maszyn wirtualnych platformy Azure (maszyn wirtualnych) z jednego regionu do innego na potrzeby odzyskiwania (DR) po awarii.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: sujayt
ms.openlocfilehash: 7f0011172185f13f51bcea8061b36012aa5da33b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321199"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Macierz obsługi replikacji z jednego regionu Azure do innego



Ten artykuł zawiera podsumowanie obsługiwanych konfiguracji i składników podczas replikacji i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego regionu, za pomocą [usługi Azure Site Recovery](site-recovery-overview.md) usługi.

## <a name="user-interface-options"></a>Opcje interfejsu użytkownika

**Interfejs użytkownika** |  **Obsługiwane / nieobsługiwane**
--- | ---
**Azure Portal** | Obsługiwane
**Portal klasyczny** | Nieobsługiwane
**Program PowerShell** | [Replikacja Azure do platformy Azure przy użyciu programu PowerShell](azure-to-azure-powershell.md)
**Interfejs API REST** | Nie są obecnie obsługiwane
**Interfejs wiersza polecenia** | Nie są obecnie obsługiwane


## <a name="resource-move-support"></a>Obsługa przeniesienia zasobu

**Typ przenoszenia zasobów** | **Obsługiwane / nieobsługiwane** | **Uwagi**  
--- | --- | ---
**Przenieś magazyn między grupami zasobów** | Nieobsługiwane |Nie można przenieść magazyn usług odzyskiwania między grupami zasobów.
**Przenoszenie między grupami zasobów obliczeniowych, magazynu i sieci** | Nieobsługiwane |Jeśli po włączeniu replikacji maszyny wirtualnej (lub jej skojarzone składniki, takie jak magazyn i sieć), należy wyłączyć replikację i włączyć replikację dla maszyny wirtualnej ponownie.



## <a name="support-for-deployment-models"></a>Obsługa modeli wdrażania

**Model wdrażania** | **Obsługiwane / nieobsługiwane** | **Uwagi**  
--- | --- | ---
**Wdrożenie klasyczne** | Obsługiwane | Można tylko replikowanie klasyczne maszyny wirtualnej i odzyskać ją jako maszynę wirtualną w klasycznym. Nie można go odzyskać jako maszynę wirtualną Menedżera zasobów. W przypadku wdrożenia klasyczne maszyny Wirtualnej bez sieci wirtualnej i bezpośrednio do regionu platformy Azure, nie jest obsługiwane.
**Resource Manager** | Obsługiwane |

>[!NOTE]
>
> 1. Replikowanie maszyn wirtualnych platformy Azure z jedną subskrypcję do innej scenariuszy odzyskiwania po awarii nie jest obsługiwane.
> 2. Azure Migrowanie maszyn wirtualnych między subskrypcjami nie jest obsługiwane.
> 3. Azure Migrowanie maszyn wirtualnych w tym samym regionie nie jest obsługiwane.
> 4. Migrowanie maszyn wirtualnych platformy Azure z klasycznego modelu wdrożenia do zasobu modelu wdrażania Menedżera nie jest obsługiwane.

## <a name="support-for-replicated-machine-os-versions"></a>Obsługa wersji systemu operacyjnego zreplikowanej maszyny

Poniżej Obsługa dotyczy dowolne obciążenia uruchomione na wymienione systemu operacyjnego.

#### <a name="windows"></a>Windows

- Windows Server 2016 (instalacja Server Core, serwer ze środowiskiem pulpitu) *
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 z na co najmniej z dodatkiem SP1

>[!NOTE]
>
> \* Windows Server 2016 Nano Server nie jest obsługiwane.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3,7.4
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3,7.4
- Ubuntu 14.04 LTS Server [ (obsługiwane wersje jądra)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS Server [ (obsługiwane wersje jądra)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7 [ (obsługiwane wersje jądra)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8 [ (obsługiwane wersje jądra)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 12 SP1 z dodatkiem SP2, z dodatkiem SP3 [ (obsługiwane wersje jądra)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4
- Oracle Linux przedsiębiorstwa 6.4, 6.5 systemem Red Hat jądra zgodny lub podzielenie Enterprise jądra wersji 3 (UEK3)

(Uaktualnienie replikowanie maszyn z SLES 11 z dodatkiem SP3 do SLES 11 z dodatkiem SP4 nie jest obsługiwane. Jeśli zreplikowanej maszyny został uaktualniony z SLES 11SP3 do SLES 11 z dodatkiem SP4, należy wyłączyć replikację i chronić komputer ponownie post uaktualnienia.)

>[!NOTE]
>
> Serwery systemu Ubuntu przy użyciu uwierzytelniania opartego na hasłach i logowania i konfigurowanie maszyn wirtualnych w chmurze, za pomocą pakietu init chmury mogą mieć logowania opartego na hasłach wyłączone podczas pracy awaryjnej (w zależności od konfiguracji cloudinit.) Logowanie oparte na hasłach, można ponownie włączyć na maszynie wirtualnej poprzez zresetowanie hasła z menu ustawień (w pomocy technicznej i rozwiązywania problemów sekcja) z nieudane przez maszynę wirtualną w portalu Azure.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra Ubuntu maszyn wirtualnych platformy Azure

**Zlecenia** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
14.04 LTS | 9.17 | 3.13.0-24-Generic do 3.13.0-147-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-124-generic |
14.04 LTS | 9.16 | 3.13.0-24-Generic do 3.13.0-144-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-119-generic |
14.04 LTS | 9.15. | 3.13.0-24-Generic do 3.13.0-143-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-116-generic |
14.04 LTS | : 9,14 | 3.13.0-24-Generic do 3.13.0-141-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-112-generic |
|||
16.04 LTS | 9.17 | 4.4.0-21-Generic do 4.4.0-124-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-41-generic,<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1016-azure |
16.04 LTS | 9.16 | 4.4.0-21-Generic do 4.4.0-119-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-38-generic,<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1012-azure |
16.04 LTS | 9.15. | 4.4.0-21-Generic do 4.4.0-116-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-37-generic,<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1012-azure |
16.04 LTS | : 9,14 | 4.4.0-21-Generic do 4.4.0-112-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-32-generic,<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1009-azure |


### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje Debian jądra dla maszyn wirtualnych platformy Azure

**Zlecenia** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | 9.17 | 3.2.0-4-AMD64 do 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | : 9,14, 9.15., 9.16 | 3.2.0-4-AMD64 do 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.17 | 3.16.0-4-AMD64 do 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.6-amd64 |
Debian 8 | : 9,14, 9.15., 9.16 | 3.16.0-4-AMD64 do 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.5-amd64 |

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra SUSE Linux Enterprise Server 12 maszyn wirtualnych platformy Azure

**Zlecenia** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP3 SP1 i SP2) | 9.17 | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.88-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.126-94.22-default |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Obsługiwanych systemów plików i konfiguracje magazynu gościa na maszynach wirtualnych Azure systemem operacyjnym Linux

* Systemy plików: ext3 ext4, ReiserFS (Suse Linux Enterprise Server tylko), XFS
* Menedżer woluminów: LVM2
* Oprogramowanie wielościeżkowego: mapowania urządzenia

## <a name="region-support"></a>Obsługa regionu

Można replikować i odzyskiwania maszyn wirtualnych między żadnych dwóch regionach, w tym samym klastrze geograficzne.

**Geograficzne klastra** | **Regiony platformy Azure**
-- | --
Ameryka | Kanada Wschodnia, środkowe stany USA Kanada centralnej, Południowej, zachodnie środkowe stany USA, wschodnie stany USA, wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2, środkowe stany USA, północno środkowe stany USA
Europa | Wielka Brytania Zachodnia, Wielka Brytania Południowa, Europa Północna, Europa Zachodnia, Francja środkowe, Francja Południowa
Azja | Indie Południowa, Indie środkowe, Azja południowo-wschodnia, Azja Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea środkowe, Korea południe
Australia   | Australia Wschodnia, Australia południowo-wschodnia
Azure Government    | Virginia wersji dla instytucji rządowych USA, Iowa wersji dla instytucji rządowych USA, Arizona wersji dla instytucji rządowych USA, Texas wersji dla instytucji rządowych USA, wschód DOD USA, środkowe stany USA DOD
Niemcy | Niemcy środkowe, Niemcy północno-wschodnie
Chiny | Wschodnia Chin, Chiny Północna

>[!NOTE]
>
> W regionie Brazylia Południowa można tylko replikacji i pracy awaryjnej do jednego z południowo-środkowe stany, zachodnie centralnej nam wschodnie stany USA, wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2 i regiony północno-środkowe Stany i wykonaj powrót po awarii.


## <a name="support-for-compute-configuration"></a>Obsługa konfiguracji obliczeniowej

**Konfiguracja** | **Obsługiwane/nieobsługiwane** | **Uwagi**
--- | --- | ---
Rozmiar | Wszelkie rozmiar maszyny Wirtualnej platformy Azure z co najmniej 2 rdzeni Procesora i 1 GB pamięci RAM | Zapoznaj się [rozmiary maszyny wirtualnej platformy Azure](../virtual-machines/windows/sizes.md)
Zestawy dostępności | Obsługiwane | Jeśli używasz opcji domyślnej podczas wykonywania kroku "Włącz replikację" w portalu zestawu dostępności jest automatycznie tworzone w oparciu konfiguracji region źródła. Zestaw docelowej dostępności można zmienić "elementu zreplikowane > Ustawienia > obliczenia i sieć > zestawu dostępności" dowolnej chwili.
Maszyny wirtualne korzyści (KONCENTRATOR) Użyj hybrydowego | Obsługiwane | Jeśli źródło maszyny Wirtualnej ma włączone licencji KONCENTRATORA, Test trybu failover lub trybu Failover maszyny Wirtualnej używa również licencji KONCENTRATORA.
Zestawy skalowania maszyn wirtualnych | Nieobsługiwane |
Opublikowane Microsoft Azure obrazów w galerii- | Obsługiwane | Obsługiwane tak długo, jak maszyna wirtualna działa na system operacyjny obsługiwany przez usługę Site Recovery
Obrazów w galerii Azure - opublikowane innych firm | Obsługiwane | Obsługiwane, jak długo maszyna wirtualna działa na system operacyjny obsługiwany przez usługę Site Recovery.
Niestandardowe obrazy - opublikowane innych firm | Obsługiwane | Obsługiwane, jak długo maszyna wirtualna działa na system operacyjny obsługiwany przez usługę Site Recovery.
Migracja maszyn wirtualnych przy użyciu usługi Site Recovery | Obsługiwane | Jeżeli jest to przeprowadzić migracji maszyny VMware/fizyczne na platformie Azure przy użyciu usługi Site Recovery, musisz odinstalować starszej wersji usługi mobilności i uruchom ponownie komputer przed replikować go do innego regionu systemu Azure.

## <a name="support-for-storage-configuration"></a>Obsługa konfiguracji magazynu

**Konfiguracja** | **Obsługiwane/nieobsługiwane** | **Uwagi**
--- | --- | ---
Maksymalny rozmiar dysku systemu operacyjnego | 2048 GB | Zapoznaj się [dysków używanych przez maszyny wirtualne.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Rozmiar dysku danych maksymalna | 4095 GB | Zapoznaj się [dysków używanych przez maszyny wirtualne.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Liczba dysków z danymi | Maksymalnie 64 jako obsługiwana przez określony rozmiar maszyny Wirtualnej Azure | Zapoznaj się [rozmiary maszyny wirtualnej platformy Azure](../virtual-machines/windows/sizes.md)
Tymczasowe dysku | Zawsze wyłączone z replikacji | Dysku tymczasowym został wykluczony z replikacji zawsze. Nie należy umieszczać żadnych danych na dysku tymczasowym zgodnie z harmonogramem wskazówki platformy Azure. Zapoznaj się [dysku tymczasowym na maszynach wirtualnych Azure](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) więcej szczegółów.
Częstotliwość zmian danych na dysku | Maksymalnie 10 MB/s na dysku dla usługi Premium storage i 2 MB/s dla każdego dysku do magazynu w warstwie standardowa | Częstotliwość zmian uśrednianie danych na dysku jest ponad 10 MB/s (dla Premium) i 2 MB/s (standardu) stale, replikacji nie będzie przechwytywać. Jeśli jest serii danych okazjonalne i częstotliwości zmian danych jest większa niż 10 MB/s (w przypadku Premium) i 2 MB/s (standardu) przez pewien czas i zawiera, replikacja będzie przechwytywać. W takim przypadku można napotkać punktów odzyskiwania nieco opóźnione.
Dyski na kontach magazynu w warstwie standardowa | Obsługiwane |
Dyski na kontach magazynu w warstwie premium | Obsługiwane | Jeśli maszyna wirtualna zawiera dyski rozmieszczenie do konta magazynu w warstwie standardowa i premium, możesz wybrać inny element docelowy konta magazynu dla każdego dysku upewnić się, czy masz taką samą konfigurację magazynu w docelowym regionie
Dyski standardowe zarządzane | Obsługiwane w regionach platformy Azure, w których usługi Azure Site Recovery jest obsługiwana. Rządowych chmury nie są obecnie obsługiwane.  |  
Dysków zarządzanych w warstwie Premium | Obsługiwane w regionach platformy Azure, w których usługi Azure Site Recovery jest obsługiwana. Rządowych chmury nie są obecnie obsługiwane. |
Funkcja miejsca do magazynowania | Obsługiwane |         
Szyfrowanie magazynowanych (SSE) | Obsługiwane | SSE jest ustawieniem domyślnym dla kont magazynu.   
Szyfrowanie dysków Azure (ADE) | Nieobsługiwane |
Dodaj lub usuń gorących dysku | Nieobsługiwane | Dodaj lub Usuń dysk danych na maszynie Wirtualnej, należy wyłączyć replikację i włącz ponownie replikację maszyny Wirtualnej.
Wykluczanie dysku | Nieobsługiwane|   Domyślnie jest wykluczony dysku tymczasowym.
Bezpośrednie miejsca do magazynowania  | Nieobsługiwane|
Serwer plików skalowalny w poziomie  | Nieobsługiwane|
LRS | Obsługiwane |
GRS | Obsługiwane |
RA-GRS | Obsługiwane |
ZRS | Nieobsługiwane |  
Chłodny i gorących magazynu | Nieobsługiwane | Dyski maszyny wirtualnej nie są obsługiwane na magazynu chłodnego i gorących
Usługa Azure Storage zapory dla sieci wirtualnej  | Nie | Zezwalanie na dostęp do określonych sieci wirtualnych platformy Azure na kontach magazynu pamięci podręcznej używany do przechowywania replikowanych danych nie jest obsługiwane.
Konta magazynu ogólnego przeznaczenia V2 (zarówno gorącego i chłodnej warstwy) | Nie | Zwiększenie kosztów transakcji znacznie w porównaniu do ogólnego przeznaczenia V1 kont magazynu

>[!IMPORTANT]
> Upewnij się, że przestrzegają wirtualna dysku skalowalność i wydajność docelowe [Linux](../virtual-machines/linux/disk-scalability-targets.md) lub [Windows](../virtual-machines/windows/disk-scalability-targets.md) maszyn wirtualnych, aby uniknąć problemów z wydajnością. Jeśli wykonujesz ustawień domyślnych usługi Site Recovery spowoduje utworzenie wymagane dyski i kont magazynu, na podstawie konfiguracji źródła. Jeśli należy dostosować i wybrać własne ustawienia, upewnij się, wykonaj cele skalowalność i wydajność dysku dla źródła maszyn wirtualnych.

## <a name="support-for-network-configuration"></a>Obsługa konfiguracji sieci
**Konfiguracja** | **Obsługiwane/nieobsługiwane** | **Uwagi**
--- | --- | ---
Interfejsu sieciowego (NIC) | Maksymalnie maksymalną liczbę kart sieciowych obsługiwanych przez określony rozmiar maszyny Wirtualnej Azure | Karty sieciowe są tworzone po utworzeniu maszyny Wirtualnej w ramach testowego trybu failover lub przejście w tryb Failover. Liczba kart sieciowych w tryb failover maszyny Wirtualnej zależy od liczba kart sieciowych źródło, które maszyna wirtualna ma podczas włączania replikacji. Jeśli musisz usunąć kart interfejsu Sieciowego po włączeniu replikacji, liczba kart Sieciowych w tryb failover maszyny Wirtualnej nie ma wpływu.
Internetowy moduł równoważenia obciążenia | Obsługiwane | Musisz skojarzyć usługi równoważenia obciążenia wstępnie skonfigurowane przy użyciu skryptu automatyzacji azure w planie odzyskiwania.
Wewnętrzny moduł równoważenia obciążenia | Obsługiwane | Musisz skojarzyć usługi równoważenia obciążenia wstępnie skonfigurowane przy użyciu skryptu automatyzacji azure w planie odzyskiwania.
Publiczny adres IP| Obsługiwane | Musisz skojarzyć już istniejącego publicznego adresu IP do karty Sieciowej lub utworzyć i skojarzyć z kartą sieciową w planie odzyskiwania przy użyciu skryptu automatyzacji azure.
Grupa NSG w karcie Sieciowej (Resource Manager)| Obsługiwane | Musisz skojarzyć grupę NSG z kartą sieciową w planie odzyskiwania przy użyciu skryptu automatyzacji azure.  
NSG podsieci (Resource Manager i model klasyczny)| Obsługiwane | Musisz skojarzyć grupę NSG do podsieci za pomocą skryptu automatyzacji azure w planie odzyskiwania.
Grupy NSG na maszynie Wirtualnej (klasyczne)| Obsługiwane | Musisz skojarzyć grupę NSG z kartą sieciową w planie odzyskiwania przy użyciu skryptu automatyzacji azure.
Zastrzeżony adres IP (statyczny adres IP) / zachować źródłowy adres IP | Obsługiwane | Jeśli karty interfejsu Sieciowego na źródłowej maszyny Wirtualnej ma konfiguracji statycznych adresów IP i podsieci docelowej ma tego samego adresu IP dostępne, jest przypisany do trybu failover maszyny Wirtualnej. Jeśli w podsieci docelowej nie ma tego samego adresu IP dostępne, jeden z dostępnych adresów IP w podsieci jest zarezerwowany dla tej maszyny Wirtualnej. Można określić stałego adresu IP w wybranym "elementu zreplikowane > Ustawienia > obliczenia i sieć > interfejsów sieciowych. Można wybrać kartę Sieciową i podaj podsieć lub adres IP wybranego.
Dynamicznego adresu IP| Obsługiwane | Jeśli karta sieciowa na źródłowej maszyny Wirtualnej ma dynamicznej konfiguracji IP, karty Sieciowej w tryb failover maszyny Wirtualnej jest również dynamiczne domyślnie. Można określić stałego adresu IP w wybranym "elementu zreplikowane > Ustawienia > obliczenia i sieć > interfejsów sieciowych. Można wybrać kartę Sieciową i podaj podsieć lub adres IP wybranego.
Integracja z usługą Traffic Manager | Obsługiwane | Można wstępnie skonfigurować Menedżera ruchu w taki sposób, że ruch jest kierowany do punktu końcowego w regionie źródłowym w sposób regularny do punktu końcowego w region docelowy w przypadku trybu failover.
Azure zarządzanych DNS | Obsługiwane |
Niestandardowe DNS  | Obsługiwane |    
Nieuwierzytelnione serwera Proxy | Obsługiwane | Zapoznaj się [wytycznych sieci.](site-recovery-azure-to-azure-networking-guidance.md)    
Uwierzytelnionego serwera Proxy | Nieobsługiwane | Jeśli maszyna wirtualna używa wychodzące połączenie z uwierzytelnionego serwera proxy, nie mogą być replikowane za pomocą usługi Azure Site Recovery.    
Sieci VPN między lokacjami z lokalnymi (z lub bez ExpressRoute)| Obsługiwane | Upewnij się, że Udr i grup NSG są skonfigurowane w taki sposób, że ruch odzyskiwania lokacji nie jest kierowany do lokalnego. Zapoznaj się [wytycznych sieci.](site-recovery-azure-to-azure-networking-guidance.md)  
Sieć wirtualna połączenia sieci Wirtualnej | Obsługiwane | Zapoznaj się [wytycznych sieci.](site-recovery-azure-to-azure-networking-guidance.md)  
Punkty końcowe usługi sieci wirtualnej | Obsługiwane | Azure zapór magazynowania dla sieci wirtualne nie są obsługiwane. Zezwalanie na dostęp do określonych sieci wirtualnych platformy Azure na kontach magazynu pamięci podręcznej używany do przechowywania replikowanych danych nie jest obsługiwane.
Accelerated Networking | Nieobsługiwane | Mogą być replikowane maszyny Wirtualnej za pomocą przyspieszony sieci włączone, ale tryb failover maszyna wirtualna nie będzie miał przyspieszony sieci włączone. Również zostaną wyłączone przyspieszonego sieci dla źródłowej maszyny Wirtualnej na powrót po awarii.


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [sieci wskazówki dotyczące replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure-networking-guidance.md)
- Włączyć ochronę obciążeń przez [replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure.md)
