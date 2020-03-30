---
title: Obsługa odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure za pomocą usługi Azure Site Recovery
description: Podsumowuje obsługiwane składniki i wymagania dotyczące odzyskiwania po awarii funkcji Hyper-VM na platformie Azure za pomocą usługi Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 1/27/2020
ms.author: raynew
ms.openlocfilehash: d4409fe61bfe1f0a9fe74171f5b1ec471b9a6a26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258059"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Macierz obsługi odzyskiwania po awarii lokalnych maszyn wirtualnych z programem Hyper V na platformie Azure


W tym artykule podsumowano obsługiwane składniki i ustawienia odzyskiwania po awarii lokalnych maszyn wirtualnych z programem Hyper V na platformie Azure przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md)



## <a name="supported-scenarios"></a>Obsługiwane scenariusze

**Scenariusz** | **Szczegóły**
--- | ---
Funkcja Hyper-V z Menedżerem maszyn wirtualnych <br> <br>| Odzyskiwanie po awarii można wykonać na platformie Azure dla maszyn wirtualnych uruchomionych na hostach funkcji Hyper-V zarządzanych w sieci szkieletowej Menedżera maszyn wirtualnych w centrum systemu.<br/><br/> Można wdrożyć ten scenariusz w witrynie Azure portal lub przy użyciu programu PowerShell.<br/><br/> Gdy hosty funkcji Hyper-V są zarządzane przez Menedżera maszyn wirtualnych, można również wykonać odzyskiwanie po awarii w dodatkowej lokacji lokalnej. Aby dowiedzieć się więcej o tym scenariuszu, przeczytaj [ten samouczek](hyper-v-vmm-disaster-recovery.md).
Funkcja Hyper-V bez menedżera maszyn wirtualnych | Odzyskiwanie po awarii można wykonać na platformie Azure dla maszyn wirtualnych uruchomionych na hostach funkcji Hyper-V, które nie są zarządzane przez Menedżera maszyn wirtualnych.<br/><br/> Można wdrożyć ten scenariusz w witrynie Azure portal lub przy użyciu programu PowerShell.

## <a name="on-premises-servers"></a>Serwery lokalne

**Serwer** | **Wymagania** | **Szczegóły**
--- | --- | ---
Funkcja Hyper-V (działająca bez menedżera maszyn wirtualnych) |  Windows Server 2019, Windows Server 2016 (w tym instalacja rdzenia serwera), Windows Server 2012 R2 z najnowszymi aktualizacjami | Jeśli system Windows Server 2012 R2 został już skonfigurowany z systemem Windows Server 2012 R2 lub SCVMM 2012 R2 z usługą Azure Site Recovery i planuje uaktualnić system operacyjny, postępuj zgodnie z [dokumentacją.](upgrade-2012R2-to-2016.md) 
Funkcja Hyper-V (uruchomiona z Menedżerem maszyn wirtualnych) | Menedżer maszyn wirtualnych 2019, menedżer maszyn wirtualnych 2016, menedżer maszyn wirtualnych 2012 R2 | Jeśli używany jest Menedżer maszyn wirtualnych, hosty systemu Windows Server 2019 powinny być zarządzane w Menedżerze maszyn wirtualnych 2019. Podobnie hosty systemu Windows Server 2016 powinny być zarządzane w Menedżerze maszyn wirtualnych 2016.<br/><br/> Uwaga: Powrót po awarii do lokalizacji alternatywnej nie jest obsługiwany dla hostów systemu Windows Server 2019.


## <a name="replicated-vms"></a>Replikowane maszyny wirtualne


W poniższej tabeli podsumowano obsługę maszyn wirtualnych. Odzyskiwanie lokacji obsługuje wszystkie obciążenia uruchomione w obsługiwanym systemie operacyjnym.

 **Składnik** | **Szczegóły**
--- | ---
Konfiguracja maszyny Wirtualnej | Maszyny wirtualne replikowane na platformie Azure muszą spełniać [wymagania platformy Azure.](#azure-vm-requirements)
System operacyjny gościa | Każdy system operacyjny [gościa obsługiwany dla platformy Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)..<br/><br/> System Windows Server 2016 Nano Server nie jest obsługiwany.


## <a name="vmdisk-management"></a>Zarządzanie maszyną wirtualną/dyskiem

**Akcja** | **Szczegóły**
--- | ---
Ponowne rozmiary dysku na zreplikowanej maszynie wirtualnej z programem Hyper-V | Bez pomocy technicznej. Wyłącz replikację, wykonuj zmiany, a następnie ponownie włącz replikację maszyny Wirtualnej.
Dodawanie dysku na zreplikowanej maszynie wirtualnej funkcji Hyper-V | Bez pomocy technicznej. Wyłącz replikację, wykonuj zmiany, a następnie ponownie włącz replikację maszyny Wirtualnej.

## <a name="hyper-v-network-configuration"></a>Konfiguracja sieci funkcji Hyper-V

**Składnik** | **Funkcja Hyper-V z Menedżerem maszyn wirtualnych** | **Funkcja Hyper-V bez menedżera maszyn wirtualnych**
--- | --- | ---
Sieć hostów: tworzenie zespołu kart sieciowych | Tak | Tak
Sieć hostów: VLAN | Tak | Tak
Sieć hostów: IPv4 | Tak | Tak
Sieć hostów: IPv6 | Nie | Nie
Sieć maszyn wirtualnych gości: tworzenie zespołu kart sieciowych | Nie | Nie
Sieć maszyn wirtualnych gości: IPv4 | Tak | Tak
Sieć maszyn wirtualnych gości: IPv6 | Nie | Tak
Sieć maszyn wirtualnych gości: statyczny adres IP (Windows) | Tak | Tak
Sieć maszyn wirtualnych gości: Statyczny adres IP (Linux) | Nie | Nie
Sieć maszyn wirtualnych gości: Wiele kart sieciowych | Tak | Tak



## <a name="azure-vm-network-configuration-after-failover"></a>Konfiguracja sieci maszyn wirtualnych platformy Azure (po przemijaniu awaryjnym)

**Składnik** | **Funkcja Hyper-V z Menedżerem maszyn wirtualnych** | **Funkcja Hyper-V bez menedżera maszyn wirtualnych**
--- | --- | ---
Azure ExpressRoute | Tak | Tak
Ilb | Tak | Tak
Elb | Tak | Tak
Azure Traffic Manager | Tak | Tak
Wiele kart sieciowych | Tak | Tak
Zarezerwowany adres IP | Tak | Tak
Protokół IPv4 | Tak | Tak
Zachowaj źródłowy adres IP | Tak | Tak
Punkty końcowe usługi sieci wirtualnej platformy Azure<br/> (bez zapór usługi Azure Storage) | Tak | Tak
Accelerated Networking | Nie | Nie


## <a name="hyper-v-host-storage"></a>Pamięć masowa hosta funkcji Hyper-V

**Magazyn** | **Funkcja Hyper-V z Menedżerem maszyn wirtualnych** | **Funkcja Hyper-V bez menedżera maszyn wirtualnych**
--- | --- | --- 
NFS | Nie dotyczy | Nie dotyczy
SMB 3.0 | Tak | Tak
SAN (ISCSI) | Tak | Tak
Wielowąskowych (MPIO). Testowany z:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM dla CLARiiON | Tak | Tak

## <a name="hyper-v-vm-guest-storage"></a>Magazyn gościa maszyny Wirtualnej funkcji Hyper-V

**Magazyn** | **Funkcja Hyper-V z Menedżerem maszyn wirtualnych** | **Funkcja Hyper-V bez menedżera maszyn wirtualnych**
--- | --- | ---
Vmdk | Nie dotyczy | Nie dotyczy
VHD/VHDX | Tak | Tak
Maszyna wirtualna generacji 2 | Tak | Tak
EFI/UEFI<br></br>Zmigrowana maszyna wirtualna na platformie Azure zostanie automatycznie przekonwertowana na maszynę wirtualną rozruchową systemu BIOS. Maszyna wirtualna powinna być uruchomiona tylko w systemie Windows Server 2012 i nowszych. Dysk systemu operacyjnego powinien mieć maksymalnie pięć partycji lub mniej, a rozmiar dysku systemu operacyjnego powinien być mniejszy niż 300 GB.| Tak | Tak
Udostępniony dysk klastrowy | Nie | Nie
Zaszyfrowany dysk | Nie | Nie
NFS | Nie dotyczy | Nie dotyczy
SMB 3.0 | Nie | Nie
Rdm | Nie dotyczy | Nie dotyczy
Dysk >1 TB | Tak, do 4095 GB | Tak, do 4095 GB
Dysk: sektor logiczny i fizyczny 4K | Nie obsługiwane: Gen 1/Gen 2 | Nie obsługiwane: Gen 1/Gen 2
Dysk: sektor fizyczny logiczny 4K i 512 bajtów | Tak |  Tak
Logiczne zarządzanie woluminami (LVM). LVM jest obsługiwany tylko na dyskach z danymi. Platforma Azure udostępnia tylko jeden dysk systemu operacyjnego. | Tak | Tak
Wolumin z rozłożonym dyskiem >1 TB | Tak | Tak
Miejsca do magazynowania | Nie | Nie
Gorący dodawanie/usuwanie dysku | Nie | Nie
Wykluczanie dysku | Tak | Tak
Wielobiegowa (MPIO) | Tak | Tak

## <a name="azure-storage"></a>Azure Storage

**Składnik** | **Funkcja Hyper-V z Menedżerem maszyn wirtualnych** | **Funkcja Hyper-V bez menedżera maszyn wirtualnych**
--- | --- | ---
Magazyn lokalnie nadmiarowy | Tak | Tak
Magazyn geograficznie nadmiarowy | Tak | Tak
Magazyn geograficznie nadmiarowy dostępny do odczytu | Tak | Tak
Chłodne przechowywanie | Nie | Nie
Przechowywanie na gorąco| Nie | Nie
Blokowe obiekty blob | Nie | Nie
Szyfrowanie w spoczynku (SSE)| Tak | Tak
Szyfrowanie w spoczynku (CMK) <br></br> (Tylko w przypadku pracy awaryjnej na dyskach zarządzanych)| Tak (za pośrednictwem modułu PowerShell Az 3.3.0) | Tak (za pośrednictwem modułu PowerShell Az 3.3.0)
Premium Storage | Tak | Tak
Usługa importu/eksportu | Nie | Nie
Konta usługi Azure Storage z włączoną zaporą | Tak. Dla magazynu docelowego i pamięci podręcznej. | Tak. Dla magazynu docelowego i pamięci podręcznej.
Modyfikowanie konta magazynu | Nie. Docelowe konto usługi Azure Storage nie można zmodyfikować po włączeniu replikacji. Aby zmodyfikować, należy wyłączyć, a następnie ponownie włączyć odzyskiwanie po awarii. | Nie


## <a name="azure-compute-features"></a>Funkcje obliczeniowe platformy Azure

**Funkcja** | **Funkcja Hyper-V z Menedżerem maszyn wirtualnych** | **Funkcja Hyper-V bez menedżera maszyn wirtualnych**
--- | --- | ---
Zestawy dostępności | Tak | Tak
Koncentratora | Tak | Tak  
Dyski zarządzane | Tak, w przypadku pracy awaryjnej.<br/><br/> Powrót po awarii dysków zarządzanych nie jest obsługiwany. | Tak, w przypadku pracy awaryjnej.<br/><br/> Powrót po awarii dysków zarządzanych nie jest obsługiwany.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Lokalne maszyny wirtualne replikowane na platformie Azure muszą spełniać wymagania maszyny wirtualnej platformy Azure podsumowane w tej tabeli.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Usługa Site Recovery obsługuje wszystkie systemy operacyjne [obsługiwane przez platformę Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Architektura systemu operacyjnego gościa | 32-bitowe (Windows Server 2008)/64-bitowe | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Rozmiar dysku systemu operacyjnego | Do 2048 GB dla maszyn wirtualnych generacji 1.<br/><br/> Do 300 GB dla maszyn wirtualnych generacji 2.  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków systemu operacyjnego | 1 | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków danych | 16 lub mniej  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Rozmiar wirtualnego dysku twardego dysku danych | Do 4095 GB | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Karty sieciowe | Obsługiwana jest konfiguracja z wieloma kartami sieciowymi |
Udostępniony wirtualny dysk twardy | Nieobsługiwane | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Dysk FC | Nieobsługiwane | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Format dysku twardego | VHD <br/><br/> Vhdx | Usługa Site Recovery automatycznie konwertuje vhdx na dysk VHD po przełączeniu w stan fail over na platformę Azure. Po powrocie do lokalnego, maszyny wirtualne nadal używać formatu VHDX.
BitLocker | Nieobsługiwane | Funkcja BitLocker musi być wyłączona przed włączeniem replikacji dla maszyny Wirtualnej.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków. Ograniczone do liter, cyfr i łączników. Nazwa maszyny wirtualnej musi zaczynać się i kończyć literą lub cyfrą. | Zaktualizuj wartość we właściwościach maszyny Wirtualnej w odzyskiwaniu witryny.
Typ maszyny wirtualnej | Pierwsza generacja<br/><br/> Generacja 2 - Windows | Obsługiwane są maszyny wirtualne generacji 2 z podstawowym typem dysku systemu operacyjnego (który zawiera jeden lub dwa woluminy danych sformatowane jako VHDX) i mniej niż 300 GB miejsca na dysku.<br></br>Maszyny wirtualne generacji systemu Linux 2 nie są obsługiwane. [Dowiedz się więcej](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Akcje magazynu usług odzyskiwania

**Akcja** |  **Hyper-V z programem VMM** | **Hyper-V bez programu VMM**
--- | --- | ---
Przenoszenie przechowalni między grupami zasobów<br/><br/> W ramach subskrypcji i między subskrypcjami | Nie | Nie
Przenoszenie pamięci masowej, sieci, maszyn wirtualnych platformy Azure między grupami zasobów<br/><br/> W ramach subskrypcji i między subskrypcjami | Nie | Nie

> [!NOTE]
> Podczas replikowania maszyn wirtualnych z sieci Hyper-V z lokalnego na platformę Azure, można replikować tylko do jednej dzierżawy usługi AD z jednego określonego środowiska — lokacji Hyper-V lub funkcji Hyper-V z programem VMM, stosownie do przypadku.


## <a name="provider-and-agent"></a>Dostawca i agent

Aby upewnić się, że wdrożenie jest zgodne z ustawieniami w tym artykule, upewnij się, że są uruchomione najnowsze wersje dostawcy i agenta.

**Nazwa** | **Opis** | **Szczegóły**
--- | --- | --- 
Dostawca usługi Azure Site Recovery | Koordynuje komunikację między serwerami lokalnymi a platformą Azure <br/><br/> Funkcja Hyper-V z menedżerem maszyn wirtualnych: zainstalowana na serwerach Menedżera maszyn wirtualnych<br/><br/> Funkcja Hyper-V bez menedżera maszyn wirtualnych: zainstalowana na hostach funkcji Hyper-V| Najnowsza wersja: 5.1.2700.1 (dostępna w witrynie Azure portal)<br/><br/> [Najnowsze funkcje i poprawki](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agent usługi Microsoft Azure Recovery Services | Współrzędne replikacji między maszynami wirtualnymi funkcji Hyper V a platformą Azure<br/><br/> Zainstalowany na lokalnych serwerach Hyper-V (z menedżerem maszyn wirtualnych lub bez niego) | Najnowszy agent dostępny w portalu






## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [przygotować platformę Azure](tutorial-prepare-azure.md) do odzyskiwania po awarii lokalnych maszyn wirtualnych z programem Hyper V.
