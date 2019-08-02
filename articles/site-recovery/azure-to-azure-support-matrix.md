---
title: Macierz obsługi odzyskiwania po awarii maszyn wirtualnych platformy Azure między regionami platformy Azure z Azure Site Recovery | Microsoft Docs
description: Podsumowuje wymagania wstępne i obsługę odzyskiwania po awarii maszyn wirtualnych platformy Azure z jednego regionu do innego za pomocą Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 87362b785ecbb17c5a49a8c087b6a70715480877
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679378"
---
# <a name="support-matrix-for-replicating-azure-vms-from-one-region-to-another"></a>Matryca obsługi replikacji maszyn wirtualnych platformy Azure z jednego regionu do innego

Ten artykuł podsumowuje pomoc techniczną i wymagania wstępne w przypadku konfigurowania odzyskiwania po awarii maszyn wirtualnych platformy Azure z jednego regionu świadczenia usługi Azure do innego przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .


## <a name="deployment-method-support"></a>Obsługa metod wdrażania

**Wdrożenie** |  **Pomoc techniczna**
--- | ---
**Azure Portal** | Obsługiwane.
**Program PowerShell** | Obsługiwane. [Dowiedz się więcej](azure-to-azure-powershell.md)
**REST API** | Obsługiwane.
**Interfejs wiersza polecenia** | Obecnie nie jest obsługiwane


## <a name="resource-support"></a>Pomoc techniczna dla zasobów

**Akcja zasobu** | **Szczegóły**
--- | --- | ---
**Przenoszenie magazynów między grupami zasobów** | Nieobsługiwane
**Przenoszenie zasobów obliczeniowych/magazynowych/sieciowych między grupami zasobów** | Nieobsługiwane.<br/><br/> W przypadku przenoszenia maszyny wirtualnej lub skojarzonych składników, takich jak magazyn/sieć po zakończeniu replikacji maszyny wirtualnej, należy wyłączyć i ponownie włączyć replikację maszyny wirtualnej.
**Replikowanie maszyn wirtualnych platformy Azure z jednej subskrypcji do innej na potrzeby odzyskiwania po awarii** | Obsługiwane w ramach tej samej dzierżawy Azure Active Directory.
**Migrowanie maszyn wirtualnych między regionami w ramach obsługiwanych klastrów geograficznych (w ramach i między subskrypcjami)** | Obsługiwane w ramach tej samej dzierżawy Azure Active Directory.
**Migrowanie maszyn wirtualnych w tym samym regionie** | Nieobsługiwane.

## <a name="region-support"></a>Obsługa regionów

Maszyny wirtualne można replikować i odzyskiwać między dwoma regionami w tym samym klastrze geograficznym. Klastry geograficzne są definiowane z uwzględnieniem opóźnień danych i suwerenności.


**Klaster geograficzny** | **Regiony platformy Azure**
-- | --
USA | Kanada Wschodnia, Kanada środkowa, Południowo-środkowe stany USA, zachodnie stany USA, Wschodnie stany USA, Wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2, środkowe stany USA, Północno-środkowe stany USA
Europa | Zachodnie Zjednoczone Królestwo, Południowe Zjednoczone Królestwo, Europa Północna, Europa Zachodnia, Francja środkowa, Francja Południowa, Zachodnia Republika Południowej Afryki Północna Republika Południowej Afryki
Azja | Indie Południowe, Indie Środkowe, Indie Zachodnie, Azja Południowo-Wschodnia, Azja Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa
Australia   | Australia Wschodnia, Australia Południowo-Wschodnia, Australia Środkowa, Australia Środkowa 2
Azure Government    | Stany USA GOV Wirginia, US GOV Iowa, US GOV Arizona, US GOV Texass, US DOD USA, US DOD USA 
Niemcy | Niemcy środkowe, Niemcy północno-wschodnie
Chiny | Chiny Wschodnie, Chiny Północne, Chiny North2, Chiny 2

>[!NOTE]
>
> - W przypadku **Brazylii Południowej**można replikować i przełączać w tryb failover do tych regionów: Południowo-środkowe stany USA, zachodnie stany USA, Wschodnie stany USA, Wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2 i Północno-środkowe stany USA.
> - Brazylia Południowa może być używana tylko jako region źródłowy, z którego maszyny wirtualne mogą replikować przy użyciu Site Recovery. Nie może działać jako region docelowy. Dzieje się tak z powodu problemów opóźnienia spowodowanych odległościami geograficznymi.
> - Możesz korzystać z regionów, do których masz odpowiedni dostęp.
> - Jeśli region, w którym chcesz utworzyć magazyn, nie jest wyświetlany, upewnij się, że Twoja subskrypcja ma dostęp do tworzenia zasobów w tym regionie.
> - Jeśli podczas włączania replikacji nie widzisz regionu w klastrze geograficznym, upewnij się, że Twoja subskrypcja ma uprawnienia do tworzenia maszyn wirtualnych w tym regionie.



## <a name="cache-storage"></a>Magazyn pamięci podręcznej

Ta tabela zawiera podsumowanie obsługi konta magazynu pamięci podręcznej używanego przez Site Recovery podczas replikacji.

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Konta magazynu ogólnego przeznaczenia w wersji 2 (warstwa gorąca i chłodna) | Obsługiwane | Użycie GPv2 jest niezalecane, ponieważ koszty transakcji dla wersji 2 są znacznie wyższe niż konta magazynu w wersji 1.
Zapory usługi Azure Storage dla sieci wirtualnych  | Obsługiwane | Jeśli używasz konta magazynu pamięci podręcznej z włączoną obsługą zapory lub docelowego konta magazynu, upewnij się, że masz [opcję "Zezwalaj na zaufane usługi firmy Microsoft"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## <a name="replicated-machine-operating-systems"></a>Zreplikowane systemy operacyjne maszyn

Site Recovery obsługuje replikację maszyn wirtualnych platformy Azure z systemami operacyjnymi wymienionymi w tej sekcji.

### <a name="windows"></a>Windows

**System operacyjny** | **Szczegóły**
--- | ---
Windows Server 2019 | Server Core, serwer z funkcją środowisko pulpitu
Windows Server 2016  | Server Core, serwer z funkcją środowisko pulpitu
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Uruchamianie programu SP1 lub nowszego
Windows 10 (x64) |
Windows 8.1 (x64) |
Windows 8 (x64) |
Windows 7 (x64) | Uruchomiony program SP1 lub nowszy (system Windows 7 RTM nie jest obsługiwany)

#### <a name="linux"></a>Linux

**System operacyjny** | **Szczegóły**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6  
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6
Serwer Ubuntu 14,04 LTS | [Obsługiwane wersje jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Serwer Ubuntu 16,04 LTS | [Obsługiwana wersja jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Serwery Ubuntu korzystające z uwierzytelniania opartego na hasłach i logowania, a pakiet Cloud-init do konfigurowania maszyn wirtualnych w chmurze mogą mieć wyłączone logowanie oparte na hasłach w trybie failover (w zależności od konfiguracji cloudinit). Logowanie oparte na haśle można włączyć na maszynie wirtualnej przez zresetowanie hasła w menu Ustawienia > > Rozwiązywanie problemów z pomocą techniczną (w przypadku maszyny wirtualnej w trybie failover w Azure Portal.
Debian 7 | [Obsługiwane wersje jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Obsługiwane wersje jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Obsługiwane wersje jądra)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Uaktualnienie maszyn replikowanych z programu SP3 do wersji SP4 nie jest obsługiwane. Jeśli replikowana maszyna została uaktualniona, należy wyłączyć replikację i ponownie włączyć replikację po uaktualnieniu.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6<br/><br/> Uruchamianie jądra zgodnego z systemem Red Hat lub nieprzerwane wydanie jądra 3, 4 & 5 (UEK3, UEK4, UEK5) 


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra Ubuntu dla usługi Azure Virtual Machines

**Usuwanie** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
14,04 LTS | 9,27 | 3.13.0-24-ogólny do 3.13.0-170-Generic,<br/>3.16.0-25-ogólny do 3.16.0-77-Generic,<br/>3.19.0-18-Generic do 3.19.0-80-Generic,<br/>4.2.0-18-Generic do 4.2.0-42-Generic,<br/>4.4.0-21-Generic to 4.4.0-148-Generic,<br/>4.15.0-1023-Azure do 4.15.0-1045 — Azure |
14,04 LTS | 9,26 | 3.13.0-24-ogólny do 3.13.0-170-Generic,<br/>3.16.0-25-ogólny do 3.16.0-77-Generic,<br/>3.19.0-18-Generic do 3.19.0-80-Generic,<br/>4.2.0-18-Generic do 4.2.0-42-Generic,<br/>4.4.0-21-Generic to 4.4.0-148-Generic,<br/>4.15.0-1023-Azure do 4.15.0-1045 — Azure |
14,04 LTS | 9,25 | 3.13.0-24-ogólny do 3.13.0-169-Generic,<br/>3.16.0-25-ogólny do 3.16.0-77-Generic,<br/>3.19.0-18-Generic do 3.19.0-80-Generic,<br/>4.2.0-18-Generic do 4.2.0-42-Generic,<br/>4.4.0-21-Generic to 4.4.0-146-Generic,<br/>4.15.0-1023-Azure do 4.15.0-1042 — Azure |
14,04 LTS | 9,24 | 3.13.0-24-ogólny do 3.13.0-167-Generic,<br/>3.16.0-25-ogólny do 3.16.0-77-Generic,<br/>3.19.0-18-Generic do 3.19.0-80-Generic,<br/>4.2.0-18-Generic do 4.2.0-42-Generic,<br/>4.4.0-21-Generic to 4.4.0-143-Generic,<br/>4.15.0-1023-Azure do 4.15.0-1040 — Azure |
|||
16,04 LTS | 9,27 | 4.4.0-21-Generic to 4.4.0-154-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic do 4.15.0-54-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1050-Azure|
16,04 LTS | 9,26 | 4.4.0-21-Generic to 4.4.0-148-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic do 4.15.0-50-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1045-Azure|
16,04 LTS | 9,25 | 4.4.0-21-Generic to 4.4.0-146-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic to 4.15.0-48-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1042-Azure|
16,04 LTS | 9,24 | 4.4.0-21-Generic to 4.4.0-143-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic do 4.15.0-46-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1040-Azure|

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra Debian dla usługi Azure Virtual Machines

**Usuwanie** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | 9.24,9.25,9.26,9.27 | 3.2.0-4-amd64 do 3.2.0-6-amd64, 3.16.0 -0. BPO. 4-amd64 |
|||
Debian 8 | 9,27 | 3.16.0-4-amd64 do 3.16.0-9-amd64, 4.9.0 -0. BPO. 4-amd64 do 4.9.0 -0. BPO. 9-amd64 |
Debian 8 | 9,25, 9,26 | 3.16.0-4-amd64 do 3.16.0-8-amd64, 4.9.0 -0. BPO. 4-amd64 do 4.9.0 -0. BPO. 8-amd64 |
Debian 8 | 9,24 | 3.16.0-4-amd64 do 3.16.0-7-amd64, 4.9.0 -0. BPO. 4-amd64 do 4.9.0 -0. BPO. 8-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane SUSE Linux Enterprise Server 12 wersji jądra dla usługi Azure Virtual Machines

**Usuwanie** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,27 | SP1 3.12.49-11 — domyślnie 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45 — domyślnie 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69-domyślnie 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73 — domyślnie 4.4.121-92.114-default</br></br>SP3 4.4.73-5 — domyślnie 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-Azure to 4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41-domyślnie 4.12.14-95.19-default</br>SP4 4.12.14-6.3-Azure to 4.12.14-6.15-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,26 | SP1 3.12.49-11 — domyślnie 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45 — domyślnie 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-domyślnie 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73 — domyślnie 4.4.121-92.109-default</br></br>SP3 4.4.73-5 — domyślnie 4.4.178-94.91-default</br></br>SP3 4.4.138-4,7-Azure to 4.4.178-4.28-Azure</br></br>SP4 4.12.14-94.41-domyślnie 4.12.14-95.16-default</br>SP4 4.12.14-6.3-Azure to 4.12.14-6,9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,25 | SP1 3.12.49-11 — domyślnie 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45 — domyślnie 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-domyślnie 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73 — domyślnie 4.4.121-92.104-default</br></br>SP3 4.4.73-5 — domyślnie 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-Azure to 4.4.176-4.25-Azure</br></br>SP4 4.12.14-94.41-domyślnie 4.12.14-95.13-default</br>SP4 4.12.14-6.3-Azure to 4.12.14-6,9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,24 | SP1 3.12.49-11 — domyślnie 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45 — domyślnie 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-domyślnie 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73 — domyślnie 4.4.121-92.104-default</br></br>SP3 4.4.73-5 — domyślnie 4.4.176-94.88-default</br></br>SP4 4.12.14-94.41-domyślnie 4.12.14-95.13-default |


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Zreplikowane maszyny — system plików Linux/magazyn gościa

* Systemy plików: ext3, EXT4, ReiserFS (tylko system SUSE Linux Enterprise Server), XFS, BTRFS
* Menedżer woluminów: LVM2
* Oprogramowanie wielościeżkowe: Mapowanie urządzeń


## <a name="replicated-machines---compute-settings"></a>Zreplikowane maszyny — ustawienia obliczeń

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Size | Rozmiar maszyny wirtualnej platformy Azure z co najmniej 2 rdzeniami procesora i 1 GB pamięci RAM | Sprawdź [rozmiary maszyn wirtualnych platformy Azure](../virtual-machines/windows/sizes.md).
Zestawy dostępności | Obsługiwane | Jeśli włączysz replikację dla maszyny wirtualnej platformy Azure z opcjami domyślnymi, zestaw dostępności zostanie utworzony automatycznie na podstawie ustawień regionu źródłowego. Te ustawienia można modyfikować.
Strefy dostępności | Obsługiwane |
Korzyść użycia hybrydowego (centrum) | Obsługiwane | Jeśli źródłowa maszyna wirtualna ma włączoną licencję centrum, testowej pracy w trybie failover lub maszyny wirtualnej przełączonej do sieci również korzysta z licencji centrum.
Zestawy skalowania maszyn wirtualnych | Nieobsługiwane |
Obrazy z galerii platformy Azure — opublikowane przez firmę Microsoft | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna jest uruchamiana w obsługiwanym systemie operacyjnym.
Obrazy z galerii platformy Azure — opublikowane przez inną firmę | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna jest uruchamiana w obsługiwanym systemie operacyjnym.
Obrazy niestandardowe — Opublikowano przez inną firmę | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna jest uruchamiana w obsługiwanym systemie operacyjnym.
Maszyny wirtualne migrowane przy użyciu Site Recovery | Obsługiwane | Jeśli maszyna wirtualna VMware lub maszyna fizyczna została zmigrowana do platformy Azure przy użyciu Site Recovery, należy odinstalować starszą wersję usługi mobilności działającej na maszynie i ponownie uruchomić maszynę przed replikacją jej do innego regionu platformy Azure.
Zasady RBAC | Nieobsługiwane | Zasady kontroli dostępu opartej na rolach (RBAC) na maszynach wirtualnych nie są replikowane do maszyny wirtualnej trybu failover w regionie docelowym.
Rozszerzenia | Nieobsługiwane | Rozszerzenia nie są replikowane do maszyny wirtualnej trybu failover w regionie docelowym. Należy ją zainstalować ręcznie po przejściu do trybu failover.

## <a name="replicated-machines---disk-actions"></a>Zreplikowane maszyny — akcje dysku

**Akcja** | **Szczegóły**
-- | ---
Zmień rozmiar dysku na zreplikowanej maszynie wirtualnej | Obsługiwane
Dodawanie dysku do zreplikowanej maszyny wirtualnej | Obsługiwane

## <a name="replicated-machines---storage"></a>Zreplikowane maszyny — magazyn

Ta tabela zawiera podsumowanie obsługi dysku systemu operacyjnego Azure VM, dysku danych i dysku tymczasowego.

- Ważne jest, aby obserwować limity dysku maszyny wirtualnej i elementy docelowe dla maszyn wirtualnych z systemami [Linux](../virtual-machines/linux/disk-scalability-targets.md) i [Windows](../virtual-machines/windows/disk-scalability-targets.md) , aby uniknąć problemów z wydajnością.
- W przypadku wdrożenia z ustawieniami domyślnymi program Site Recovery automatycznie tworzy dyski i konta magazynu na podstawie ustawień źródłowych.
- W przypadku dostosowania programu upewnij się, że przestrzegasz wytycznych.

**Składnik** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Maksymalny rozmiar dysku systemu operacyjnego | 2048 GB | [Dowiedz się więcej](../virtual-machines/windows/managed-disks-overview.md) o dyskach maszyn wirtualnych.
Dysk tymczasowy | Nieobsługiwane | Dysk tymczasowy jest zawsze wykluczony z replikacji.<br/><br/> Nie przechowuj żadnych trwałych danych na dysku tymczasowym. [Dowiedz się więcej](../virtual-machines/windows/managed-disks-overview.md).
Maksymalny rozmiar dysku danych | 8192 GB dla dysków zarządzanych<br></br>4095 GB dla dysków niezarządzanych|
Minimalny rozmiar dysku danych | Brak ograniczeń dla dysków niezarządzanych. 2 GB dla dysków zarządzanych | 
Maksymalna liczba dysków danych | Do 64, zgodnie z obsługą określonego rozmiaru maszyny wirtualnej platformy Azure | [Dowiedz się więcej](../virtual-machines/windows/sizes.md) o rozmiarach maszyn wirtualnych.
Szybkość zmian dysku danych | Maksymalnie 10 MB/s na dysk dla usługi Premium Storage. Maksymalnie 2 MB/s na dysk w przypadku magazynu w warstwie Standardowa. | Jeśli średnia szybkość zmian danych na dysku jest ciągle wyższa niż wartość maksymalna, replikacja nie zostanie wystawiona.<br/><br/>  Jeśli jednak maksimum zostanie przekroczone sporadycznie, replikacja może zostać wyświetlona, ale może wystąpić nieco opóźnione punkty odzyskiwania.
Dysk danych — standardowe konto magazynu | Obsługiwane |
Dysk danych — konto magazynu w warstwie Premium | Obsługiwane | Jeśli maszyna wirtualna ma rozłożone dyski w warstwie Premium i w warstwie Standardowa, możesz wybrać inne docelowe konto magazynu dla każdego dysku, aby mieć pewność, że konfiguracja magazynu jest taka sama w regionie docelowym.
Dysk zarządzany — standardowa | Obsługiwane w regionach świadczenia usługi Azure, w których Azure Site Recovery jest obsługiwana. |
Dysk zarządzany — wersja Premium | Obsługiwane w regionach świadczenia usługi Azure, w których Azure Site Recovery jest obsługiwana. |
SSD w warstwie Standardowa | Obsługiwane |
Nadmiarowość | Obsługiwane są LRS i GRS.<br/><br/> ZRS nie jest obsługiwana.
Chłodna i gorąca pamięć masowa | Nieobsługiwane | Dyski maszyn wirtualnych nie są obsługiwane w chłodnym i gorącym magazynie
Miejsca do magazynowania | Obsługiwane |
Szyfrowanie w spoczynku (SSE) | Obsługiwane | Funkcja SSE jest domyślnym ustawieniem na kontach magazynu.   
Azure Disk Encryption (ADE) dla systemu operacyjnego Windows | Obsługiwane są maszyny wirtualne obsługujące [szyfrowanie za pomocą aplikacji usługi Azure AD](https://aka.ms/ade-aad-app) |
Azure Disk Encryption (ADE) dla systemu operacyjnego Linux | Nieobsługiwane |
Dodawanie gorące | Obsługiwane | W przypadku maszyn wirtualnych korzystających z dysków zarządzanych można włączyć replikację dla dysku danych dodawanego do zreplikowanej maszyny wirtualnej platformy Azure.
Gorąca usuwanie dysku | Nieobsługiwane | Jeśli usuniesz dysk z danymi na maszynie wirtualnej, musisz wyłączyć replikację i ponownie włączyć replikację dla maszyny wirtualnej.
Wykluczanie dysku | Pomocy. Aby skonfigurować program, należy użyć [programu PowerShell](azure-to-azure-exclude-disks.md) . |  Dyski tymczasowe są domyślnie wykluczone.
Bezpośrednie miejsca do magazynowania  | Obsługiwane dla punktów odzyskiwania spójnych na poziomie awarii. Punkty odzyskiwania spójne z aplikacjami nie są obsługiwane. |
Serwer plików skalowalny w poziomie  | Obsługiwane dla punktów odzyskiwania spójnych na poziomie awarii. Punkty odzyskiwania spójne z aplikacjami nie są obsługiwane. |
LRS | Obsługiwane |
GRS | Obsługiwane |
RA-GRS | Obsługiwane |
ZRS | Nieobsługiwane |
Chłodna i gorąca pamięć masowa | Nieobsługiwane | Dyski maszyny wirtualnej nie są obsługiwane w chłodnym i gorącym magazynie
Zapory usługi Azure Storage dla sieci wirtualnych  | Obsługiwane | Jeśli Ogranicz dostęp do sieci wirtualnej do kont magazynu, Włącz opcję [Zezwalaj na zaufane usługi firmy Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Konta magazynu ogólnego przeznaczenia w wersji 2 (warstwa gorąca i chłodna) | Tak | Koszty transakcji rosną znacznie w porównaniu z kontami magazynu ogólnego przeznaczenia w wersji 1

>[!IMPORTANT]
> Aby uniknąć problemów z wydajnością, należy się upewnić, że są używane elementy docelowe skalowalności i wydajności dysków maszyny wirtualnej dla maszyn wirtualnych z systemem [Linux](../virtual-machines/linux/disk-scalability-targets.md) lub [Windows](../virtual-machines/windows/disk-scalability-targets.md) . Jeśli używasz ustawień domyślnych, Site Recovery tworzy wymagane dyski i konta magazynu na podstawie konfiguracji źródłowej. Jeśli dostosowujesz i wybierasz własne ustawienia, postępuj zgodnie z celami skalowalności i wydajności dysków dla źródłowych maszyn wirtualnych.

## <a name="limits-and-data-change-rates"></a>Limity i szybkości zmian danych

Poniższa tabela zawiera podsumowanie limitów Site Recovery.

- Limity te są oparte na naszych testach, ale oczywiście nie obejmują wszystkich możliwych kombinacji operacji we/wy aplikacji.
- Rzeczywiste wyniki mogą się różnić w zależności od kombinacji operacji we/wy aplikacji.
- Istnieją dwa ograniczenia, które należy wziąć pod uwagę w przypadku zmian danych na dysku i zmian danych maszyny wirtualnej.
- Przykładowo, jeśli korzystamy z dysku P20 Premium, zgodnie z opisem w poniższej tabeli, Site Recovery może obsłużyć 5 MB zmian na dysk, z maksymalną liczbą pięciu dysków na maszynę wirtualną, ze względu na limit 25 MB/s całkowitej liczby zmian na maszynę wirtualną.

**Miejsce docelowe magazynu** | **Średnia liczba operacji we/wy dysku źródłowego** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB  | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |  336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |20 MB/s | 1684 GB na dysk

## <a name="replicated-machines---networking"></a>Zreplikowane maszyny — sieć
**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
NIC | Maksymalna liczba obsługiwana dla określonego rozmiaru maszyny wirtualnej platformy Azure | Karty sieciowe są tworzone podczas pracy w trybie failover.<br/><br/> Liczba kart sieciowych na maszynie wirtualnej trybu failover zależy od liczby kart sieciowych na źródłowej maszynie wirtualnej, gdy replikacja została włączona. Po dodaniu lub usunięciu karty sieciowej po włączeniu replikacji nie ma to wpływu na liczbę kart sieciowych replikowanych maszyn wirtualnych po przejściu w tryb failover. Należy również pamiętać, że kolejność kart sieciowych po przejściu w tryb failover nie gwarantuje, że jest to taka sama jak oryginalna kolejność.
Internetowy moduł równoważenia obciążenia | Obsługiwane | Skojarz wstępnie skonfigurowany moduł równoważenia obciążenia za pomocą skryptu Azure Automation w planie odzyskiwania.
Wewnętrzny moduł równoważenia obciążenia | Obsługiwane | Skojarz wstępnie skonfigurowany moduł równoważenia obciążenia za pomocą skryptu Azure Automation w planie odzyskiwania.
Publiczny adres IP | Obsługiwane | Skojarz istniejący publiczny adres IP z kartą sieciową. Lub Utwórz publiczny adres IP i skojarz go z kartą sieciową przy użyciu skryptu Azure Automation w planie odzyskiwania.
SIECIOWEJ grupy zabezpieczeń na karcie sieciowej | Obsługiwane | Skojarz sieciowej grupy zabezpieczeń z kartą sieciową przy użyciu skryptu Azure Automation w planie odzyskiwania.
SIECIOWEJ grupy zabezpieczeń w podsieci | Obsługiwane | Skojarz sieciowej grupy zabezpieczeń z podsiecią, używając skryptu Azure Automation w planie odzyskiwania.
Zarezerwowany (statyczny) adres IP | Obsługiwane | Jeśli karta sieciowa na źródłowej maszynie wirtualnej ma statyczny adres IP, a podsieć docelowa ma ten sam adres IP, jest on przypisany do maszyny wirtualnej przełączonej w tryb failover.<br/><br/> Jeśli podsieć docelowa nie ma dostępnego tego samego adresu IP, jeden z dostępnych adresów IP w podsieci jest zarezerwowany dla maszyny wirtualnej.<br/><br/> Można również określić stały adres IP i podsieć w ustawieniach **replikowanych elementów** > **Ustawienia** > elementy**obliczeniowe i** > **sieciowe.**
Dynamiczny adres IP | Obsługiwane | Jeśli karta sieciowa w źródle ma dynamiczne adresowanie IP, karta sieciowa na maszynie wirtualnej w trybie failover jest również domyślnie dynamiczna.<br/><br/> Jeśli jest to wymagane, można je zmodyfikować na stały adres IP.
Wiele adresów IP | Nieobsługiwane | Po przełączeniu maszyny wirtualnej w tryb failover, która ma kartę sieciową z wieloma adresami IP, zachowywany jest tylko podstawowy adres IP karty sieciowej w regionie źródłowym. Aby przypisać wiele adresów IP, można dodać maszyny wirtualne do [planu odzyskiwania](recovery-plan-overview.md) i dołączyć skrypt, aby przypisać dodatkowe adresy IP do planu, lub wprowadzić zmianę ręcznie lub za pomocą skryptu po przejściu do trybu failover. 
Traffic Manager     | Obsługiwane | Można wstępnie skonfigurować Traffic Manager tak, aby ruch był kierowany do punktu końcowego w regionie źródłowym w regularnych odstępach czasu i do punktu końcowego w regionie docelowym w przypadku przejścia w tryb failover.
Usługa DNS platformy Azure | Obsługiwane |
Niestandardowe DNS  | Obsługiwane |
Nieuwierzytelniony serwer proxy | Obsługiwane | [Dowiedz się więcej](site-recovery-azure-to-azure-networking-guidance.md)    
Uwierzytelniony serwer proxy | Nieobsługiwane | Jeśli maszyna wirtualna używa uwierzytelnionego serwera proxy do łączności wychodzącej, nie może być replikowana przy użyciu Azure Site Recovery.    
Połączenie sieci VPN typu lokacja-lokacja z lokalnym<br/><br/>(z lub bez ExpressRoute)| Obsługiwane | Upewnij się, że UDR i sieciowych grup zabezpieczeń są skonfigurowane w taki sposób, że ruch Site Recovery nie jest kierowany do lokalnego. [Dowiedz się więcej](site-recovery-azure-to-azure-networking-guidance.md)    
Połączenie między sieciami WIRTUALNYmi | Obsługiwane | [Dowiedz się więcej](site-recovery-azure-to-azure-networking-guidance.md)  
Punkty końcowe usługi dla sieci wirtualnej | Obsługiwane | W przypadku ograniczania dostępu do sieci wirtualnej do kont magazynu upewnij się, że zaufane usługi firmy Microsoft mają dostęp do konta magazynu.
Przyspieszona sieć | Obsługiwane | Na źródłowej maszynie wirtualnej musi być włączona szybsza sieć. [Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Następne kroki
- Odczytaj [wskazówki dotyczące sieci](site-recovery-azure-to-azure-networking-guidance.md) dotyczące replikacji maszyn wirtualnych platformy Azure.
- Wdróż odzyskiwanie po awarii przez [replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure.md).
