---
title: Macierz obsługi do odzyskiwania po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie obsługiwanych składników i wymagania dotyczące odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V na platformę Azure za pomocą usługi Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: bce9f3b3a574d27e2fb47fb9b2da9470c43fd2eb
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399429"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Macierz obsługi dla odzyskiwania po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V do platformy Azure


Ten artykuł zawiera podsumowanie obsługiwanych składników i ustawienia dla odzyskiwania po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V na platformie Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Obsługiwane scenariusze

**Scenariusz** | **Szczegóły**
--- | ---
Funkcji Hyper-V za pomocą programu Virtual Machine Manager | Można wykonać odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych uruchomionych na hostach funkcji Hyper-V, które są zarządzane w sieci szkieletowej programu System Center Virtual Machine Manager.<br/><br/> Można wdrożyć ten scenariusz, w witrynie Azure portal lub przy użyciu programu PowerShell.<br/><br/> Jeśli hosty funkcji Hyper-V są zarządzane przez Menedżera maszyny wirtualnej, można również wykonać odzyskiwanie po awarii do lokacji dodatkowej w środowisku lokalnym. Aby dowiedzieć się więcej na temat tego scenariusza, przeczytaj [w tym samouczku](hyper-v-vmm-disaster-recovery.md).
Funkcji Hyper-V bez programu Virtual Machine Manager | Dla maszyn wirtualnych uruchomionych na hostach funkcji Hyper-V, które nie są zarządzane przez Menedżera maszyny wirtualnej można wykonać odzyskiwania po awarii na platformie Azure.<br/><br/> Można wdrożyć ten scenariusz, w witrynie Azure portal lub przy użyciu programu PowerShell.


## <a name="on-premises-servers"></a>Serwery lokalne

**Serwer** | **Wymagania** | **Szczegóły**
--- | --- | ---
Funkcji Hyper-V (z systemem bez programu Virtual Machine Manager) |  2019 serwera systemu Windows, Windows Server 2016 (takie jak instalacja server core), Windows Server 2012 R2 z najnowszymi aktualizacjami | Jeśli skonfigurowano już program Windows Server 2012 R2 z / lub programu SCVMM 2012 R2 przy użyciu usługi Azure Site Recovery i planujesz uaktualnić system operacyjny, postępuj zgodnie ze wskazówkami [dokumentacji.](upgrade-2012R2-to-2016.md) 
Funkcji Hyper-V (uruchomionego za pomocą programu Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Jeśli jest używany program Virtual Machine Manager, hosty systemu Windows Server 2019 powinny być zarządzane w 2019 programu Virtual Machine Manager. Podobnie hosty systemu Windows Server 2016 powinny być zarządzane w programie Virtual Machine Manager 2016.<br/><br/>


## <a name="replicated-vms"></a>Replikowane maszyny wirtualne


Poniższa tabela zawiera podsumowanie obsługi maszyny Wirtualnej. Usługa Site Recovery obsługuje dowolne obciążenia uruchomione na obsługiwanym systemie operacyjnym.

 **Składnik** | **Szczegóły**
--- | ---
Konfiguracja maszyny Wirtualnej | Maszyny wirtualne, które są replikowane do platformy Azure muszą spełniać [wymagania dotyczące usługi Azure](#azure-vm-requirements).
System operacyjny gościa | Dowolnego systemu operacyjnego gościa [obsługiwane na platformie Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)...<br/><br/> Windows Server 2016 Nano Server nie jest obsługiwane.


## <a name="vmdisk-management"></a>Zarządzanie maszyna wirtualna/dysk

**Akcja** | **Szczegóły**
--- | ---
Zmiana rozmiaru dysku na replikowanej maszynie Wirtualnej funkcji Hyper-V | Nieobsługiwane. Wyłącz replikację, wprowadzić zmiany, a następnie ponownie włączyć replikację dla maszyny Wirtualnej.
Dodaj dysk na replikowanej maszynie Wirtualnej funkcji Hyper-V | Nieobsługiwane. Wyłącz replikację, wprowadzić zmiany, a następnie ponownie włączyć replikację dla maszyny Wirtualnej.

## <a name="hyper-v-network-configuration"></a>Konfiguracja sieci funkcji Hyper-V

**Składnik** | **Funkcji Hyper-V za pomocą programu Virtual Machine Manager** | **Funkcji Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
Sieć hosta: Tworzenie zespołu kart interfejsu Sieciowego | Tak | Tak
Sieć hosta: VLAN | Tak | Yes
Sieć hosta: IPv4 | Yes | Tak
Sieć hosta: IPv6 | Nie | Nie
Sieć maszyny Wirtualnej gościa: Tworzenie zespołu kart interfejsu Sieciowego | Nie | Nie
Sieć maszyny Wirtualnej gościa: IPv4 | Tak | Tak
Sieć maszyny Wirtualnej gościa: IPv6 | Nie | Tak
Sieć maszyny Wirtualnej gościa: Statyczny adres IP (Windows) | Tak | Tak
Sieć maszyny Wirtualnej gościa: Statyczny adres IP (Linux) | Nie | Nie
Sieć maszyny Wirtualnej gościa: Multi-NIC | Yes | Yes



## <a name="azure-vm-network-configuration-after-failover"></a>Konfiguracji sieci maszyny Wirtualnej platformy Azure (po włączeniu trybu failover)

**Składnik** | **Funkcji Hyper-V za pomocą programu Virtual Machine Manager** | **Funkcji Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
Usługa ExpressRoute systemu Azure | Tak | Tak
ILB | Tak | Tak
ELB | Yes | Tak
Azure Traffic Manager | Tak | Tak
Multi-NIC | Tak | Yes
Zastrzeżony adres IP | Tak | Yes
IPv4 | Tak | Yes
Zachowaj źródłowy adres IP | Tak | Yes
Punkty końcowe usługi sieci wirtualnej platformy Azure<br/> (bez zapór usługi Azure Storage) | Yes | Tak
Accelerated Networking | Nie | Nie


## <a name="hyper-v-host-storage"></a>Magazyn hosta funkcji Hyper-V

**Storage** | **Funkcji Hyper-V za pomocą programu Virtual Machine Manager** | **Funkcji Hyper-V bez programu Virtual Machine Manager**
--- | --- | --- 
NFS | Nie dotyczy | Nie dotyczy
SMB 3.0 | Yes | Yes
SAN (ISCSI) | Tak | Yes
Wiele ścieżek (MPIO). Przetestowane za pomocą:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> Program EMC PowerPath DSM dla CLARiiON | Yes | Yes

## <a name="hyper-v-vm-guest-storage"></a>Magazyn gościa maszyny Wirtualnej funkcji Hyper-V

**Storage** | **Funkcji Hyper-V za pomocą programu Virtual Machine Manager** | **Funkcji Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
VMDK | Nie dotyczy | Nie dotyczy
VHD/VHDX | Yes | Tak
2. generacji maszyn wirtualnych | Tak | Yes
EFI/UEFI| Tak | Tak
Udostępniony dysk klastra | Nie | Nie
Zaszyfrowanego dysku | Nie | Nie
NFS | Nie dotyczy | Nie dotyczy
SMB 3.0 | Nie | Nie
RDM | Nie dotyczy | Nie dotyczy
Dysk > 1 TB | Tak, maksymalnie 4095 GB. | Tak, maksymalnie 4095 GB.
Dysk: Logiczne i fizyczne z sektorami 4K | Nieobsługiwane: Velikost haldy 1/Gen 2 | Nieobsługiwane: Velikost haldy 1/Gen 2
Dysk: 4K logiczne i fizyczne sektora 512 bajtów | Tak |  Tak
Zarządzanie woluminami logicznych (LVM). LVM jest obsługiwana na tylko dyski z danymi. Platforma Azure udostępnia tylko jeden dysk systemu operacyjnego. | Tak | Tak
Wolumin o użycie dysku rozłożonego > 1 TB | Tak | Tak
Miejsca do magazynowania | Yes | Tak
Dodaj lub usuń gorąco dysku | Nie | Nie
Wykluczanie dysku | Tak | Tak
Wiele ścieżek (MPIO) | Tak | Yes

## <a name="azure-storage"></a>Azure Storage

**Składnik** | **Funkcji Hyper-V za pomocą programu Virtual Machine Manager** | **Funkcji Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
Magazyn lokalnie nadmiarowy | Tak | Tak
Magazyn geograficznie nadmiarowy | Tak | Yes
Magazyn geograficznie nadmiarowy z dostępem do odczytu | Tak | Tak
Chłodny Magazyn | Nie | Nie
Magazynu gorącego| Nie | Nie
Obiekty BLOB typu Block | Nie | Nie
Szyfrowanie danych magazynowanych (SSE)| Tak | Tak
Premium Storage | Yes | Tak
Usługa import/export | Nie | Nie
Usługa Azure Storage zapory dla sieci wirtualnych skonfigurowanych na docelowe konto magazynu dla magazynu/pamięci podręcznej (używane do przechowywania danych replikacji) | Nie | Nie


## <a name="azure-compute-features"></a>Funkcje obliczeniowe platformy Azure

**Funkcja** | **Funkcji Hyper-V za pomocą programu Virtual Machine Manager** | **Funkcji Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
Zestawy dostępności | Tak | Tak
HUB | Tak | Tak  
Dyski zarządzane | Tak, pracy w trybie Failover.<br/><br/> Powrót po awarii z dyskami zarządzanymi nie jest obsługiwane. | Tak, pracy w trybie Failover.<br/><br/> Powrót po awarii z dyskami zarządzanymi nie jest obsługiwane.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny Wirtualnej platformy Azure

Lokalnych maszyn wirtualnych, które są replikowane do platformy Azure muszą spełniać wymagania maszyny Wirtualnej platformy Azure podsumowane w poniższej tabeli.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Usługa Site Recovery obsługuje wszystkie systemy operacyjne, które są [obsługiwanym przez platformę Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Architektura systemu operacyjnego gościa | 64-bitowy | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Rozmiar dysku systemu operacyjnego | Do 2048 GB dla maszyn wirtualnych generacji 1.<br/><br/> Maksymalnie 300 GB dla maszyn wirtualnych generacji 2.  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Liczba dysków systemu operacyjnego | 1 | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Liczba dysków danych | 16 lub mniej  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Rozmiar wirtualnego dysku twardego dysku danych | Do 4095 GB. | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Karty sieciowe | Obsługiwana jest konfiguracja z wieloma kartami sieciowymi |
Udostępniony wirtualny dysk twardy | Nieobsługiwane | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Dysk FC | Nieobsługiwane | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Format dysku twardego | VHD <br/><br/> VHDX | Usługa Site Recovery automatycznie konwertuje pliki VHDX na dysku VHD podczas przejścia w tryb failover na platformie Azure. Po powrocie po awarii do środowiska lokalnego, maszyny wirtualne nadal używają formatu VHDX.
BitLocker | Nieobsługiwane | Przed włączeniem replikacji dla maszyny Wirtualnej, należy wyłączyć funkcję BitLocker.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków. Ograniczone do liter, cyfr i łączników. Nazwa maszyny wirtualnej musi zaczynać się i kończyć literą lub cyfrą. | Zaktualizuj wartość we właściwościach maszyny Wirtualnej w usłudze Site Recovery.
Typ maszyny Wirtualnej | 1. generacji<br/><br/> Generacja 2 — Windows | Maszyny wirtualne generacji 2, korzystając z typu dysku systemu operacyjnego basic (który zawiera jeden lub dwa woluminy danych w formacie VHDX) i mniej niż 300 GB miejsca na dysku są obsługiwane.<br></br>Maszyny wirtualne systemu Linux generacji 2 nie są obsługiwane. [Dowiedz się więcej](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Akcje magazyn usług odzyskiwania

**Akcja** |  **Funkcji Hyper-V za pomocą programu Virtual Machine Manager** | **Funkcji Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
Przenoszenie magazynu między grupami zasobów<br/><br/> W ramach i między subskrypcjami | Nie | Nie
Przenieś magazyn, sieci, maszyn wirtualnych platformy Azure między grupami zasobów<br/><br/> W ramach i między subskrypcjami | Nie | Nie

> [!NOTE]
> W przypadku replikacji funkcji Hyper-VMs (zarządzane z lub bez SCVMM) ze środowiska lokalnego do platformy Azure, można replikować tylko jedną dzierżawą usługi AD z jednego środowiska określone — w lokacji funkcji Hyper-V lub programu SCVMM, jeśli ma to zastosowanie.


## <a name="provider-and-agent"></a>Dostawca i agent

Aby upewnić się, że wdrożenie jest zgodny z ustawieniami w tym artykule, upewnij się, że używasz najnowszego dostawcę i wersje agentów.

**Nazwa** | **Opis** | **Szczegóły**
--- | --- | --- 
Dostawca usługi Azure Site Recovery | Służy do koordynowania komunikacji między serwerami lokalnymi a platformą Azure <br/><br/> Funkcji Hyper-V za pomocą programu Virtual Machine Manager: Zainstalowane na serwerach programu Virtual Machine Manager<br/><br/> Funkcji Hyper-V bez programu Virtual Machine Manager: Zainstalowany na hostach funkcji Hyper-V| Najnowsza wersja: 5.1.2700.1 (dostępne w witrynie Azure portal)<br/><br/> [Najnowsze funkcje i poprawki](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agent usług odzyskiwania Microsoft Azure | Koordynuje replikację między maszynami wirtualnymi funkcji Hyper-V i platformą Azure<br/><br/> Zainstalowane na serwerach funkcji Hyper-V w środowisku lokalnym (z lub bez programu Virtual Machine Manager) | Najnowszą wersję agenta, dostępne w portalu






## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [przygotowywanie platformy Azure](tutorial-prepare-azure.md) do odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w środowisku lokalnym.
