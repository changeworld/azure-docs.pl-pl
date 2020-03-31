---
title: Co nowego w usłudze Azure Site Recovery
description: Zawiera podsumowanie nowych funkcji i najnowszych aktualizacji w usłudze Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: abb1592bcacf025e9a052d7a9222f6fb3d2b72d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257435"
---
# <a name="whats-new-in-site-recovery"></a>Co nowego w usłudze Site Recovery

Usługa [Azure Site Recovery](site-recovery-overview.md) jest aktualizowana i ulepszana na bieżąco. Aby pomóc Ci być na bieżąco, w tym artykule znajdziesz informacje o najnowszych wersjach, nowych funkcjach i nowej zawartości. Ta strona jest regularnie aktualizowana.

Możesz śledzić i subskrybować powiadomienia o aktualizacjach usługi Site Recovery w kanale [aktualizacji platformy Azure.](https://azure.microsoft.com/updates/?product=site-recovery)

## <a name="supported-updates"></a>Obsługiwane aktualizacje

W przypadku składników odzyskiwania witryny obsługujemy wersje N-4, w których N jest najnowszą wersją wydaną. Podsumowanie dotyczące tych raportów można znaleźć w poniższej tabeli.

**Aktualizacja** |  **Ujednolicona konfiguracja** | **Ova serwera konfiguracji** | **Agent usług mobilności** | **Dostawca odzyskiwania witryny** | **Agent usług odzyskiwania**
--- | --- | --- | --- | --- | ---
[Zestawienie 45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0
[Zestawienie 43](https://support.microsoft.com/help/4537047/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[Zestawienie 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[Zestawienie 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[Zestawienie 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0

[Dowiedz się więcej](service-updates-how-to.md) o instalacji i obsłudze technicznej aktualizacji.

> [!NOTE]
> Pakiet zbiorczy aktualizacji 44 nie jest wyświetlany w tabeli, ponieważ nie zawiera aktualizacji dla dostawców i agentów odzyskiwania witryny.

## <a name="updates-march-2020"></a>Aktualizacje (marzec 2020 r.)

### <a name="update-rollup-45"></a>Pakiet zbiorczy aktualizacji 45

[Pakiet zbiorczy aktualizacji 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) zawiera następujące aktualizacje:

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje agentów i dostawców odzyskiwania witryny, jak opisano w zestawie zbiorczym.
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń, jak opisano w zestawie zbiorczym.

## <a name="updates-january-2020"></a>Aktualizacje (styczeń 2020 r.)

### <a name="update-rollup-44"></a>Pakiet zbiorczy aktualizacji 44

[Pakiet zbiorczy aktualizacji 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Nie było żadnych aktualizacji dla dostawców i agentów odzyskiwania witryny.
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń, jak opisano w zestawie zbiorczym.

### <a name="azure-vmware-disaster-recovery"></a>Odzyskiwanie po awarii usługi Azure VMware

Maszyny wirtualne platformy Azure obsługują teraz maszyny wirtualne włączają szyfrowanie w spoczynku za pomocą kluczy zarządzanych przez klienta. [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Pakiet zbiorczy aktualizacji 43

[Pakiet zbiorczy aktualizacji 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje agentów i dostawców odzyskiwania witryn (jak opisano w zestawie zbiorczym)
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym)


## <a name="updates-november-2019"></a>Aktualizacje (listopad 2019)

### <a name="update-rollup-42"></a>Pakiet zbiorczy aktualizacji 42

[Pakiet zbiorczy aktualizacji 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje agentów i dostawców odzyskiwania witryn (jak opisano w zestawie zbiorczym)
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym)


### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure

Nowe funkcje odzyskiwania po awarii maszyny Wirtualnej platformy Azure są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Uefi** | Usługa Site Recovery obsługuje teraz odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure z architekturą rozruchową opartą na UEFI.
**Linux** | Usługa Site Recovery obsługuje teraz maszyny wirtualne platformy Azure z systemem Linux z szyfrowaniem dysków azure (ADE).
**Druga generacja** | Wszystkie maszyny wirtualne platformy Azure generacji 2 są teraz obsługiwane w celu odzyskiwania po awarii.
**Regiony** | Teraz można włączyć odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w obszarze geograficznym Norwegii.

### <a name="vmware-to-azure-disaster-recovery"></a>Odzyskiwanie po awarii VMware–Azure

Nowe funkcje odzyskiwania po awarii VMware do platformy Azure są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Uefi** | Odzyskiwanie lokacji obsługuje teraz odzyskiwanie po awarii dla maszyn wirtualnych VMware z architekturą rozruchową opartą na UEFI.<br/><br/> Obsługiwane systemy operacyjne obejmują systemy Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 z sp4, RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Aktualizacja do obsługi aktualizacji stosu/SHA-2

W przypadku odzyskiwania po awarii maszyn wirtualnych platformy Azure do regionu pomocniczego lub lokalnych maszyn wirtualnych VMware lub serwerów fizycznych na platformie Azure, należy zwrócić uwagę na następujące kwestie:

- Od wersji 9.30.5407.1 rozszerzenia usługi mobilności (dla maszyn wirtualnych platformy Azure) i agenta usługi mobilności (dla maszyn VMware/fizycznych) niektóre systemy operacyjne maszyny muszą uruchamiać aktualizację stosu obsługi i sha-2. Szczegóły są przedstawione w poniższej tabeli.
- Zainstaluj aktualizację i SHA-2 zgodnie z połączonym KB. SHA-1 nie jest obsługiwany od września 2019 r., a jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami.
- Dowiedz się więcej o [aktualizacji SHA-2 i wymaganiach](https://aka.ms/SHA-2KB).

**System operacyjny** | **Maszyna wirtualna platformy Azure** | **VMware VM/maszyna fizyczna**
--- | --- | ---
**Windows 2008 R2 SP1** | [Aktualizacja stosu obsługi](https://support.microsoft.com/help/4490628)<br/> [SHA-2 (Sha-2)](https://support.microsoft.com/help/4474419)| [Aktualizacja stosu obsługi](https://support.microsoft.com/help/4490628)<br/> [SHA-2 (Sha-2)](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Aktualizacja stosu obsługi](https://support.microsoft.com/help/4493730)<br/> [SHA-2 (Sha-2)](https://support.microsoft.com/help/4474419)| [Aktualizacja stosu obsługi](https://support.microsoft.com/help/4493730)<br/> [SHA-2 (Sha-2)](https://support.microsoft.com/help/4474419)
**Windows 7 z dodatkiem SP1** | [Aktualizacja stosu obsługi](https://support.microsoft.com/help/4490628)<br/> [SHA-2 (Sha-2)](https://support.microsoft.com/help/4474419)| [Aktualizacja stosu obsługi](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Aktualizacje (październik 2019)

### <a name="update-rollup-41"></a>Pakiet zbiorczy aktualizacji 41

[Pakiet zbiorczy aktualizacji 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje agentów i dostawców odzyskiwania witryn (jak opisano w zestawie zbiorczym)
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym)



### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure

Nowe funkcje odzyskiwania po awarii maszyny Wirtualnej platformy Azure są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Testowanie ustawień trybu failover** | Podczas konfigurowania testowego trybu failover można teraz skonfigurować ustawienia testowej maszyny wirtualnej trybu failover i sieci, w tym adres IP, sieć sieciowej sieciowej, wewnętrzny bilans obciążenia i publiczny adres IP dla każdej karty sieciowej komputera. Te ustawienia są opcjonalne i nie zmieniają bieżącego zachowania. Jeśli te ustawienia nie zostaną skonfigurowane, możesz wybrać witrynę wirtualną platformy Azure w czasie testowania pracy awaryjnej. [Dowiedz się więcej](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Plany odzyskiwania** | Plany odzyskiwania są teraz ograniczone do 100 maszyn wirtualnych, aby zapewnić niezawodność pracy awaryjnej.

### <a name="vmware-to-azure-disaster-recovery"></a>Odzyskiwanie po awarii VMware–Azure

Nowe funkcje odzyskiwania po awarii VMware do platformy Azure są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Plany odzyskiwania** | Plany odzyskiwania są teraz ograniczone do 100 maszyn wirtualnych, aby zapewnić niezawodność pracy awaryjnej.


## <a name="updates-september-2019"></a>Aktualizacje (wrzesień 2019)

### <a name="update-rollup-40"></a>Pakiet zbiorczy aktualizacji 40

[Pakiet zbiorczy aktualizacji 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje agentów i dostawców odzyskiwania witryn (jak opisano w zestawie zbiorczym)
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym)




### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure

Nowe funkcje odzyskiwania po awarii maszyny Wirtualnej platformy Azure są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Oczyszczanie po 3- po awarii** | Po przepełnieniu po awarii na pomocniczej platformie Azure, a następnie w przypadku niepowodzenia z powrotem do regionu podstawowego, usługa Site Recovery automatycznie czyści maszyny w regionie pomocniczym. Nie ma potrzeby ręcznego usuwania maszyn wirtualnych i kart sieciowych.
**Test pracy awaryjnej zachowuje adres IP** | Teraz można zachować adres IP źródłowej maszyny Wirtualnej podczas ćwiczenia odzyskiwania po awarii i wybrać statyczny adres IP dla testowego trybu failover.

### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii serwera VMware/fizycznego

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
Nowe alerty serwera procesów | Dodaliśmy nowe alerty serwera procesów. [Dowiedz się więcej](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Odzyskiwanie po awarii funkcji Hyper-V

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
Konto magazynu | Usługa Site Recovery obsługuje teraz korzystanie z kont magazynu z włączoną zaporą dla odzyskiwania po awarii funkcji Hyper-V na platformie Azure.  Można wybrać konta magazynu z włączoną zaporą jako konto docelowe lub magazyn pamięci podręcznej. Jeśli używasz konta z włączoną zaporą, upewnij się, że włączono tę opcję zezwalania na zaufane usługi firmy Microsoft.<br/><br/> Jest to obsługiwane dla maszyn wirtualnych z programem VMM lub bez programu System Center.


## <a name="updates-august-2019"></a>Aktualizacje (sierpień 2019)

### <a name="update-rollup-39"></a>Pakiet zbiorczy aktualizacji 39

[Pakiet zbiorczy aktualizacji 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje agentów i dostawców odzyskiwania witryn (jak opisano w zestawie zbiorczym)
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym)


### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure

Nowe funkcje odzyskiwania po awarii maszyny Wirtualnej platformy Azure są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Szyfrowanie bez usługi Azure AD** | Szyfrowanie bez aplikacji usługi Azure AD jest teraz obsługiwane dla replikacji maszyny wirtualnej platformy Azure na dyskach zarządzanych z systemem Windows.
**Zasoby sieciowe do pracy awaryjnej** | W przypadku awarii do innego regionu, można teraz dołączyć ustawienia zasobów sieciowych (NSG, równoważenie obciążenia, publiczny adres IP) do maszyny Wirtualnej.

## <a name="updates-july-2019"></a>Aktualizacje (lipiec 2019)

### <a name="update-rollup-38"></a>Pakiet zbiorczy aktualizacji 38

[Pakiet zbiorczy aktualizacji 38](https://support.microsoft.com/help/4513507/) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje agentów i dostawców odzyskiwania witryn (jak opisano w zestawie zbiorczym)
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym)


### <a name="general"></a>Ogólne

Odzyskiwanie lokacji obsługuje teraz korzystanie z kont magazynu ogólnego przeznaczenia w wersji 2 dla magazynu pamięci podręcznej lub magazynu docelowego. Wcześniej obsługiwano tylko v1.

### <a name="vmware-to-azure-disaster-recovery"></a>Odzyskiwanie po awarii VMware–Azure

Teraz można replikować dyski o pojemności do 8 TB podczas replikowania na maszynę wirtualną platformy Azure z dyskami zarządzanymi.


## <a name="updates-june-2019"></a>Aktualizacje (czerwiec 2019)

### <a name="update-rollup-37"></a>Pakiet zbiorczy aktualizacji 37

[Pakiet zbiorczy aktualizacji 37](https://support.microsoft.com/help/4508614/) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje agentów i dostawców odzyskiwania witryn (jak opisano w zestawie zbiorczym)
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym)


### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii serwera VMware/fizycznego

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Partycje GPT** | Począwszy od pakietu zbiorczego aktualizacji 37 (usługa mobilności w wersji 9.25.5241.1) w UEFI jest obsługiwanych do pięciu partycji GPT. Przed tą aktualizacją obsługiwane były cztery.



## <a name="updates-may-2019"></a>Aktualizacje (maj 2019)

### <a name="update-rollup-36"></a>Pakiet zbiorczy aktualizacji 36

[Pakiet zbiorczy aktualizacji 36](https://support.microsoft.com/help/4503156) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja agentów i dostawców odzyskiwania witryny (jak opisano w zestawie zbiorczym)
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym)

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Replikowanie dodanych dysków** | Włącz replikację dysków danych dodanych do maszyny Wirtualnej platformy Azure, która jest już włączona do odzyskiwania po awarii. [Dowiedz się więcej](azure-to-azure-enable-replication-added-disk.md).
**Automatyczne aktualizacje** | Podczas konfigurowania aktualizacji automatycznych dla rozszerzenia usługi mobilności, który działa na maszynach wirtualnych platformy Azure włączone do odzyskiwania po awarii, można teraz wybrać istniejące konto automatyzacji do użycia, zamiast przy użyciu konta domyślnego utworzonego przez usługę Site Recovery. [Dowiedz się więcej](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii serwera VMware/fizycznego

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Monitorowanie serwera przetwarzania** | W celu odzyskiwania po awarii lokalnych maszyn wirtualnych VMware i serwerów fizycznych należy monitorować i rozwiązywać problemy z serwerem procesów dzięki ulepszonym raportowaniom kondycji serwera i alertom. [Dowiedz się więcej](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Aktualizacje (marzec 2019)

### <a name="update-rollup-35"></a>Pakiet zbiorczy aktualizacji 35

[Pakiet zbiorczy aktualizacji 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja agentów i dostawców odzyskiwania witryny (jak opisano w zestawie zbiorczym)
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym)

### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii serwera VMware/fizycznego

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Dyski zarządzane** | Replikacja lokalnych maszyn wirtualnych VMware i serwerów fizycznych jest teraz bezpośrednio do dysków zarządzanych na platformie Azure. Dane lokalne są wysyłane do konta magazynu pamięci podręcznej na platformie Azure, a punkty odzyskiwania są tworzone na dyskach zarządzanych w lokalizacji docelowej. Dzięki temu nie trzeba zarządzać wieloma docelowymi kontami magazynu.
**Serwer konfiguracji** | Usługa Site Recovery obsługuje teraz serwery konfiguracyjne z wieloma kartami sieciowymi. Dodaj dodatkowe karty do maszyny Wirtualnej serwera konfiguracji przed zarejestrowaniem serwera konfiguracji w przechowalni. Jeśli dodasz później, musisz ponownie zarejestrować serwer w przechowalni.


## <a name="updates-february-2019"></a>Aktualizacje (luty 2019)

### <a name="update-rollup-34"></a>Pakiet zbiorczy aktualizacji 34

[Pakiet zbiorczy aktualizacji 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja agentów i dostawców odzyskiwania witryny (zgodnie z opisem w zestawie zbiorczym).
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym).


### <a name="update-rollup-33"></a>Pakiet zbiorczy aktualizacji 33

[Pakiet zbiorczy aktualizacji 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja agentów i dostawców odzyskiwania witryny (zgodnie z opisem w zestawie zbiorczym).
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym).


### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Mapowanie sieci** | W przypadku odzyskiwania po awarii maszyny Wirtualnej platformy Azure można teraz używać dowolnej dostępnej sieci docelowej po włączeniu replikacji.
**Dysk SSD w warstwie Standardowa** | Teraz można skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu [standardowych dysków SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Bezpośrednie miejsca do magazynowania** | Odzyskiwanie po awarii można skonfigurować dla aplikacji uruchomionych w aplikacjach maszyny Wirtualnej platformy Azure przy użyciu [bezpośrednie miejsca do magazynowania](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) dla wysokiej dostępności.  Korzystanie z funkcji Bezpośrednie miejsca do magazynowania (S2D) wraz z usługą Site Recovery zapewnia kompleksową ochronę obciążeń maszyn wirtualnych platformy Azure. S2D umożliwia hostowanie klastra gościa na platformie Azure. Jest to szczególnie przydatne, gdy maszyna wirtualna obsługuje krytyczną aplikację, taką jak warstwa SAP ASCS, program SQL Server lub skalowany serwer plików w poziomie.


### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii serwera VMware/fizycznego

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**System plików Linux BRTFS** | Usługa Site Recovery obsługuje teraz replikację maszyn wirtualnych VMware za pomocą systemu plików BRTFS. Replikacja nie jest obsługiwana, jeśli:<br/><br/>- Pod wolumin systemu plików BTRFS jest zmieniany po włączeniu replikacji.<br/><br/>- System plików jest rozłożony na wiele dysków.<br/><br/>- System plików BTRFS obsługuje RAID.
**Windows Server 2019** | Dodano obsługę maszyn z systemem Windows Server 2019.


## <a name="updates-january-2019"></a>Aktualizacje (styczeń 2019)


### <a name="accelerated-networking-azure-vms"></a>Przyspieszona sieć (maszyny wirtualne platformy Azure)

Przyspieszona sieć umożliwia wirtualizację we/wy pojedynczego katalogu głównego (SR-IOV) na maszynie wirtualnej, poprawiając wydajność sieci. Po włączeniu replikacji dla maszyny Wirtualnej platformy Azure usługa Site Recovery wykrywa, czy przyspieszona sieć jest włączona. Jeśli tak jest, po przejściu awaryjnym Site Recovery automatycznie konfiguruje przyspieszone sieci na docelowej repliki maszyny Wirtualnej Platformy Azure, dla [systemów Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) i [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Pakiet zbiorczy aktualizacji 32

[Pakiet zbiorczy aktualizacji 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja agentów i dostawców odzyskiwania witryny (zgodnie z opisem w zestawie zbiorczym).
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym).

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługę RedHat Workstation 6/7 oraz nowych wersji jądra dla Ubuntu, Debiana i SUSE.
**Bezpośrednie miejsca do magazynowania** | Usługa Site Recovery obsługuje maszyny wirtualne platformy Azure przy użyciu funkcji Bezpośrednie miejsca do magazynowania (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Maszyny wirtualne VMware/odzyskiwanie po awarii serwerów fizycznych

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługę Redhat Enterprise Linux 7.6, RedHat Workstation 6/7, Oracle Linux 6.10 i Oracle Linux 7.6 oraz nowych wersji jądra dla Ubuntu, Debiana i SUSE.


### <a name="update-rollup-31"></a>Pakiet zbiorczy aktualizacji 31

[Pakiet zbiorczy aktualizacji 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja agentów i dostawców odzyskiwania witryny (zgodnie z opisem w zestawie zbiorczym).
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym).

### <a name="vmware-vmsphysical-servers-replication"></a>Replikacja maszyn wirtualnych VMware/serwerów fizycznych

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** |  Dodano obsługę systemów Oracle Linux 6.8, Oracle Linux 6.9 i Oracle Linux 7.0 z jądrem zgodnym z red hat oraz wersji 5 unbreakable Enterprise Kernel (UEK).
**Lvm** | Dodano obsługę woluminów LVM i LVM2.<br/><br/> Katalog /boot na partycji dysku i na woluminach LVM jest teraz obsługiwany.
**Katalogi** | Dodano obsługę tych katalogów skonfigurowanych jako oddzielne partycje lub systemy plików, które nie są na tym samym dysku systemowym:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Dodano obsługę dysków dynamicznych.
**Tryb failover** | Poprawiono czas pracy awaryjnej dla maszyn wirtualnych VMware, w których storvsc i vsbus nie są sterownikami rozruchowymi.
**Wsparcie UEFI** | Maszyny wirtualne platformy Azure nie obsługują typu rozruchowego UEFI. Teraz można migrować lokalne serwery fizyczne z UEFI na platformę Azure za pomocą usługi Site Recovery. Usługa Site Recovery migruje serwer, konwertując typ rozruchu na system BIOS przed migracją. Odzyskiwanie witryny wcześniej obsługiwało tę konwersję tylko dla maszyn wirtualnych. Pomoc techniczna jest dostępna dla serwerów fizycznych z systemem Windows Server 2012 lub nowszym.

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługę systemów Oracle Linux 6.8, Oracle Linux 6.9 i Oracle Linux 7.0 z jądrem zgodnym z red hat oraz wersji 5 unbreakable Enterprise Kernel (UEK).
**System plików Linux BRTFS** | Obsługiwane dla maszyn wirtualnych platformy Azure.
**Maszyny wirtualne platformy Azure w strefach dostępności** | Replikację można włączyć do innego regionu dla maszyn wirtualnych platformy Azure wdrożonych w strefach dostępności. Teraz można włączyć replikację dla maszyny Wirtualnej platformy Azure i ustawić obiekt docelowy dla trybu failover na pojedyncze wystąpienie maszyny Wirtualnej, maszynę wirtualną w zestawie dostępności lub maszynę wirtualną w strefie dostępności. To ustawienie nie ma wpływu na replikację. [Przeczytaj](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) ogłoszenie.
**Magazyn z obsługą zapory (portal/program PowerShell)** | Dodano obsługę [kont magazynu z obsługą zapory](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Maszyny wirtualne platformy Azure można replikować za pomocą dysków niezarządzanych na kontach magazynu z obsługą zapory do innego regionu platformy Azure w celu odzyskiwania po awarii.<br/><br/> Kont magazynu z włączoną zaporą można używać jako kont magazynu docelowego dla dysków niezarządzanych.<br/><br/> Obsługiwane w portalu i przy użyciu programu PowerShell.

## <a name="updates-december-2018"></a>Aktualizacje (grudzień 2018 r.)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Automatyczne aktualizacje usługi mobilności (maszyny wirtualne platformy Azure)

Usługa Site Recovery dodała opcję automatycznych aktualizacji rozszerzenia usługi Mobilności. Rozszerzenie usługi mobilności jest instalowane na każdej maszynie wirtualnej platformy Azure replikowanej przez usługę Site Recovery. Po włączeniu replikacji należy wybrać, czy usługa Site Recovery ma umożliwiać zarządzanie aktualizacjami rozszerzenia.

Aktualizacje nie wymagają ponownego uruchomienia maszyny Wirtualnej i nie wpływają na replikację. [Dowiedz się więcej](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Kalkulator cen dla odzyskiwania po awarii maszyny Wirtualnej platformy Azure

Odzyskiwanie po awarii maszyn wirtualnych platformy Azure wiąże się z kosztami licencjonowania maszyn wirtualnych oraz kosztami sieci i magazynu. Platforma Azure udostępnia [kalkulator cen,](https://aka.ms/a2a-cost-estimator) który pomoże Ci ustalić te koszty. Usługa Site Recovery zawiera teraz [przykładową wycenę](https://aka.ms/a2a-cost-estimator) cen, która wycenia przykładowe wdrożenie oparte na aplikacji trójwarstwowej przy użyciu sześciu maszyn wirtualnych z 12 dyskami standardowego dysku twardego i 6 dyskami SSD premium.

- Przykład zakłada zmianę danych 10 GB dziennie dla standardu i 20 GB dla wersji premium.
- Dla danego wdrożenia można zmienić zmienne, aby oszacować koszty.
- Można określić liczbę maszyn wirtualnych, liczbę i typ dysków zarządzanych oraz oczekiwaną szybkość zmiany danych oczekiwaną na maszynach wirtualnych.
- Ponadto można zastosować współczynnik kompresji do szacowania kosztów przepustowości.

[Przeczytaj](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) ogłoszenie.


## <a name="updates-october-2018"></a>Aktualizacje (październik 2018 r.)

### <a name="update-rollup-30"></a>Pakiet zbiorczy aktualizacji 30

[Pakiet zbiorczy aktualizacji 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja agentów i dostawców odzyskiwania witryny (zgodnie z opisem w zestawie zbiorczym).
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym).

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure
Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Wsparcie regionu** | Dodano obsługę odzyskiwania witryny dla australii środkowej 1 i Australii Środkowej 2.
**Obsługa szyfrowania dysku** | Dodano obsługę odzyskiwania po awarii maszyn wirtualnych platformy Azure zaszyfrowanych za pomocą szyfrowania dysków azure (ADE) za pomocą aplikacji usługi Azure AD. [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Wykluczenie dysku** | Niezainicjowane dyski są teraz automatycznie wykluczane podczas replikacji maszyny Wirtualnej platformy Azure.
**Magazyn z obsługą zapory (PowerShell)** | Dodano obsługę [kont magazynu z obsługą zapory](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Maszyny wirtualne platformy Azure można replikować za pomocą dysków niezarządzanych na kontach magazynu z obsługą zapory do innego regionu platformy Azure w celu odzyskiwania po awarii.<br/><br/> Kont magazynu z włączoną zaporą można używać jako kont magazynu docelowego dla dysków niezarządzanych.<br/><br/> Obsługiwane tylko przy użyciu programu PowerShell.


### <a name="update-rollup-29"></a>Pakiet zbiorczy aktualizacji 29

[Pakiet zbiorczy aktualizacji 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja agentów i dostawców odzyskiwania witryny (zgodnie z opisem w zestawie zbiorczym).
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym).


## <a name="updates-august-2018"></a>Aktualizacje (sierpień 2018)

### <a name="update-rollup-28"></a>Pakiet zbiorczy aktualizacji 28

[Pakiet zbiorczy aktualizacji 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja agentów i dostawców odzyskiwania witryny (zgodnie z opisem w zestawie zbiorczym).
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym).

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure
Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługiwane dla RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/>
**Obsługa chmury** | Obsługiwane odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w chmurze Niemiec.
**Odzyskiwanie po awarii między subskrypcjami** | Obsługa replikowania maszyn wirtualnych platformy Azure w jednym regionie do innego regionu w innej subskrypcji w ramach tej samej dzierżawy usługi Azure Active Directory. [Dowiedz się więcej](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Odzyskiwanie po awarii VMware VMware VM/server physical server
Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługę RedHat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> Maszyny wirtualne oparte na systemie Linux, które używają stylu partycji tabeli partycji GUID (GPT) w starszym trybie zgodności systemu BIOS, są teraz obsługiwane. Zapoznaj się z [często zadawanymi pytaniami dotyczącymi maszyny wirtualnej platformy Azure,](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) aby uzyskać więcej informacji.
**Odzyskiwanie po awarii dla maszyn wirtualnych po migracji** | Obsługa włączania odzyskiwania po awarii w regionie pomocniczym dla lokalnej maszyny Wirtualnej VMware zmigrowanego na platformę Azure bez konieczności odinstalowywania usługi mobilności na maszynie Wirtualnej przed włączeniem replikacji.
**Windows Server 2008** | Obsługa migracji komputerów z systemem Windows Server 2008 R2/2008 64-bitowych i 32-bitowych.<br/><br/> Tylko migracja (replikacja i przemijenie awaryjne). Powrót po awarii nie jest obsługiwany.

## <a name="updates-july-2018"></a>Aktualizacje (lipiec 2018 r.)

### <a name="update-rollup-27-july-2018"></a>Pakiet zbiorczy aktualizacji 27 (lipiec 2018 r.)

[Pakiet zbiorczy aktualizacji 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizacja** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja agentów i dostawców odzyskiwania witryny (zgodnie z opisem w zestawie zbiorczym).
**Poprawki/ulepszenia problemu** | Szereg poprawek i ulepszeń (jak opisano w zestawie zbiorczym).

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługę Red Hat Enterprise Linux 7.5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Odzyskiwanie po awarii VMware VMware VM/server physical server

Funkcje dodane w tym miesiącu są podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługę Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Następne kroki

Bądź na bieżąco z naszymi aktualizacjami na stronie [Aktualizacje platformy Azure.](https://azure.microsoft.com/updates/?product=site-recovery)
