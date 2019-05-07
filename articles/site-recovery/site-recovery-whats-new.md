---
title: Co nowego w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie nowych funkcji wprowadzonych w usłudze Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: raynew
ms.openlocfilehash: e2145fbbb5fa09aa3321742ca8a786822f6f0641
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148661"
---
# <a name="whats-new-in-site-recovery"></a>Co nowego w usłudze Site Recovery

[Usługi Azure Site Recovery](site-recovery-overview.md) usługi są aktualizowane i ulepszony w sposób ciągły. Aby ułatwić Ci najnowsze informacje, w tym artykule dostarcza informacje o najnowszych wersji, nowe funkcje i nową zawartość. Ta strona jest aktualizowana w regularnych odstępach czasu.

Jeśli masz sugestie dotyczące funkcji odzyskiwania lokacji, dlatego chcielibyśmy [czekamy na Twoją opinię](https://feedback.azure.com/forums/256299-site-recovery).


## <a name="updates-march-2019"></a>Aktualizacje (marca 2019 r.)

### <a name="update-rollup-35"></a>Pakiet zbiorczy aktualizacji 35

[Pakiet zbiorczy 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Problem poprawki/ulepszeń** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym)

#### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii programu VMware lub serwera fizycznego
Nowe funkcje dodane w aktualizacji.

**Funkcja** | **Szczegóły**
--- | ---
**Dyski zarządzane** | Replikacja serwerów fizycznych i maszyn wirtualnych VMware w środowisku lokalnym jest teraz bezpośrednio do usługi managed disks na platformie Azure. Lokalne dane są wysyłane do konta magazynu pamięci podręcznej na platformie Azure i punkty odzyskiwania są tworzone w dysków zarządzanych w lokalizacji docelowej. Dzięki temu nie trzeba zarządzać wieloma kontami magazynu docelowego.
**Serwer konfiguracji** | Usługa Site Recovery obsługuje teraz serwery konfiguracji z wieloma kartami sieciowymi. Aby zarejestrować serwer konfiguracji w magazynie, należy dodać dodatkowe karty z serwerem konfiguracji maszyny Wirtualnej. Jeśli później dodasz, należy ponownie zarejestrować serwer w magazynie.


## <a name="updates-february-2019"></a>Aktualizacje (lutego 2019 r.)

### <a name="update-rollup-34"></a>Pakiet zbiorczy aktualizacji 34 

[Pakiet zbiorczy 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Problem poprawki/ulepszeń** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym).


### <a name="update-rollup-33"></a>Pakiet zbiorczy aktualizacji 33 

[Pakiet zbiorczy 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Problem poprawki/ulepszeń** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym).


#### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii w usłudze Azure VM 
Nowe funkcje dodane w aktualizacji.

**Funkcja** | **Szczegóły**
--- | ---
**Mapowanie sieci** | Odzyskiwania po awarii maszyny Wirtualnej platformy Azure można teraz używać wszelkie dostępności docelowej sieci po włączeniu replikacji. 
**Standardowy dysk SSD** | Teraz możesz skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu [dysków SSD w warstwie standardowa](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Bezpośrednie miejsca do magazynowania magazynu** | Możesz skonfigurować odzyskiwanie po awarii dla aplikacji działających na maszynie Wirtualnej platformy Azure aplikacje przy użyciu [bezpośrednimi miejscami do magazynowania](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) wysokiej dostępności.  Za pomocą bezpośrednimi miejscami do magazynowania (S2D) wraz z Site Recovery zapewnia kompleksową ochronę obciążeń maszyny Wirtualnej platformy Azure. S2D umożliwia hostowanie w klastrze gościa na platformie Azure. Jest to szczególnie przydatne w przypadku, gdy maszyna wirtualna jest hostem aplikacja o krytycznym znaczeniu, takie jak warstwa SAP ASCS, program SQL Server lub serwer plików skalowalny w poziomie.


#### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii programu VMware lub serwera fizycznego
Nowe funkcje dodane w aktualizacji.

**Funkcja** | **Szczegóły**
--- | ---
**System plików BRTFS systemu Linux** | Usługa Site Recovery obsługuje teraz replikację maszyn wirtualnych programu VMware w systemie plików BRTFS. Replikacja nie jest obsługiwana, jeśli:<br/><br/>-BTRFS wolumin systemu plików podrzędnych zostało zmienione po włączeniu replikacji.<br/><br/>— System plików jest rozłożona na wiele dysków.<br/><br/>— System plików BTRFS obsługuje RAID.
**Windows Server 2019** | Dodano dla maszyn z systemem Windows Server 2019 obsługę.


## <a name="updates-january-2019"></a>Aktualizacje (stycznia 2019 r.)

### <a name="accelerated-networking-azure-vms"></a>Wydajniejsze sieci (maszyn wirtualnych platformy Azure)

Przyspieszona sieć Włącz wirtualizację we/wy pojedynczego elementu głównego (SR-IOV) do maszyny Wirtualnej, zwiększanie wydajności sieci. Po włączeniu replikacji dla maszyny Wirtualnej platformy Azure Usługa Site Recovery wykrywa, czy przyspieszona sieć jest włączona. Jeśli tak jest, po pracy awaryjnej usługi Site Recovery automatycznie konfiguruje przyspieszoną sieć dla docelowej repliki maszyny Wirtualnej platformy Azure, dla obu [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) i [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Pakiet zbiorczy aktualizacji 32 

[Pakiet zbiorczy 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Problem poprawki/ulepszeń** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym).

#### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii w usłudze Azure VM

Nowe funkcje dodane w aktualizacji.

**Funkcja** | **Szczegóły**
--- | ---
**Pomoc techniczna Linux support** | Dodano obsługę dla stacji roboczej RedHat 6 7 dni w tygodniu i nowe wersje jądra systemu Ubuntu, Debian i SUSE.
**Bezpośrednie miejsca do magazynowania magazynu** | Usługa Site Recovery obsługuje maszyny wirtualne platformy Azure przy użyciu bezpośrednimi miejscami do magazynowania (S2D).

#### <a name="vmware-vmsphysical-servers-replication"></a>Replikacja serwerów fizycznych i maszyn wirtualnych VMware 
**Funkcja** | **Szczegóły**
--- | ---
**Pomoc techniczna Linux support** | Dodano obsługę systemu Redhat Enterprise Linux 7.6, RedHat stacji roboczej 6 7 dni w tygodniu, Oracle Linux 6.10/7.6 i nowe wersje jądra dla systemu Ubuntu, Debian i SUSE.


### <a name="update-rollup-31"></a>Pakiet zbiorczy aktualizacji 31 

[Pakiet zbiorczy 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Problem poprawki/ulepszeń** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym).

#### <a name="vmware-vmsphysical-servers-replication"></a>Replikacja serwerów fizycznych i maszyn wirtualnych VMware 
Nowe funkcje dodane w aktualizacji.

**Funkcja** | **Szczegóły**
--- | ---
**Pomoc techniczna Linux support** | Dodano obsługę Oracle Linux 6.8 i 6,9/7.0 i jądra UEK5.
**LVM** | Dodano woluminów LVM i LVM2 obsługę.<br/><br/> Katalog/Boot na partycji dysku, jak i na woluminach LVM jest teraz obsługiwane.
**Katalogi** | Dodano obsługę tych katalogów jako odrębne partycje lub systemy plików, które nie są na tym samym dysku systemowym:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Obsługa dodane dla dysków dynamicznych.
**Tryb failover** | Ulepszona czas pracy awaryjnej dla maszyn wirtualnych VMware, w którym storvsc i vsbus nie sterowników rozruchowych.
**Obsługa interfejsu UEFI** | Typ rozruchu UEFI nie są obsługiwane maszyny wirtualne platformy Azure. Można teraz przeprowadzić migrację lokalnych serwerów fizycznych z interfejsem UEFI na platformie Azure z usługą Site Recovery. Usługa Site Recovery umożliwia migrowanie serwera, konwertując typ rozruchu systemu BIOS przed migracją. Usługa Site Recovery wcześniej obsługiwane tę konwersję dla maszyn wirtualnych tylko. Pomoc techniczna jest dostępna dla serwerów fizycznych z systemem Windows Server 2012 lub nowszej.

#### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii w usłudze Azure VM
Nowe funkcje dodane w aktualizacji.

**Funkcja** | **Szczegóły**
--- | ---
**Pomoc techniczna Linux support** | Obsługiwane została dodana dla Oracle Linux 6.8 i 6,9/7.0. i jądra UEK5.
**System plików BRTFS systemu Linux** | Obsługiwane w przypadku maszyn wirtualnych platformy Azure.
**Maszyny wirtualne platformy Azure w strefach dostępności** | Można włączyć replikację do innego regionu maszyn wirtualnych platformy Azure wdrożonych w strefach dostępności. Można teraz włączyć replikację dla maszyny Wirtualnej platformy Azure i ustawić cel dla trybu failover do pojedynczego wystąpienia maszyny Wirtualnej, Maszynę wirtualną w zestawie dostępności lub maszyny Wirtualnej w strefie dostępności. To ustawienie nie ma wpływu na replikację. [Odczyt](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) anonsu.
**Włączona Zapora magazynu (portal/programu PowerShell)** | Dodano dla obsługę [kont magazynów z włączoną zaporę](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Teraz można replikować maszyny wirtualne platformy Azure z dyskami niezarządzanymi na kontach magazynów z włączoną zaporę w innym regionie platformy Azure na potrzeby odzyskiwania po awarii.<br/><br/> Kont magazynu z obsługą zapory można użyć jako docelowe konta magazynu dla dysków niezarządzanych.<br/><br/> Obsługiwane w portalu i przy użyciu programu PowerShell.

## <a name="updates-december-2018"></a>Aktualizacje (grudnia 2018 r.)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Aktualizacje automatyczne usługi mobilności (maszyny wirtualne platformy Azure)

Usługa Site Recovery dodać opcję automatycznych aktualizacji do rozszerzenia usługi mobilności. Rozszerzenie usługi mobilności jest zainstalowany na każdej maszynie Wirtualnej platformy Azure replikowane przez usługę Site Recovery. Po włączeniu replikacji, zdecyduj, czy umożliwić odzyskiwanie lokacji do zarządzania aktualizacjami do rozszerzenia.

Aktualizacje nie wymagają ponownego uruchomienia maszyny Wirtualnej i nie mają wpływu na replikację. [Dowiedz się więcej](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Kalkulator cen na potrzeby odzyskiwania po awarii maszyny Wirtualnej platformy Azure

Awaryjnego odzyskiwania z maszyn wirtualnych platformy Azure jest naliczana opłata, koszty licencjonowania dla maszyn wirtualnych i sieci i obniżenie kosztów magazynowania. Platforma Azure udostępnia [Kalkulator cen](https://aka.ms/a2a-cost-estimator) pomagające ustalić te koszty. Usługa Site Recovery udostępnia teraz [przykładowe ceny oszacowanie](https://aka.ms/a2a-cost-estimator) cen Przykładowe wdrożenie oparte na aplikację trójwarstwową, za pomocą sześciu maszyn wirtualnych z 12 dysków standardowych dysków Twardych i 6 dysków w warstwie Premium SSD.

- Przykład zakłada zmian danych 10 GB na dzień dla warstwy standardowej i 20 GB dla warstwy premium.
- Dla danego wdrożenia możesz zmienić zmienne do szacowania kosztów.
- Można określić liczbę maszyn wirtualnych, liczbę i rodzaj dysków zarządzanych, a łączna ilość oczekiwanych danych Zmień częstotliwość oczekuje na maszyny wirtualne.
- Ponadto można stosować współczynnika kompresji, aby oszacować koszty przepustowości.

[Odczyt](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) anonsu.


## <a name="updates-october-2018"></a>Aktualizacje (października 2018)

### <a name="update-rollup-30"></a>Pakiet zbiorczy aktualizacji 30 

[Pakiet zbiorczy 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Problem poprawki/ulepszeń** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym).

#### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii w usłudze Azure VM
Nowe funkcje dodane w aktualizacji.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa regionu** | W witrynie pomocy technicznej odzyskiwania dodane Australia Środkowa 1 i Australia Środkowa 2.
**Obsługa szyfrowania dysków** | Obsługa dodane do odzyskiwania po awarii maszyn wirtualnych platformy Azure, zaszyfrowany za pomocą szyfrowania dysków Azure (ADE) za pomocą aplikacji usługi Azure AD. [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Wykluczenie dysku** | Dyski niezainicjowanej teraz są automatycznie wykluczane podczas replikacji maszyny Wirtualnej platformy Azure.
**Włączona Zapora magazynu (PowerShell)** | Dodano dla obsługę [kont magazynów z włączoną zaporę](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Teraz można replikować maszyny wirtualne platformy Azure z dyskami niezarządzanymi na kontach magazynów z włączoną zaporę w innym regionie platformy Azure na potrzeby odzyskiwania po awarii.<br/><br/> Kont magazynu z obsługą zapory można użyć jako docelowe konta magazynu dla dysków niezarządzanych.<br/><br/> Obsługiwane tylko przy użyciu programu PowerShell.


### <a name="update-rollup-29"></a>Pakiet zbiorczy aktualizacji 29 

[Pakiet zbiorczy 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Problem poprawki/ulepszeń** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym).


## <a name="updates-august-2018"></a>Aktualizacje (sierpnia 2018)

### <a name="update-rollup-28"></a>Pakiet zbiorczy aktualizacji 28 

[Pakiet zbiorczy 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Problem poprawki/ulepszeń** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym).

#### <a name="azure-vms-disaster-recovery"></a>Usługa Azure odzyskiwania po awarii maszyn wirtualnych 
Nowe funkcje dodane w aktualizacji.

**Funkcja** | **Szczegóły**
--- | ---
**Pomoc techniczna Linux support** | Dodano obsługiwanych dla RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/>
**Obsługa chmury za pomocą** | Obsługiwane odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w usłudze Germany cloud.
**Odzyskiwanie po awarii między subskrypcjami** | Obsługa replikowania maszyn wirtualnych platformy Azure w jednym regionie do innego regionu, w ramach innej subskrypcji w ramach tej samej dzierżawie usługi Azure Active Directory. [Dowiedz się więcej](https://aka.ms/cross-sub-blog).

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej VMware lub serwera fizycznego 
Nowe funkcje dodane w aktualizacji.

**Funkcja** | **Szczegóły**
--- | ---
**Pomoc techniczna Linux support** | Obsługa dodane do systemu RedHat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> Opartą na systemie Linux maszyn wirtualnych Użyj styl partycji (GPT tabela) identyfikator GUID partycji w starszym trybie zgodności systemu BIOS są teraz obsługiwane. Przegląd [— często zadawane pytania dla maszyny Wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) Aby uzyskać więcej informacji. 
**Odzyskiwanie po awarii dla maszyn wirtualnych po migracji** | Obsługa dla Włączanie odzyskiwania po awarii do regionu pomocniczego dla lokalnej maszyny Wirtualnej VMware migracji na platformę Azure, bez konieczności odinstalowania mobilności usługi na maszynie Wirtualnej przed włączeniem replikacji.
**Windows Server 2008** | Obsługa migracji maszyn uruchamianie systemu Windows Server 2008 R2/2008 64-bitowe i 32-bitowych.<br/><br/> Migracja tylko (replikacja i tryb failover). Powrót po awarii nie jest obsługiwany.

## <a name="updates-july-2018"></a>Aktualizacje (lipca 2018 r.)

### <a name="update-rollup-27-july-2018"></a>Pakiet zbiorczy aktualizacji 27 (lipca 2018 r.)

[Pakiet zbiorczy 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacji agentów Site Recovery i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Problem poprawki/ulepszeń** | Liczba poprawek i udoskonaleń (zgodnie z opisem w pakiecie zbiorczym).

#### <a name="azure-vms-disaster-recovery"></a>Usługa Azure odzyskiwania po awarii maszyn wirtualnych 

Nowe funkcje dodane w aktualizacji.

**Funkcja** | **Szczegóły**
--- | ---
**Pomoc techniczna Linux support** | Obsługa dodawane w systemie Red Hat Enterprise Linux w wersji 7.5.

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej VMware lub serwera fizycznego 

Nowe funkcje dodane w aktualizacji.

**Funkcja** | **Szczegóły**
--- | ---
**Pomoc techniczna Linux support** | Obsługa dodawane w systemie Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Kolejne kroki

Zapewnianie aktualności za pomocą naszej aktualizacji na [aktualizacje platformy Azure](https://azure.microsoft.com/updates/?product=site-recovery) strony.
