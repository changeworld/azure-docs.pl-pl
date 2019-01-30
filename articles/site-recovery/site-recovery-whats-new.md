---
title: Co nowego w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie nowych funkcji wprowadzonych w usłudze Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: raynew
ms.openlocfilehash: 61e66a19b625141c69a9473373d3d5d808e18fde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211121"
---
# <a name="whats-new-in-site-recovery"></a>Co nowego w usłudze Site Recovery

[Usługi Azure Site Recovery](site-recovery-overview.md) usługi są aktualizowane i ulepszony w sposób ciągły. Aby ułatwić Ci najnowsze informacje, w tym artykule dostarcza informacje o najnowszych wersji, nowe funkcje i nową zawartość. Ta strona jest aktualizowana w regularnych odstępach czasu.

Jeśli masz sugestie dotyczące funkcji odzyskiwania lokacji, dlatego chcielibyśmy [czekamy na Twoją opinię](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>Q1 2019

### <a name="linux-support"></a>Obsługa systemu Linux

[Pakiet zbiorczy 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) zapewnia aktualizacji agentów Site Recovery i dostawców. Aktualizacje dodaje obsługę systemu Linux w następujący sposób:

- **Odzyskiwanie po awarii maszyn wirtualnych platformy Azure**: Stacja robocza RedHat 6 7 dni w tygodniu; nowe wersje jądra dla systemu Ubuntu, Debian i SUSE.
- **Odzyskiwanie po awarii serwerów fizycznych i maszyn wirtualnych VMware do platformy Azure**: RedHat Enterprise Linux 7.6; Stacja robocza RedHat 6 7 dni w tygodniu; Oracle Linux 6.10/7.6 nowe wersje jądra systemu Ubuntu, Debian i SUSE.



## <a name="q4-2018"></a>KWARTAŁ 4 2018 R.

## <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Kalkulator cen na potrzeby odzyskiwania po awarii maszyny Wirtualnej platformy Azure

Odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure jest naliczana opłata, koszty licencjonowania dla maszyn wirtualnych i sieci i obniżenie kosztów magazynowania. Platforma Azure udostępnia [Kalkulator cen](https://aka.ms/a2a-cost-estimator) pomagające ustalić te koszty. Usługa Site Recovery udostępnia teraz [przykładowe ceny oszacowanie](https://aka.ms/a2a-cost-estimator) cen Przykładowe wdrożenie oparte na aplikację trójwarstwową, za pomocą sześciu maszyn wirtualnych z 12 dysków standardowych dysków Twardych i 6 dysków w warstwie Premium SSD. Przykład zakładają zmian danych 10 GB na dzień dla warstwy standardowej i 20 GB dla warstwy premium. Dla danego wdrożenia możesz zmienić zmienne do szacowania kosztów. Można określić liczbę maszyn wirtualnych, liczbę i rodzaj dysków zarządzanych, a łączna ilość oczekiwanych danych Zmień częstotliwość oczekuje na maszyny wirtualne. Ponadto można stosować współczynnika kompresji, aby oszacować koszty przepustowości. [Odczyt](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) anonsu.

### <a name="support-for-azure-vms-in-zones"></a>Obsługa maszyn wirtualnych platformy Azure w strefach

Strefy dostępności platformy Azure to unikatowe fizycznie lokalizacje w regionie platformy Azure. Każda strefa składa się z co najmniej jeden centrów danych, wyposażone w niezależne zasilanie, chłodzenie i usługi sieciowe. Można teraz włączyć replikację dla maszyny Wirtualnej platformy Azure i ustawić cel dla trybu failover do pojedynczego wystąpienia maszyny Wirtualnej, Maszynę wirtualną w zestawie dostępności lub maszyny Wirtualnej w strefie dostępności. To ustawienie nie ma wpływu na replikację. [Odczyt](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) anonsu.
 
### <a name="disaster-recovery-for-encrypted-vms"></a>Odzyskiwanie po awarii dla szyfrowanych maszyn wirtualnych

Usługa Site Recovery obsługuje maszyny wirtualne platformy Azure, zaszyfrowany za pomocą szyfrowania dysków Azure (ADE) za pomocą aplikacji usługi Azure AD. [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-ade-vms.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Odzyskiwanie po awarii dla maszyn wirtualnych przy użyciu przyspieszoną sieć

Przyspieszona sieć Włącz wirtualizację we/wy pojedynczego elementu głównego (SR-IOV) do maszyny Wirtualnej, zwiększanie wydajności sieci. Po włączeniu replikacji dla maszyny Wirtualnej platformy Azure Usługa Site Recovery wykrywa, czy przyspieszona sieć jest włączona. Jeśli tak jest, po pracy awaryjnej usługi Site Recovery automatycznie konfiguruje przyspieszoną sieć dla docelowej repliki maszyny Wirtualnej platformy Azure, dla obu [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) i [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Automatyczne aktualizacje dla rozszerzenia usługi mobilności

Usługa Site Recovery dodać opcję automatycznych aktualizacji do rozszerzenia usługi mobilności. Rozszerzenie usługi mobilności jest zainstalowany na każdej maszynie Wirtualnej platformy Azure replikowane przez usługę Site Recovery. Po włączeniu replikacji, zdecyduj, czy umożliwić odzyskiwanie lokacji do zarządzania aktualizacjami do rozszerzenia. Aktualizacje nie wymagają ponownego uruchomienia maszyny Wirtualnej i nie mają wpływu na replikację. [Dowiedz się więcej](azure-to-azure-autoupdate.md).

### <a name="support-for-standard-ssd-disks"></a>Obsługa dysków SSD w warstwie standardowa

Azure wprowadzona [pełny stan dyski (SSD w warstwie standardowa)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd) dysków w celu zapewnienia rozwiązania ekonomicznego magazynowania dla aplikacji, takich jak serwery sieci web, które muszą spójną wydajność, ale nie ma wysokiego dysku na SEKUNDĘ. Łączą w sobie elementy premium SSD i HDD dyski w warstwie standardowa. Usługa Site Recovery zapewnia odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu dysków SSD w warstwie standardowa. Domyślnie typ dysku są zachowywane po przejściu w tryb failover w regionie docelowym.

### <a name="support-for-azure-storage-firewall"></a>Obsługa zapory usługi Azure storage

Konta usługi Azure storage do określonego zestawu sieci można zabezpieczyć, włączając reguły zapory dla konta. Konfigurowanie kont magazynu w celu blokowania ruchu z sieci wewnętrznej i Internetu domyślnie następnie przyznać dostęp do ruchu z określonych sieci wirtualnych. Usługa Site Recovery obsługuje replikację dla maszyn wirtualnych z dyskami niezarządzanymi na kontach magazynów z włączoną zaporę do regionu pomocniczego. W regionie docelowym w przypadku dysków niezarządzanych można wybrać konta magazynu z włączoną zaporą. Można również ograniczyć dostęp do konta magazynu pamięci podręcznej przez ograniczenie dostępu do sieci tylko sieci, w którym znajdują się źródłowe maszyny wirtualne. Należy pamiętać, że musisz [zezwolić na dostęp](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) dla zaufanych usług firmy Microsoft.

## <a name="q3-2018"></a>K3 2018 R. 

### <a name="linux-support"></a>Obsługa systemu Linux

#### <a name="update-rollup-28"></a>Pakiet zbiorczy aktualizacji 28

[Pakiet zbiorczy 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) zapewnia aktualizacji agentów Site Recovery i dostawców. Aktualizacje dodaje obsługę systemu Linux w następujący sposób:

- **Odzyskiwanie po awarii maszyn wirtualnych platformy Azure**: RedHat Enterprise Linux 6.10; CentOS 6.10; Opartą na systemie Linux maszyn wirtualnych Użyj styl partycji (GPT tabela) identyfikator GUID partycji w starszym trybie zgodności systemu BIOS są teraz obsługiwane.
- **Odzyskiwanie po awarii serwerów fizycznych i maszyn wirtualnych VMware do platformy Azure**: RedHat Enterprise Linux 6.10; CentOS 6.10; Opartą na systemie Linux maszyn wirtualnych Użyj styl partycji (GPT tabela) identyfikator GUID partycji w starszym trybie zgodności systemu BIOS są teraz obsługiwane.

#### <a name="update-rollup-27"></a>Pakiet zbiorczy aktualizacji 27

[Pakiet zbiorczy 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) zapewnia aktualizacji agentów Site Recovery i dostawców. Aktualizacje dodaje obsługę systemu Linux w następujący sposób:

- **Odzyskiwanie po awarii maszyn wirtualnych platformy Azure**: Red Hat Enterprise Linux 7.5
- **Odzyskiwanie po awarii serwerów fizycznych i maszyn wirtualnych VMware do platformy Azure**: SUSE Linux Enterprise Server 12, Red Hat Enterprise Linux 7.5

### <a name="support-for-azure-vms-running-on-windows-server-2016"></a>Obsługa maszyn wirtualnych platformy Azure w systemie Windows Server 2016

Maszyny wirtualne platformy Azure, uruchomiony w systemie Windows Server 2016 mogą być replikowane między regionami platformy Azure za pomocą usługi Azure Site Recovery.

### <a name="support-for-azure-vms-running-storage-spaces-direct"></a>Obsługa usługi Azure maszyny wirtualne z bezpośrednimi miejscami do magazynowania

[Bezpośrednimi miejscami do magazynowania](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) (dostępnej w systemie Windows Server 2016 i nowszych wersjach) umożliwia grupowanie dysków do puli magazynów, a następnie używa pojemności z puli, aby utworzyć miejsca do magazynowania. Miejsca do magazynowania może być używany w autonomicznej maszyny Wirtualnej na [klastra gościa maszyn wirtualnych platformy Azure](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) przy użyciu lokalnego magazynu w każdym węźle klastra lub magazyn udostępniony w klastrze.

## <a name="next-steps"></a>Kolejne kroki

Zapewnianie aktualności za pomocą naszej aktualizacji na [aktualizacje platformy Azure](https://azure.microsoft.com/updates/?product=site-recovery) strony.


