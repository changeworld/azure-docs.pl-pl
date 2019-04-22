---
title: Macierz obsługi na potrzeby odzyskiwania po awarii maszyn wirtualnych platformy Azure między regionami platformy Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie obsługiwanych systemów operacyjnych i konfiguracji do usługi Azure Site Recovery replikacji maszyn wirtualnych (VM) z jednego regionu do innego na potrzeby odzyskiwania po awarii.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 04/16/2019
ms.author: raynew
ms.openlocfilehash: 58d7aeb3c710610d93eda09b37374a167b444bd0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679010"
---
# <a name="support-matrix-for-replicating-azure-vms-from-one-region-to-another"></a>Macierz obsługi na potrzeby replikacji maszyn wirtualnych platformy Azure z jednego regionu do innego

Ten artykuł zawiera podsumowanie obsługiwanych konfiguracji i składników podczas wdrażania odzyskiwania po awarii dzięki replikacji, trybu failover i odzyskiwania maszyn wirtualnych platformy Azure między regionami platformy Azure do innego, za pomocą [usługi Azure Site Recovery](site-recovery-overview.md) usługi.


## <a name="deployment-method-support"></a>Obsługa metody wdrażania

**Wdrożenie** |  **Pomoc techniczna**
--- | ---
**Azure Portal** | Obsługiwane.
**Program PowerShell** | Obsługiwane. [Dowiedz się więcej](azure-to-azure-powershell.md)
**Interfejs API REST** | Obsługiwane.
**Interfejs wiersza polecenia** | Nie jest obecnie obsługiwany


## <a name="resource-support"></a>Obsługa zasobów

**Akcja zasobu** | **Szczegóły**
--- | --- | ---
**Przenieś magazynów między grupami zasobów** | Nieobsługiwane
**Przenoszenie zasobów obliczeniowych i magazynu/sieci między grupami zasobów** | Nieobsługiwane.<br/><br/> Po maszyny Wirtualnej jest replikowany, przenieść Maszynę wirtualną lub skojarzone składniki, takie jak storage i sieci, należy wyłączyć i ponownie włączyć replikację dla maszyny Wirtualnej.
**Replikowanie maszyn wirtualnych platformy Azure z jednej subskrypcji do innej na potrzeby odzyskiwania po awarii** | Obsługiwane w ramach tej samej dzierżawie usługi Azure Active Directory.
**Migrowanie maszyn wirtualnych między regionami w ramach klastrów geograficzne obsługiwane (wewnątrz i między subskrypcjami)** | Obsługiwane w ramach tej samej dzierżawie usługi Azure Active Directory.
**Migrowanie maszyn wirtualnych w tym samym regionie** | Nieobsługiwane.

## <a name="region-support"></a>Obsługa regionu

Można replikować i odzyskiwanie maszyn wirtualnych między wszystkie dwóch regionach w obrębie tego samego klastra geograficznego. Klastry geograficzne są zdefiniowane, pamiętając o opóźnieniu przesyłania danych i niezależność danych.


**Geograficzne klastra** | **Regiony platformy Azure**
-- | --
Ameryka | Kanada Wschodnia, środkowe stany USA Kanada środkowe, południowo-, Zachodnia środkowe stany USA, wschodnie stany USA, wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2, środkowe stany USA, Północnośrodkowa
Europa | Zachodnie Zjednoczone Królestwo, południowe Zjednoczone Królestwo, Europa Północna, Europa Zachodnia, Francja środkowa, Francja Południowa
Azja | Indie Południowe, Indie środkowe, Azja południowo-wschodnia, Azja Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa
Australia   | Australia Wschodnia, Australia Południowo-Wschodnia, Australia Środkowa, Australia Środkowa 2
Azure Government    | Administracja USA — Wirginia, administracja USA — Iowa, administracja USA — Arizona, administracja USA — Teksas, US DOD wschodnie stany, US dod — środkowe
Niemcy | Niemcy środkowe, Niemcy północno-wschodnie
Chiny | Chiny wschodnie, Chiny Północne, Chiny North2, 2 (Chiny)

>[!NOTE]
>
> - Aby uzyskać **Brazylia Południowa**, możesz zreplikować i awaryjnej w tych regionach: Południowo-środkowe stany USA, zachodnie środkowe stany USA, wschodnie stany USA, wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2 i Północnośrodkowa.
> - Brazylia Południowa należy używać tylko jako region źródła, z którego maszyny wirtualne można replikować przy użyciu Site Recovery. Nie może pełnić roli regionie docelowym. Jest to z powodu problemów z opóźnieniem z powodu geograficznej odległości.
> - Możesz pracować w regionach, do których użytkownik ma odpowiedni dostęp.
> - Jeśli region, w którym chcesz utworzyć magazyn nie pokazuje, upewnij się, że Twoja subskrypcja ma dostęp do tworzenia zasobów w danym regionie.
> - Jeśli nie widzisz region w ramach klastra geograficzne po włączeniu replikacji, upewnij się, że Twoja subskrypcja ma uprawnienia do tworzenia maszyn wirtualnych w tym regionie.



## <a name="cache-storage"></a>Magazyn pamięci podręcznej

Ta tabela zawiera podsumowanie obsługi dla konta magazynu pamięci podręcznej, które są używane przez usługę Site Recovery podczas replikacji.

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Kont ogólnego przeznaczenia V2 storage (gorąca i chłodna warstwa) | Nieobsługiwane. | To ograniczenie istnieje dla magazynu pamięci podręcznej, ponieważ koszty transakcji dla wersji 2 są znacznie wyższe niż w wersji 1 konta magazynu.
Usługa Azure Storage zapory dla sieci wirtualnych  | Obsługiwane | Jeśli używasz konta magazynu pamięci podręcznej włączona jest Zapora lub docelowe konto magazynu, upewnij się, że ["Zezwalaj na zaufane usługi firmy Microsoft"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## <a name="replicated-machine-operating-systems"></a>Systemy operacyjne replikowanych maszyn

Usługa Site Recovery obsługuje replikację maszyn wirtualnych platformy Azure z systemami operacyjnymi, wymienione w tej sekcji.

### <a name="windows"></a>Windows

**System operacyjny** | **Szczegóły**
--- | ---
Windows Server 2019 |
Windows Server 2016  | Instalacja Server Core, serwer ze środowiskiem pulpitu
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Z dodatkiem SP1 lub nowszym

#### <a name="linux"></a>Linux

**System operacyjny** | **Szczegóły**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6
Ubuntu 14.04 LTS Server | [Wersje obsługiwanych jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [Wersja jądra obsługiwane](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Serwery systemu Ubuntu przy użyciu uwierzytelniania opartego na hasłach i logowania oraz pakietu cloud-init do konfigurowania chmur maszyn wirtualnych, może być logowania opartego na hasłach, wyłączona w trybie failover (w zależności od konfiguracji cloudinit). Logowania opartego na hasłach, można ponownie włączyć na maszynie wirtualnej poprzez zresetowanie hasła z obsługi > Rozwiązywanie problemów > menu Ustawienia (w trybie Failover maszyny Wirtualnej w witrynie Azure portal.
Debian 7 | [Wersje obsługiwanych jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Wersje obsługiwanych jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1,SP2,SP3,SP4. [(Wersje jądra obsługiwane)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Replikowanie maszyn z dodatkiem SP3 do SP4 uaktualnienie nie jest obsługiwane. Jeśli został uaktualniony replikowanej maszyny, należy wyłączyć replikację i ponownie włączyć replikację po uaktualnieniu.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 <br/><br/> System Red Hat zgodne jądra lub podzielenie Enterprise jądra wersji 3 (UEK3).


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra systemu Ubuntu maszyn wirtualnych platformy Azure

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
14.04 LTS | 9.23 | 3.13.0-24-Generic do 3.13.0-165-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-142-generic,<br/>4.15.0-1023-Azure do 4.15.0-1037-azure |
14.04 LTS | 9.22 | 3.13.0-24-Generic do 3.13.0-164-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-140-generic,<br/>4.15.0-1023-Azure do 4.15.0-1036-azure |
14.04 LTS | 9.21 | 3.13.0-24-Generic do 3.13.0-163-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-140-generic,<br/>4.15.0-1023-Azure do 4.15.0-1035-azure |
14.04 LTS | 9.20 | 3.13.0-24-Generic do 3.13.0-161-generic,<br/>3.16.0-25-Generic do 3.16.0-77-generic,<br/>3.19.0-18-Generic do 3.19.0-80-generic,<br/>4.2.0-18-Generic do 4.2.0-42-generic,<br/>4.4.0-21-Generic do 4.4.0-138-generic,<br/>4.15.0-1023-Azure do 4.15.0-1030-azure |
|||
16.04 LTS | 9.23 | 4.4.0-21-Generic do 4.4.0-142-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-45-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1037-azure|
16.04 LTS | 9.22 | 4.4.0-21-Generic do 4.4.0-140-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-43-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1036-azure|
16.04 LTS | 9.21 | 4.4.0-21-Generic do 4.4.0-140-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-42-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1035-azure|
16.04 LTS | 9.20 | 4.4.0-21-Generic do 4.4.0-138-generic,<br/>4.8.0-34-Generic do 4.8.0-58-generic,<br/>4.10.0-14-Generic do 4.10.0-42-generic,<br/>4.11.0-13-Generic do 4.11.0-14-generic,<br/>4.13.0-16-Generic do 4.13.0-45-generic,<br/>4.15.0-13-Generic do 4.15.0-38-generic<br/>4.11.0-1009-Azure do 4.11.0-1016-azure,<br/>4.13.0-1005-Azure do 4.13.0-1018-azure <br/>4.15.0-1012-Azure do 4.15.0-1030-azure|

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra systemu Debian dla maszyn wirtualnych platformy Azure

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | 9.19,9.20,9.21,9.22,9.23 | 3.2.0-4-AMD64 do 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.20, 9.21,9.22, 9.23 | 3.16.0-4-AMD64 do 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.8-amd64 |
Debian 8 | 9.19 | 3.16.0-4-AMD64 do 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.7-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra systemu SUSE Linux Enterprise Server 12 maszyn wirtualnych platformy Azure

**Wydania** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.23 | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.101-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.162-94.79-default</br></br>Z dodatkiem SP4 4.12.14-94.41-default do 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.22 | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.98-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.21 | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.98-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.20 | Z dodatkiem SP1 3.12.49-11-default do 3.12.74-60.64.40-default</br></br> 3.12.74-60.64.45-default SP1(LTSS) do 3.12.74-60.64.107-default</br></br> Z dodatkiem SP2 4.4.21-69-default do 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) do 4.4.121-92.98-default</br></br>Z dodatkiem SP3 4.4.73-5-default do 4.4.162-94.69-default |


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikowane maszyny - magazyn systemu/gościa pliku systemu Linux

* Systemy plików: ext3, ext4, ReiserFS (tylko w systemie Suse Linux Enterprise Server), XFS, BTRFS
* Menedżer woluminów: LVM2
* Oprogramowanie wielościeżkowe: Mapowanie urządzeń


## <a name="replicated-machines---compute-settings"></a>Replikowane maszyny — ustawienia obliczeń

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Rozmiar | Dowolny rozmiar maszyny Wirtualnej platformy Azure, co najmniej 2 rdzeni procesora CPU i 1 GB pamięci RAM | Sprawdź [rozmiarów maszyn wirtualnych platformy Azure](../virtual-machines/windows/sizes.md).
Zestawy dostępności | Obsługiwane | Po włączeniu replikacji dla maszyny Wirtualnej platformy Azure z opcjami domyślnymi, zestaw dostępności jest tworzony automatycznie w oparciu o ustawienia regionu źródłowego. Te ustawienia można modyfikować.
Strefy dostępności | Obsługiwane |
Korzyści z używania hybrydowej (HUB) | Obsługiwane | Jeśli źródłowa maszyna wirtualna ma włączone, licencję Centrum testowania trybu failover lub przełączone w tryb failover maszyny Wirtualnej używa również licencji KONCENTRATORA.
Zestawy skalowania maszyn wirtualnych | Nieobsługiwane |
Obrazy z galerii platformy Azure — Microsoft opublikowane | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna uruchamia się w obsługiwanym systemie operacyjnym.
Obrazy galerii platformy Azure — innych firm opublikowane | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna uruchamia się w obsługiwanym systemie operacyjnym.
Obrazy niestandardowe - innych firm opublikowane | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna uruchamia się w obsługiwanym systemie operacyjnym.
Maszyny wirtualne migrowane przy użyciu Site Recovery | Obsługiwane | Jeśli maszyna wirtualna VMware lub komputera fizycznego został zmigrowany na platformie Azure przy użyciu Site Recovery, musisz odinstalować starszej wersji usługi mobilności uruchomioną na maszynie i uruchom ponownie maszynę przed zreplikowaniem go do innego regionu platformy Azure.
Zasady RBAC | Nieobsługiwane | Oparta na rolach dostęp do zasad kontroli (RBAC) na maszynach wirtualnych nie są replikowane do trybu failover maszyny Wirtualnej w regionie docelowym.

## <a name="replicated-machines---disk-actions"></a>Replikowane maszyny - działań dysku

**Akcja** | **Szczegóły**
-- | ---
Zmiana rozmiaru dysku na zreplikowanej maszyny Wirtualnej | Obsługiwane
Dodawanie dysku do zreplikowanej maszyny Wirtualnej | Nieobsługiwane.<br/><br/> Użytkownik musi można wyłączyć replikacji dla maszyny Wirtualnej, Dodaj dysk, a następnie włącz ponownie replikację.

## <a name="replicated-machines---storage"></a>Replikowane maszyny - storage

Ta tabela zawiera podsumowanie obsługę dysku, dysk z danymi i tymczasowy dysk systemu operacyjnego maszyny Wirtualnej platformy Azure.

- Ważne jest, aby obserwować limity dysku maszyny Wirtualnej i elementy docelowe [Linux](../virtual-machines/linux/disk-scalability-targets.md) i [Windows](../virtual-machines/windows/disk-scalability-targets.md) maszyn wirtualnych, aby uniknąć problemów z wydajnością.
- W przypadku wdrożenia przy użyciu ustawień domyślnych, Usługa Site Recovery automatycznie tworzy disks i kont magazynu, w oparciu o ustawienia źródła.
- W przypadku dostosowania, upewnij się, że postępuj zgodnie z wytycznymi.

**Składnik** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Maksymalny rozmiar dysku systemu operacyjnego | 2048 GB | [Dowiedz się więcej](../virtual-machines/windows/managed-disks-overview.md) dotyczące dysków maszyn wirtualnych.
Dysk tymczasowy | Nieobsługiwane | Dysk tymczasowy zawsze jest wykluczony z replikacji.<br/><br/> Nie należy przechowywać żadnych trwałych danych na dysku tymczasowym. [Dowiedz się więcej](../virtual-machines/windows/managed-disks-overview.md).
Maksymalny rozmiar dysku danych | 4095 GB |
Maksymalna liczba dysków danych | Maksymalnie 64 zgodnie z pomocy technicznej dla określonego rozmiaru maszyny Wirtualnej platformy Azure | [Dowiedz się więcej](../virtual-machines/windows/sizes.md) o rozmiarach maszyn wirtualnych.
Częstotliwość zmian dysku danych | Maksymalnie 10 MB/s na dysk usługi premium storage. Maksymalnie 2 MB/s na dysk dla magazynu w warstwie standardowa. | Jeśli średni współczynnik zmian na dysku jest stale wyższa niż wartość maksymalna, replikacji nie będzie zapoznać się z nimi.<br/><br/>  Jednak maksymalny po przekroczeniu sporadycznie, replikację można zapoznać się z nimi, ale może zostać wyświetlony punktów odzyskiwania nieco opóźnione.
Dysk danych — konto magazynu w warstwie standardowa | Obsługiwane |
Dysk danych — konto magazynu premium storage | Obsługiwane | Maszyna wirtualna ma dyski rozkładają się na konta magazynu w warstwie standardowa i premium, po wybraniu innego docelowego konta magazynu dla każdego dysku, aby upewnić się, że mają taką samą konfigurację magazynu w regionie docelowym.
Dysk zarządzany — standardowa | Obsługiwane regiony systemu Azure, w których usługa Azure Site Recovery jest obsługiwany. |
Dysk zarządzany — premium | Obsługiwane regiony systemu Azure, w których usługa Azure Site Recovery jest obsługiwany. |
Standardowa, SSD | Obsługiwane |
Nadmiarowość | Usługi LRS i GRS są obsługiwane.<br/><br/> Magazyn ZRS nie jest obsługiwane.
Chłodnej i gorącej magazynu. | Nieobsługiwane | Dyski maszyny Wirtualnej nie są obsługiwane w chłodnej i gorącej magazynu.
Miejsca do magazynowania | Obsługiwane |
Szyfrowanie danych magazynowanych (SSE) | Obsługiwane | Funkcja SSE jest ustawieniem domyślnym dla kont magazynu.   
Usługa Azure Disk Encryption (ADE) dla systemu operacyjnego Windows | Włączone dla maszyn wirtualnych [szyfrowanie za pomocą aplikacji usługi Azure AD](https://aka.ms/ade-aad-app) są obsługiwane |
Usługa Azure Disk Encryption (ADE) dla systemu Linux, systemu operacyjnego | Nieobsługiwane |
Dodaj lub usuń gorąco dysku | Nieobsługiwane | Dodaj lub Usuń dysk danych na maszynie Wirtualnej, należy wyłączyć replikację i włącz ponownie replikację maszyny Wirtualnej.
Wykluczanie dysku | Pomoc techniczna. Należy użyć [Powershell](azure-to-azure-exclude-disks.md) do skonfigurowania. |  Dyski tymczasowe są wyłączone domyślnie.
Bezpośrednie miejsca do magazynowania  | Obsługiwane w przypadku punktów odzyskiwania spójnego na poziomie awarii. Punkty odzyskiwania zapewniających spójność aplikacji nie są obsługiwane. |
Serwer plików skalowalny w poziomie  | Obsługiwane w przypadku punktów odzyskiwania spójnego na poziomie awarii. Punkty odzyskiwania zapewniających spójność aplikacji nie są obsługiwane. |
LRS | Obsługiwane |
GRS | Obsługiwane |
RA-GRS | Obsługiwane |
ZRS | Nieobsługiwane |
Chłodnej i gorącej magazynu. | Nieobsługiwane | Dyski maszyny wirtualnej nie są obsługiwane w chłodnej i gorącej magazynu.
Usługa Azure Storage zapory dla sieci wirtualnych  | Obsługiwane | Jeśli ograniczanie dostępu do sieci wirtualnej do konta magazynu, należy włączyć [dozwolonych zaufanych usług firmy Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Konta magazynu ogólnego przeznaczenia w wersji 2 (zarówno gorąca i chłodna warstwa) | Nie | Wzrost kosztów transakcji znacznie w porównaniu do ogólnego przeznaczenia w wersji 1 konta magazynu

>[!IMPORTANT]
> Aby uniknąć problemów z wydajnością, upewnij się, postępuj zgodnie z maszyny Wirtualnej cele skalowalności i wydajności dysku dla [Linux](../virtual-machines/linux/disk-scalability-targets.md) lub [Windows](../virtual-machines/windows/disk-scalability-targets.md) maszyn wirtualnych. Jeśli używasz ustawienia domyślne, Site Recovery tworzy wymagane dyski i kont magazynu, w oparciu o konfigurację źródła. Jeśli możesz dostosować i wybrać własne ustawienia, należy wykonać cele skalowalności i wydajności dysku, źródłowe maszyny wirtualne.

## <a name="limits-and-data-change-rates"></a>Limity i danych częstotliwość zmiany

W poniższej tabeli przedstawiono limity Site Recovery.

- Te limity są oparte na naszych testach, ale oczywiście nie obejmują wszystkich możliwych operacji We/Wy kombinacji aplikacji.
- Rzeczywiste wyniki mogą się różnić zależnie od różnych aplikacji we/wy.
- Istnieją dwa ograniczenia należy wziąć pod uwagę na dysk danych na dane maszyny wirtualnej postępów i współczynnika zmian.
- Na przykład jeśli używamy dysk Premium P20, zgodnie z opisem w poniższej tabeli, Usługa Site Recovery może obsługiwać 5 MB, o współczynniku dysku, za pomocą na maksymalnie pięciu tych dysków na maszynę Wirtualną, ze względu na limit wynoszący 25 MB/s łącznym dziennym współczynniku maszyny Wirtualnej.

**Cel magazynu** | **We/Wy dysku średni źródłowy** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB  | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |  336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |20 MB/s | 1684 GB na dysk

## <a name="replicated-machines---networking"></a>Replikowane maszyny — sieci
**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
NIC | Maksymalna liczba obsługiwanych dla określonego rozmiaru maszyny Wirtualnej platformy Azure | Karty sieciowe są tworzone podczas tworzenia maszyny Wirtualnej podczas pracy awaryjnej.<br/><br/> Liczba kart sieciowych na maszynie Wirtualnej w trybie failover zależy od liczba kart sieciowych źródłowej maszyny Wirtualnej po włączeniu replikacji. Jeśli dodasz lub usuniesz kartę Sieciową po włączeniu replikacji, wpływa nie liczba kart sieciowych zreplikowaną maszyną Wirtualną po włączeniu trybu failover.
Internetowy moduł równoważenia obciążenia | Obsługiwane | Kojarzenie modułu równoważenia obciążenia wstępnie skonfigurowane, za pomocą skryptu usługi Azure Automation w planie odzyskiwania.
Wewnętrzny moduł równoważenia obciążenia | Obsługiwane | Kojarzenie modułu równoważenia obciążenia wstępnie skonfigurowane, za pomocą skryptu usługi Azure Automation w planie odzyskiwania.
Publiczny adres IP | Obsługiwane | Kojarzenie istniejącego publicznego adresu IP z karty sieciowej. Lub, tworzenie publicznego adresu IP i skojarz ją z kartą Sieciową za pomocą skryptu usługi Azure Automation w planie odzyskiwania.
Sieciowa grupa zabezpieczeń na karcie interfejsu Sieciowego | Obsługiwane | Kojarzenie sieciowej grupy zabezpieczeń z kartą Sieciową za pomocą skryptu usługi Azure Automation w planie odzyskiwania.
Sieciowa grupa zabezpieczeń w podsieci | Obsługiwane | Kojarzenie sieciowej grupy zabezpieczeń z podsiecią, za pomocą skryptu usługi Azure Automation w planie odzyskiwania.
Zastrzeżony adres IP (statyczne) | Obsługiwane | Jeśli karta sieciowa źródłowej maszyny Wirtualnej ma statyczny adres IP, a podsieć docelowa ma ten sam adres IP dostępne, jest przypisany do w trybie Failover maszyny Wirtualnej.<br/><br/> Jeśli podsieć docelowa nie ma ten sam adres IP dostępny, jeden z dostępnych adresów IP w podsieci jest zarezerwowany dla maszyny Wirtualnej.<br/><br/> Można również określić stały adres IP i podsieci w **zreplikowane elementy** > **ustawienia** > **obliczenia i sieć**  >  **Interfejsy sieciowe**.
Dynamiczny adres IP | Obsługiwane | Karta sieciowa w źródle ma dynamicznych adresów IP kart Sieciowych w trybie Failover maszyny Wirtualnej jest również dynamiczne domyślnie.<br/><br/> Możesz zmodyfikować to stały adres IP w razie potrzeby.
Wiele adresów IP | Nieobsługiwane | Po przełączeniu w tryb failover maszyny Wirtualnej, która ma kartę Sieciową z wieloma adresami IP jest przechowywana tylko podstawowy adres IP karty Sieciowej w regionie źródłowym. Aby przypisać wiele adresów IP, można dodać maszyny wirtualne do [planu odzyskiwania](recovery-plan-overview.md) i dołączyć skrypt do przypisywania dodatkowych adresów IP do planu, lub można wprowadzić zmiany ręcznie lub za pomocą skryptu po włączeniu trybu failover. 
Traffic Manager     | Obsługiwane | Tak, aby ruch jest kierowany do punktu końcowego w regionie źródłowym w regularnych odstępach czasu, a do punktu końcowego w regionie docelowym w przypadku trybu failover, można wstępnie skonfigurować usługi Traffic Manager.
System DNS platformy Azure | Obsługiwane |
Niestandardowe DNS  | Obsługiwane |
Nieuwierzytelnione serwera proxy | Obsługiwane | [Dowiedz się więcej]. (site-recovery-azure-to-azure-networking-guidance.md)   
Uwierzytelnionego serwera Proxy | Nieobsługiwane | Jeśli maszyna wirtualna korzysta z uwierzytelnionego serwera proxy dla połączenia wychodzącego, nie może być replikowane za pomocą usługi Azure Site Recovery.    
Połączenie lokacja lokacja sieci VPN do sieci lokalnej<br/><br/>(z lub bez usługi ExpressRoute)| Obsługiwane | Upewnij się, że tras zdefiniowanych przez użytkownika i sieciowymi grupami zabezpieczeń są skonfigurowane w taki sposób, ruch odzyskiwania lokacji nie jest kierowany do sieci lokalnej. [Dowiedz się więcej](site-recovery-azure-to-azure-networking-guidance.md)    
Połączenie między sieciami Wirtualnymi | Obsługiwane | [Dowiedz się więcej](site-recovery-azure-to-azure-networking-guidance.md)  
Punkty końcowe usługi sieci wirtualnej | Obsługiwane | Jeśli jest ograniczenie dostępu sieci wirtualnej, dla kont magazynu, upewnij się, że zaufanych usług firmy Microsoft będą miały dostęp do konta magazynu.
Wydajniejsze sieci | Obsługiwane | Przyspieszona sieć musi być włączona na źródłowej maszynie Wirtualnej. [Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Kolejne kroki
- Odczyt [sieć wskazówki](site-recovery-azure-to-azure-networking-guidance.md) replikowania maszyn wirtualnych platformy Azure.
- Wdrażanie odzyskiwania po awarii przez [replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure.md).
