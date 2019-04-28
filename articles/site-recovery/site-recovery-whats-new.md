---
title: Co nowego w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie nowych funkcji wprowadzonych w usłudze Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: raynew
ms.openlocfilehash: 5ee1328dddb6ae1e1c878384097b0e10aa32feeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776292"
---
# <a name="whats-new-in-site-recovery"></a>Co nowego w usłudze Site Recovery

[Usługi Azure Site Recovery](site-recovery-overview.md) usługi są aktualizowane i ulepszony w sposób ciągły. Aby ułatwić Ci najnowsze informacje, w tym artykule dostarcza informacje o najnowszych wersji, nowe funkcje i nową zawartość. Ta strona jest aktualizowana w regularnych odstępach czasu.

Jeśli masz sugestie dotyczące funkcji odzyskiwania lokacji, dlatego chcielibyśmy [czekamy na Twoją opinię](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>Q1 2019 

### <a name="update-rollup-34-february-2019"></a>Pakiet zbiorczy aktualizacji 34 (lutego 2019 r.)

[Pakiet zbiorczy 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Poprawki problem** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym)



### <a name="update-rollup-33-february-2019"></a>Pakiet zbiorczy aktualizacji 33 (lutego 2019 r.)

[Pakiet zbiorczy 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Poprawki problem** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym)
**Mapowanie sieci** | Odzyskiwania po awarii maszyny Wirtualnej platformy Azure można teraz używać wszelkie dostępności docelowej sieci po włączeniu replikacji. 
**Standardowy dysk SSD** | Teraz możesz skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu [dysków SSD w warstwie standardowa](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Bezpośrednie miejsca do magazynowania magazynu** | Możesz skonfigurować odzyskiwanie po awarii dla aplikacji działających na maszynie Wirtualnej platformy Azure aplikacje przy użyciu [bezpośrednimi miejscami do magazynowania](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) wysokiej dostępności.
**System plików BRTFS** | Obsługiwane w przypadku maszyn wirtualnych VMware, oprócz maszyn wirtualnych platformy Azure.<br/><br/> Nieobsługiwane, jeśli: Podrzędne wolumin systemu plików BTRFS zostało zmienione po włączeniu replikacji, system plików jest rozłożona na wiele dysków lub jeśli system plików BTRFS obsługuje RAID.



### <a name="update-rollup-32-january-2019"></a>Pakiet zbiorczy aktualizacji 32 (stycznia 2019 r.)

[Pakiet zbiorczy 31](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Poprawki problem** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym)
**Odzyskiwanie po awarii dla systemu Linux** | **Maszyny wirtualne platformy Azure**: Stacja robocza RedHat 6 7 dni w tygodniu; Obsługa nowych wersji jądra systemu Ubuntu, Debian i SUSE.<br/><br/> **Serwerów fizycznych i maszyn wirtualnych VMware**: RedHat Enterprise Linux 7.6; Stacja robocza RedHat 6 7 dni w tygodniu; Oracle Linux 6.10/7.6; obsługuje nowe wersje jądra, Ubuntu, Debian i SUSE.


### <a name="update-rollup-31-january-2019"></a>Pakiet zbiorczy aktualizacji 31 (stycznia 2019 r.)

[Pakiet zbiorczy 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Poprawki problem** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym)
**Odzyskiwanie po awarii dla systemu Linux** | **Maszyny wirtualne platformy Azure**: Oracle Linux 6.8 i 6,9/7.0. Obsługa UEK5 jądra.<br/><br/> **Serwerów fizycznych i maszyn wirtualnych VMware**: Oracle Linux 6.8 i 6,9/7.0. Obsługa UEK5 jądra.
**System plików BRTFS** | Obsługiwane w przypadku maszyn wirtualnych platformy Azure.
**LVM** | Dodano woluminów LVM i LVM2 obsługę.<br/><br/> Katalog/Boot na partycji dysku, jak i na woluminach LVM jest obsługiwane.
**Katalogi** | Obsługa sumowane dla tych seet katalogów jako odrębne partycje lub systemy plików, które nie są na tym samym dysku systemu: / (root), / Boot/usr, /usr/local, /var, etc.
**Windows Server 2008** | Obsługa dodane dla dysków dynamicznych.
**Tryb failover maszyny Wirtualnej VMware** | Ulepszona czas pracy awaryjnej dla maszyn wirtualnych VMware, w którym storvsc i vsbus nie sterowników rozruchowych.
**Obsługa interfejsu UEFI** | Typ rozruchu UEFI nie są obsługiwane maszyny wirtualne platformy Azure. Można teraz przeprowadzić migrację lokalnych serwerów fizycznych z interfejsem UEFI na platformie Azure z usługą Site Recovery. Usługa Site Recovery umożliwia migrowanie serwera, konwertując typ rozruchu systemu BIOS przed migracją. Usługa Site Recovery wcześniej obsługiwane tę konwersję dla maszyn wirtualnych tylko. Pomoc techniczna jest dostępna dla serwerów fizycznych z systemem Windows Server 2012 lub nowszej.
**Maszyny wirtualne platformy Azure w strefach dostępności** | Można włączyć replikację do innego regionu maszyn wirtualnych platformy Azure wdrożonych w strefach dostępności. jednostki organizacyjnej można teraz włączyć replikację dla maszyny Wirtualnej platformy Azure i ustawić cel dla trybu failover do pojedynczego wystąpienia maszyny Wirtualnej, Maszynę wirtualną w zestawie dostępności lub maszyny Wirtualnej w strefie dostępności. To ustawienie nie ma wpływu na replikację. [Odczyt](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) anonsu.


## <a name="q4-2018"></a>KWARTAŁ 4 2018 R.

### <a name="update-rollup-30-october-2018"></a>Pakiet zbiorczy aktualizacji 30 (października 2018)

[Pakiet zbiorczy 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Poprawki problem** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym)
**Obsługa regionu** | W witrynie pomocy technicznej odzyskiwania dodane Australia Środkowa 1 i Australia Środkowa 2.
**Obsługa szyfrowania dysków** | Obsługa dodane do odzyskiwania po awarii maszyn wirtualnych platformy Azure, zaszyfrowany za pomocą szyfrowania dysków Azure (ADE) za pomocą aplikacji usługi Azure AD. [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Wykluczenie dysku** | Dyski niezainicjowanej teraz są automatycznie wykluczane podczas replikacji maszyny Wirtualnej platformy Azure.
**Włączona Zapora magazynu** | Dodano dla obsługę [kont magazynów z włączoną zaporę](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Teraz można replikować maszyny wirtualne platformy Azure z dyskami niezarządzanymi na kontach magazynów z włączoną zaporę w innym regionie platformy Azure na potrzeby odzyskiwania po awarii.<br/><br/> Kont magazynu z obsługą zapory można użyć jako docelowe konta magazynu dla dysków niezarządzanych.<br/><br/> Obsługiwane tylko przy użyciu programu PowerShell.


### <a name="update-rollup-29-october-2018"></a>Pakiet zbiorczy aktualizacji 29 (października 2018)

[Pakiet zbiorczy 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Poprawki problem** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym)

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Automatyczne aktualizacje dla rozszerzenia usługi mobilności

Usługa Site Recovery dodać opcję automatycznych aktualizacji do rozszerzenia usługi mobilności. Rozszerzenie usługi mobilności jest zainstalowany na każdej maszynie Wirtualnej platformy Azure replikowane przez usługę Site Recovery. Po włączeniu replikacji, zdecyduj, czy umożliwić odzyskiwanie lokacji do zarządzania aktualizacjami do rozszerzenia. Aktualizacje nie wymagają ponownego uruchomienia maszyny Wirtualnej i nie mają wpływu na replikację. [Dowiedz się więcej](azure-to-azure-autoupdate.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Odzyskiwanie po awarii dla maszyn wirtualnych przy użyciu przyspieszoną sieć

Przyspieszona sieć Włącz wirtualizację we/wy pojedynczego elementu głównego (SR-IOV) do maszyny Wirtualnej, zwiększanie wydajności sieci. Po włączeniu replikacji dla maszyny Wirtualnej platformy Azure Usługa Site Recovery wykrywa, czy przyspieszona sieć jest włączona. Jeśli tak jest, po pracy awaryjnej usługi Site Recovery automatycznie konfiguruje przyspieszoną sieć dla docelowej repliki maszyny Wirtualnej platformy Azure, dla obu [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) i [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Kalkulator cen na potrzeby odzyskiwania po awarii maszyny Wirtualnej platformy Azure

Odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure jest naliczana opłata, koszty licencjonowania dla maszyn wirtualnych i sieci i obniżenie kosztów magazynowania. Platforma Azure udostępnia [Kalkulator cen](https://aka.ms/a2a-cost-estimator) pomagające ustalić te koszty. Usługa Site Recovery udostępnia teraz [przykładowe ceny oszacowanie](https://aka.ms/a2a-cost-estimator) cen Przykładowe wdrożenie oparte na aplikację trójwarstwową, za pomocą sześciu maszyn wirtualnych z 12 dysków standardowych dysków Twardych i 6 dysków w warstwie Premium SSD. Przykład zakładają zmian danych 10 GB na dzień dla warstwy standardowej i 20 GB dla warstwy premium. Dla danego wdrożenia możesz zmienić zmienne do szacowania kosztów. Można określić liczbę maszyn wirtualnych, liczbę i rodzaj dysków zarządzanych, a łączna ilość oczekiwanych danych Zmień częstotliwość oczekuje na maszyny wirtualne. Ponadto można stosować współczynnika kompresji, aby oszacować koszty przepustowości. [Odczyt](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) anonsu.



## <a name="q3-2018"></a>K3 2018 R. 


### <a name="update-rollup-28-august-2018"></a>Pakiet zbiorczy aktualizacji 28 (sierpnia 2018)

[Pakiet zbiorczy 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Odzyskiwanie po awarii dla systemu Linux** | **Maszyny wirtualne platformy Azure**: Dodano obsługiwanych dla RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> **Maszyny wirtualne VMware**: RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> Opartą na systemie Linux maszyn wirtualnych Użyj styl partycji (GPT tabela) identyfikator GUID partycji w starszym trybie zgodności systemu BIOS są teraz obsługiwane. Zobacz [często zadawane pytania dotyczące dysków maszyn wirtualnych IaaS platformy Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/faq-for-disks) Aby uzyskać więcej informacji. 
**Obsługa chmury za pomocą** | Obsługiwane odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w usłudze Germany cloud.
**Odzyskiwanie po awarii między subskrypcjami** | Obsługa replikowania maszyn wirtualnych platformy Azure w jednym regionie do innego regionu, w ramach innej subskrypcji w ramach tej samej dzierżawie usługi Azure Active Directory. [Dowiedz się więcej](https://aka.ms/cross-sub-blog).
**Windows Server 2008** | Obsługa migracji maszyn uruchamianie systemu Windows Server 2008 R2/2008 64-bitowe i 32-bitowych.<br/><br/> Migracja tylko (replikacja i tryb failover). Powrót po awarii nie jest obsługiwany.

### <a name="update-rollup-27-july-2018"></a>Pakiet zbiorczy aktualizacji 27 (lipca 2018 r.)

[Pakiet zbiorczy 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Odzyskiwanie po awarii dla systemu Linux** | **Maszyny wirtualne platformy Azure**: Red Hat Enterprise Linux 7.5<br/><br/> **Serwerów fizycznych i maszyn wirtualnych VMware**: Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12




## <a name="next-steps"></a>Kolejne kroki

Zapewnianie aktualności za pomocą naszej aktualizacji na [aktualizacje platformy Azure](https://azure.microsoft.com/updates/?product=site-recovery) strony.




 









