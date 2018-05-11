---
title: Macierz obsługi replikacji funkcji Hyper-V do platformy Azure | Dokumentacja firmy Microsoft
description: Podsumowanie obsługiwanych składników oraz wymagania dotyczące replikacji funkcji Hyper-V do platformy Azure z usługą Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2018
ms.author: raynew
ms.openlocfilehash: f8149d2af5542fb311ff83160d674e4d525289dc
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Macierz obsługi replikacji funkcji Hyper-V w systemie Azure


Ten artykuł zawiera podsumowanie obsługiwanych składników i ustawienia odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V lokalnej na platformie Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Obsługiwane scenariusze

**Scenariusz** | **Szczegóły**
--- | ---
Funkcja Hyper-V z programu Virtual Machine Manager | Można wykonać odzyskiwania po awarii do platformy Azure dla maszyn wirtualnych na hostach funkcji Hyper-V, które są zarządzane w sieci szkieletowej programu System Center Virtual Machine Manager.<br/><br/> Można wdrożyć ten scenariusz, w portalu Azure lub za pomocą programu PowerShell.<br/><br/> Gdy hosty funkcji Hyper-V są zarządzane przez program Virtual Machine Manager, również można wykonać odzyskiwania po awarii do lokacji dodatkowej lokalnymi. Aby dowiedzieć się więcej na temat tego scenariusza, przeczytaj [w tym samouczku](tutorial-vmm-to-vmm.md).
Funkcja Hyper-V bez programu Virtual Machine Manager | Można wykonać odzyskiwania po awarii do platformy Azure dla maszyn wirtualnych na hostach funkcji Hyper-V, które nie są zarządzane przez program Virtual Machine Manager.<br/><br/> Można wdrożyć ten scenariusz, w portalu Azure lub za pomocą programu PowerShell.


## <a name="on-premises-servers"></a>Serwery lokalne

**Serwer** | **Wymagania** | **Szczegóły**
--- | --- | ---
Funkcji Hyper-V (uruchomiony bez programu Virtual Machine Manager) | Windows Server 2016, Windows Server 2012 R2 z najnowszymi aktualizacjami | Łączenie hostów z uruchomionym systemem Windows Server 2016 i 2012 R2 nie jest obsługiwana po skonfigurowaniu lokacji funkcji Hyper-V w usłudze Site Recovery.<br/><br/> Dla maszyn wirtualnych znajdujących się na hoście z systemem Windows Server 2016 odzyskiwania do alternatywnej lokalizacji nie jest obsługiwana.
Funkcji Hyper-V (uruchomiony z programu Virtual Machine Manager) | Program Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Jeśli używany jest program Virtual Machine Manager, hosty z systemem Windows Server 2016 powinny być zarządzane w 2016 programu Virtual Machine Manager.<br/><br/> Chmurze programu Virtual Machine Manager, która łączy hostów Hyper-V z systemem Windows Server 2016 lub 2012 R2 nie jest obecnie obsługiwany.<br/><br/> Środowiska, które obejmują uaktualnienie istniejącego serwera programu Virtual Machine Manager 2012 R2, aby 2016 nie są obsługiwane.


## <a name="replicated-vms"></a>Replikowane maszyny wirtualne


Poniższa tabela zawiera podsumowanie obsługi maszyny Wirtualnej. Usługa Site Recovery obsługuje dowolne obciążenia uruchomione na obsługiwanym systemie operacyjnym.

 **Składnik** | **Szczegóły**
--- | ---
Konfiguracja maszyny Wirtualnej | Maszyny wirtualne, które są replikowane do platformy Azure musi spełniać [wymagania dotyczące usługi Azure](#failed-over-azure-vm-requirements).
System operacyjny gościa | Wszelkie system operacyjny gościa [obsługiwany przez platformę Azure](https://technet.microsoft.com/library/cc794868.aspx).<br/><br/> Windows Server 2016 Nano Server nie jest obsługiwana.




## <a name="hyper-v-network-configuration"></a>Konfiguracja sieci funkcji Hyper-V

**Składnik** | **Funkcja Hyper-V z programu Virtual Machine Manager** | **Funkcja Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
Sieci hosta: tworzenie zespołu kart interfejsu Sieciowego | Yes
Host sieci: sieć VLAN | Yes
Sieci hosta: IPv4 | Yes
Sieci hosta: IPv6 | Nie
Sieć maszyny Wirtualnej gościa: tworzenie zespołu kart interfejsu Sieciowego | Nie
Sieć maszyny Wirtualnej gościa: IPv4 | Yes
Sieć maszyny Wirtualnej gościa: IPv6 | Nie
Sieć maszyny Wirtualnej gościa: statyczny adres IP (z systemem Windows) | Yes
Sieć maszyny Wirtualnej gościa: statyczny adres IP (Linux) | Nie
Sieć maszyny Wirtualnej gościa: wieloma kartami Sieciowymi | Yes



## <a name="azure-vm-network-configuration-after-failover"></a>Konfiguracji sieci maszyny Wirtualnej platformy Azure (po trybu failover)

**Składnik** | **Funkcja Hyper-V z programu Virtual Machine Manager** | **Funkcja Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
Usługa ExpressRoute systemu Azure | Yes | Yes
Wewnętrzny moduł równoważenia obciążenia | Yes | Yes
ELB | Yes | Yes
Azure Traffic Manager | Yes | Yes
Multi-NIC | Yes | Yes
Zastrzeżony adres IP | Yes | Yes
Protokół IPv4 | Yes | Yes
Zachowaj źródłowy adres IP | Yes | Yes
Punkty końcowe usługi sieci wirtualnej platformy Azure<br/> (bez zapory usługi Azure Storage) | Yes | Yes
Accelerated Networking | Nie | Nie


## <a name="hyper-v-host-storage"></a>Magazyn hosta funkcji Hyper-V

**Storage** | **Funkcja Hyper-V z programu Virtual Machine Manager** | **Funkcja Hyper-V bez programu Virtual Machine Manager**
--- | --- | --- | ---
NFS | Nie dotyczy | Nie dotyczy
SMB 3.0 | Yes | Yes
SAN (ISCSI) | Yes | Yes
Wiele ścieżek (MPIO). Przetestowane z:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> Program EMC PowerPath DSM dla CLARiiON | Yes | Yes

## <a name="hyper-v-vm-guest-storage"></a>Magazynu gościa maszyny Wirtualnej funkcji Hyper-V

**Storage** | **Funkcja Hyper-V z programu Virtual Machine Manager** | **Funkcja Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
VMDK | Nie dotyczy | Nie dotyczy
VHD/VHDX | Yes | Yes
Generacji 2 maszyn wirtualnych | Yes | Yes
EFI/UEFI| Yes | Yes
Udostępniony dysk klastra | Nie | Nie
Zaszyfrowanego dysku | Nie | Nie
NFS | Nie dotyczy | Nie dotyczy
SMB 3.0 | Nie | Nie
RDM | Nie dotyczy | Nie dotyczy
Dysk > 1 TB | Tak, maksymalnie 4,095 GB | Tak, maksymalnie 4,095 GB
Dysku: sektor logiczny i fizyczny 4K | Nieobsługiwana: gł 1/Gen 2 | Nieobsługiwana: gł 1/Gen 2
Dysku: logicznych 4K i 512 bajtów sektora fizycznego | Yes |  Yes
Wolumin dysku rozłożone > 1 TB<br/><br/> Zarządzanie woluminami logiczne (LVM) | Yes | Yes
Funkcja miejsca do magazynowania | Yes | Yes
Dodaj lub usuń gorących dysku | Nie | Nie
Wykluczanie dysku | Yes | Yes
Wiele ścieżek (MPIO) | Yes | Yes

## <a name="azure-storage"></a>Azure Storage

**Składnik** | **Funkcja Hyper-V z programu Virtual Machine Manager** | **Funkcja Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
Magazyn lokalnie nadmiarowy | Yes | Yes
Magazyn geograficznie nadmiarowy | Yes | Yes
Dostęp do odczytu magazynu geograficznie nadmiarowego | Yes | Yes
Magazynu chłodnego | Nie | Nie
Magazynu gorącego| Nie | Nie
Obiekty BLOB typu Block | Nie | Nie
Szyfrowanie magazynowanych (SSE)| Yes | Yes
Premium Storage | Yes | Yes
Import/Eksport usługi | Nie | Nie
Usługa Azure Storage zapory dla sieci wirtualnych skonfigurowanych na docelowe konto magazynu pamięci podręcznej/magazynów (używane do przechowywania danych replikacji) | Nie | Nie


## <a name="azure-compute-features"></a>Funkcje obliczeń platformy Azure

**Funkcja** | **Funkcja Hyper-V z programu Virtual Machine Manager** | **Funkcja Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
Zestawy dostępności | Yes | Yes
HUB | Yes | Yes  
Dyski zarządzane | Tak, w tryb pracy awaryjnej.<br/><br/> Powrót po awarii dysków zarządzanych nie jest obsługiwana. | Tak, w tryb pracy awaryjnej.<br/><br/> Powrót po awarii dysków zarządzanych nie jest obsługiwana.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny Wirtualnej platformy Azure

Lokalnych maszyn wirtualnych, które są replikowane do platformy Azure musi spełniać wymagania maszyny Wirtualnej Azure podsumowaniem w poniższej tabeli.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Usługa Site Recovery obsługuje wszystkich systemów operacyjnych, które są [obsługiwany przez platformę Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Architektura systemu operacyjnego gościa | 64-bitowa | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Rozmiar dysku systemu operacyjnego | Do 2048 GB dla maszyny wirtualne generacji 1.<br/><br/> Dla maszyn wirtualnych generacji 2 do 300 GB.  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Liczba dysków systemu operacyjnego | 1 | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Liczba dysków danych | 16 lub mniej  | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Rozmiar wirtualnego dysku twardego dysku danych | Do 4,095 GB | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Karty sieciowe | Obsługiwana jest konfiguracja z wieloma kartami sieciowymi |
Udostępniony wirtualny dysk twardy | Nieobsługiwane | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
FC dysku | Nieobsługiwane | Sprawdzanie wymagań wstępnych kończy się niepowodzeniem, jeśli jest nieobsługiwany.
Format dysku twardego | WIRTUALNEGO DYSKU TWARDEGO <br/><br/> VHDX | Usługa Site Recovery automatycznie konwertuje VHDX do wirtualnego dysku twardego, gdy awaryjnie na platformie Azure. Przechodzenia wstecz do lokalnych maszyn wirtualnych w dalszym ciągu korzystać z formatu VHDX.
BitLocker | Nieobsługiwane | Przed włączeniem replikacji dla maszyny Wirtualnej, należy wyłączyć funkcję BitLocker.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków. Ograniczone do liter, cyfr i łączników. Nazwa maszyny wirtualnej musi zaczynać się i kończyć literą lub cyfrą. | Zaktualizuj tę wartość właściwości maszyny Wirtualnej w usłudze Site Recovery.
Typu maszyny Wirtualnej | Generacja 1<br/><br/> Generacja 2 — systemu Windows | Maszyny wirtualne generacji 2 z typem dysku systemu operacyjnego, Basic (zawierającej co najmniej dwa woluminy danych w formacie VHDX) i mniejsza niż 300 GB miejsca na dysku są obsługiwane.<br></br>Maszyn wirtualnych systemu Linux generacji 2 nie są obsługiwane. [Dowiedz się więcej](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Akcje magazyn usług odzyskiwania

**Akcja** |  **Funkcja Hyper-V z programu Virtual Machine Manager** | **Funkcja Hyper-V bez programu Virtual Machine Manager**
--- | --- | ---
Przenieś magazyn między grupami zasobów<br/><br/> W obrębie subskrypcji oraz | Nie | Nie
Przenieść magazyn, sieć, maszyn wirtualnych platformy Azure w grupach zasobów<br/><br/> W obrębie subskrypcji oraz | Nie | Nie


## <a name="provider-and-agent"></a>Dostawca i agent

Aby upewnić się, że wdrożenie jest zgodny z ustawieniami w tym artykule, upewnij się, że jest uruchomiona najnowsza wersja dostawcy i wersje agentów.

**Nazwa** | **Opis** | **Szczegóły**
--- | --- | --- | --- | ---
Dostawca usługi Azure Site Recovery | Współrzędne komunikacji między serwerami lokalnymi i Azure <br/><br/> Funkcja Hyper-V z programu Virtual Machine Manager: zainstalowanych na serwerach programu Virtual Machine Manager<br/><br/> Funkcja Hyper-V bez programu Virtual Machine Manager: zainstalowanych na hostach funkcji Hyper-V| Najnowsza wersja: 5.1.2700.1 (dostępne w portalu Azure)<br/><br/> [Najnowsze funkcje i poprawki](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agent usług odzyskiwania Microsoft Azure | Koordynuje replikację między maszynami wirtualnymi funkcji Hyper-V i platformą Azure<br/><br/> Zainstalowane na serwerach funkcji Hyper-V lokalnej (z lub bez programu Virtual Machine Manager) | Najnowsza wersja agenta dostępna z portalu






## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [przygotowanie Azure](tutorial-prepare-azure.md) odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V lokalnymi.
