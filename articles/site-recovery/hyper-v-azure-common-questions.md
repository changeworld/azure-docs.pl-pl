---
title: Typowe pytania dotyczące odzyskiwania po awarii funkcji Hyper-V za pomocą usługi Azure Site Recovery
description: W tym artykule podsumowano typowe pytania dotyczące konfigurowania odzyskiwania po awarii dla lokalnych maszyn wirtualnych z programem Hyper V na platformie Azure przy użyciu witryny Azure Site Recovery.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 7c5f55fbea67567ddf7a2afa6a61f6c76568d829
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498192"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Często zadawane pytania — odzyskiwanie po awarii z funkcji Hyper-V do platformy Azure

Ten artykuł zawiera odpowiedzi na typowe pytania, które widzimy podczas replikowania lokalnych maszyn wirtualnych z programem Hyper V na platformie Azure. 

## <a name="general"></a>Ogólne

### <a name="how-is-site-recovery-priced"></a>Jak wyceniono odzyskiwanie witryny?
Przejrzyj szczegóły [dotyczące cen usługi Azure Site Recovery.](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="how-do-i-pay-for-azure-vms"></a>Jak zapłacić za maszyny wirtualne platformy Azure?
Podczas replikacji dane są replikowane do magazynu platformy Azure i nie płacisz żadnych zmian maszyny Wirtualnej. Po uruchomieniu pracy awaryjnej na platformie Azure usługa Site Recovery automatycznie tworzy maszyny wirtualne usługi Azure IaaS. Następnie są rozliczane za zasoby obliczeniowe, które zużywają na platformie Azure.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Czy istnieje jakaś różnica w kosztach podczas replikowania do ogólnego przeznaczenia konta magazynu w wersji 2?

Zazwyczaj zobaczysz wzrost kosztów transakcji poniesionych na kontach magazynu GPv2, ponieważ usługa Azure Site Recovery jest transakcjami o dużym obciążeniu. [Przeczytaj więcej,](../storage/common/storage-account-upgrade.md#pricing-and-billing) aby oszacować zmianę.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Co jest potrzebne w funkcji Hyper-V do zorganizowania replikacji za pomocą funkcji Site Recovery?

Wymagania serwera hosta funkcji Hyper-V zależą od scenariusza wdrożenia. Zapoznaj się z wymaganiami wstępnymi funkcji Hyper-V:

* [Replicating Hyper-V VMs (without VMM) to Azure (Replikacja maszyn wirtualnych funkcji Hyper-V [bez programu VMM] do platformy Azure)](site-recovery-hyper-v-site-to-azure.md)
* [Replicating Hyper-V VMs (with VMM) to Azure (Replikacja maszyn wirtualnych funkcji Hyper-V [z programem VMM] do platformy Azure)](site-recovery-vmm-to-azure.md)
* [Replicating Hyper-V VMs to a secondary datacenter (Replikacja maszyn wirtualnych funkcji Hyper-V do dodatkowego centrum danych)](site-recovery-vmm-to-vmm.md)
* Jeśli replikujesz do dodatkowego centrum danych, przeczytaj o [obsługiwanych systemach operacyjnych gościa dla maszyn wirtualnych funkcji Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Jeśli replikujesz na platformę Azure, usługa Site Recovery obsługuje wszystkie systemy operacyjne [gościa obsługiwane przez platformę Azure.](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Czy mogę chronić maszyny wirtualne, gdy funkcja Hyper-V jest uruchomiona w klienckim systemie operacyjnym?
Nie. Maszyny wirtualne muszą znajdować się na serwerze hosta funkcji Hyper-V, który jest uruchomiony na serwerze z obsługiwanym systemem Windows. Jeśli chcesz chronić komputer kliencki, możesz zreplikować go jako komputer fizyczny na [platformie Azure](site-recovery-vmware-to-azure.md) lub [w pomocniczym centrum danych.](site-recovery-vmware-to-vmware.md)

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Czy hosty Hyper-V muszą znajdować się w chmurach programu VMM?
Jeśli chcesz replikować do pomocniczego centrum danych, maszyny wirtualne funkcji Hyper-V muszą znajdować się na serwerach hostów funkcji Hyper-V znajdujących się w chmurze programu VMM. Jeśli chcesz replikować na platformie Azure, możesz replikować maszyny wirtualne z chmurami programu VMM lub bez niego. [Dowiedz się więcej](tutorial-hyper-v-to-azure.md) o replikacji funkcji Hyper-V na platformie Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Czy można replikować maszyny wirtualne funkcji Hyper-V drugiej generacji do platformy Azure?
Tak. Funkcja Site Recovery konwertuje z generacji 2 na generację 1 podczas pracy awaryjnej. W momencie powrotu awaryjnego maszyna jest konwertowana z powrotem do generacji 2. [Dowiedz się więcej](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Czy mogę wdrożyć usługę Site Recovery z programem VMM, jeśli mam tylko jeden serwer VMM?

Tak. Maszyny wirtualne można replikować na serwerach funkcji Hyper-V w chmurze programu VMM na platformie Azure lub można replikować między chmurami programu VMM na tym samym serwerze. W przypadku replikacji lokalnej do lokalnej zaleca się, aby mieć serwer programu VMM w lokacjach podstawowych i dodatkowych. 

### <a name="what-do-i-need-in-azure"></a>Czego potrzebuję na platformie Azure?
Potrzebujesz subskrypcji platformy Azure, magazynu usług odzyskiwania, konta magazynu i sieci wirtualnej. Przechowalnia, konto magazynu i sieć muszą znajdować się w tym samym regionie.

### <a name="what-azure-storage-account-do-i-need"></a>Jakie konto usługi Azure storage jest potrzebne?
Potrzebujesz konta magazynu LRS lub GRS. Zalecamy użycie konta GRS, dzięki czemu dane będą odporne w przypadku regionalnej awarii lub sytuacji, w której nie można odzyskać regionu podstawowego. Obsługiwane jest magazynowanie w wersji premium.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Czy moje konto platformy Azure potrzebuje uprawnień do tworzenia maszyn wirtualnych?
Jeśli jesteś administratorem subskrypcji, masz uprawnienia replikacji, których potrzebujesz. Jeśli nie, potrzebujesz uprawnień do utworzenia maszyny Wirtualnej platformy Azure w grupie zasobów i sieci wirtualnej, którą określisz podczas konfigurowania usługi Site Recovery, oraz uprawnień do zapisu na wybranym koncie magazynu. [Dowiedz się więcej](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?
Nie, usługa Site Recovery nie przechwytuje replikowanych danych i nie ma żadnych informacji o tym, co jest uruchomione na maszynach wirtualnych. Dane replikacji są wymieniane między hostami funkcji Hyper-V a magazynem platformy Azure. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Site Recovery posiada certyfikat ISO 27001:2013, 27018, HIPAA, DPA i jest w trakcie ocen SOC2 i FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Czy możemy przechowywać lokalne metadane w regionie geograficznym?
Tak. Podczas tworzenia przechowalni w regionie, zapewniamy, że wszystkie metadane używane przez usługi Site Recovery pozostają w granicach geograficznych tego regionu.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Tak, obsługiwane są zarówno szyfrowanie podczas przesyłania, jak i [szyfrowanie na platformie Azure.](https://docs.microsoft.com/azure/storage/storage-service-encryption)


## <a name="deployment"></a>wdrażania

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Co mogę zrobić z replikacją funkcji Hyper-V na platformie Azure?

- **Odzyskiwanie po awarii:** Można skonfigurować pełne odzyskiwanie po awarii. W tym scenariuszu można replikować lokalnych maszyn wirtualnych z funkcjią Hyper V do magazynu platformy Azure:
    - Maszyny wirtualne można replikować na platformie Azure. Jeśli infrastruktura lokalna jest niedostępna, można przejść awaryjnie do platformy Azure.
    - Po przełączeniu w stan fail over maszyny wirtualne platformy Azure są tworzone przy użyciu replikowanych danych. Dostęp do aplikacji i obciążeń można uzyskać na maszynach wirtualnych platformy Azure.
    - Gdy lokalne centrum danych jest ponownie dostępne, można przywrócić po awarii z platformy Azure do witryny lokalnej.
- **Migracja:** Można użyć usługi Site Recovery do migracji lokalnych maszyn wirtualnych z programem Hyper V do magazynu platformy Azure. Następnie można przejść awaryjnie z lokalnego do platformy Azure. Po przemijaniu awaryjnego aplikacje i obciążenia są dostępne i uruchomione na maszynach wirtualnych platformy Azure.


### <a name="what-do-i-need-on-premises"></a>Czego potrzebuję lokalnie?

Potrzebujesz co najmniej jednej maszyny wirtualnej uruchomionej na co najmniej jednym autonomicznym lub klastrowanym hostie funkcji Hyper-V. Można również replikować maszyny wirtualne uruchomione na hostach zarządzanych przez Menedżera maszyn wirtualnych programu System Center (VMM).
- Jeśli nie korzystasz z programu VMM, podczas wdrażania usługi Site Recovery zbierasz hosty funkcji Hyper-V i klastry do witryn funkcji Hyper-V. Agentów odzyskiwania witryny (dostawca odzyskiwania witryny azure i agent usług odzyskiwania) na każdym hoście funkcji Hyper-V.
- Jeśli hosty funkcji Hyper-V znajdują się w chmurze programu VMM, można zorganizować replikację w programie VMM. Dostawca odzyskiwania lokacji jest instalowany na serwerze programu VMM, a agent usług odzyskiwania na każdym hoście funkcji Hyper-V. Mapowanie między sieciami logicznymi/maszynami wirtualnymi programu VMM a sieciami wirtualnymi platformy Azure.
- [Dowiedz się więcej](hyper-v-azure-architecture.md) o architekturze funkcji Hyper-V na platformie Azure.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Czy można replikować maszyny wirtualne znajdujące się w klastrze funkcji Hyper-V?

Tak, usługa Site Recovery obsługuje klastrowane hosty funkcji Hyper-V. Należy pamiętać, że:

- Wszystkie węzły klastra powinny być zarejestrowane w tym samym magazynie.
- Jeśli nie używasz programu VMM, wszystkie hosty funkcji Hyper-V w klastrze powinny zostać dodane do tej samej witryny funkcji Hyper-V.
- Zainstaluj agenta dostawcy odzyskiwania witryny platformy Azure i usług odzyskiwania na każdym hoście funkcji Hyper-V w klastrze i dodaj każdy host do witryny funkcji Hyper-V.
- W klastrze nie trzeba wykonywać żadnych określonych kroków.
- Po uruchomieniu narzędzia Planek wdrażania dla funkcji Hyper-V narzędzie zbiera dane profilu z uruchomionego węzła i miejsca, w którym maszyna wirtualna jest uruchomiona. Narzędzie nie może zbierać żadnych danych z węzła, który jest wyłączony, ale będzie śledzić ten węzeł. Po uruchomieniu węzła narzędzie rozpoczyna zbieranie danych profilu maszyny Wirtualnej z niego (jeśli maszyna wirtualna jest częścią listy maszyn wirtualnych profilu i jest uruchomiona w węźle).
- Jeśli maszyna wirtualna na hoście funkcji Hyper-V w magazynie odzyskiwania witryny migruje do innego hosta funkcji Hyper-V w tym samym klastrze lub do autonomicznego hosta, replikacja maszyny Wirtualnej nie ma wpływu. Host funkcji Hyper-V musi spełniać [wymagania wstępne](hyper-v-azure-support-matrix.md#on-premises-servers)i być skonfigurowany w magazynie odzyskiwania witryny. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Czy mogę chronić maszyny wirtualne, gdy funkcja Hyper-V jest uruchomiona w klienckim systemie operacyjnym?
Nie. Maszyny wirtualne muszą znajdować się na serwerze hosta funkcji Hyper-V, który jest uruchomiony na serwerze z obsługiwanym systemem Windows. Jeśli chcesz chronić komputer kliencki, możesz [zreplikować go jako komputer fizyczny](physical-azure-disaster-recovery.md) na platformie Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Czy można replikować maszyny wirtualne funkcji Hyper-V drugiej generacji do platformy Azure?
Tak. Funkcja Site Recovery konwertuje z generacji 2 na generację 1 podczas pracy awaryjnej. W momencie powrotu awaryjnego maszyna jest konwertowana z powrotem do generacji 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Czy można zautomatyzować scenariusze odzyskiwania witryny za pomocą sdk?
Tak. Przepływy pracy usługi Site Recovery można zautomatyzować przy użyciu interfejsu API REST, programu PowerShell lub zestawu SDK platformy Azure. Obecnie obsługiwane scenariusze replikowania funkcji Hyper-V na platformę Azure przy użyciu programu PowerShell:

- [Replikowanie funkcji Hyper-V bez programu VMM za pomocą programu PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replikowanie funkcji Hyper-V za pomocą programu VMM przy użyciu programu Powershell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikacja

### <a name="where-do-on-premises-vms-replicate-to"></a>Gdzie lokalne maszyny wirtualne replikują się?
Dane są replikowane do magazynu platformy Azure. Po uruchomieniu pracy awaryjnej usługa Site Recovery automatycznie tworzy maszyny wirtualne platformy Azure z konta magazynu.

### <a name="what-apps-can-i-replicate"></a>Jakie aplikacje można replikować?
Można replikować dowolną aplikację lub obciążenie z uruchomieniem maszyny Wirtualnej funkcji Hyper-V, która spełnia [wymagania dotyczące replikacji.](hyper-v-azure-support-matrix.md#replicated-vms) Usługa Site Recovery zapewnia obsługę replikacji obsługującej aplikacje, dzięki czemu aplikacje mogą zostać przejęte awaryjnie i po awarii z powrotem do stanu inteligentnego. Usługa Site Recovery integruje się z aplikacjami firmy Microsoft, takimi jak SharePoint, Exchange, Dynamics, SQL Server i Active Directory, i ściśle współpracuje z wiodącymi dostawcami, takimi jak Oracle, SAP, IBM i Red Hat. [Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="whats-the-replication-process"></a>Co to jest proces replikacji?

1. Po wyzwoleniu replikacji początkowej jest pobierana migawka maszyny Wirtualnej funkcji Funkcji Wirtualnej funkcji.
2. Wirtualne dyski twarde na maszynie wirtualnej są replikowane jeden po drugim, dopóki nie zostaną skopiowane na platformę Azure. Może to trochę potrwać, w zależności od rozmiaru maszyny Wirtualnej i przepustowości sieci. Dowiedz się, jak zwiększyć przepustowość sieci.
3. Jeśli zmiany dysku wystąpią podczas replikacji początkowej, moduł śledzenia replik repliki funkcji Hyper-V śledzi zmiany jako dzienniki replikacji funkcji Hyper-V (hrl). Te pliki dziennika znajdują się w tym samym folderze co dyski. Każdy dysk ma skojarzony plik hrl, który jest wysyłany do magazynu pomocniczego. Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny wirtualnej jest usuwana.
5. Wszelkie zmiany dysku w dzienniku są synchronizowane i scalane z dyskiem nadrzędnym.
6. Po zakończeniu replikacji początkowej zostanie uruchamiana ochrona Finalize na maszynie wirtualnej. Konfiguruje sieci i inne ustawienia po replikacji, tak aby maszyna wirtualna jest chroniona.
7. Na tym etapie można sprawdzić ustawienia maszyny Wirtualnej, aby upewnić się, że jest gotowy do pracy awaryjnej. Można uruchomić wiertło odzyskiwania po awarii (test pracy awaryjnej) dla maszyny Wirtualnej, aby sprawdzić, czy przechodzi w stan failover zgodnie z oczekiwaniami.
8. Po replikacji początkowej replikacja delta rozpoczyna się zgodnie z zasadami replikacji.
9. Zmiany są rejestrowane pliki .hrl. Z każdym dyskiem skonfigurowanym pod kątem replikacji jest skojarzony plik hrl.
10. Dziennik jest wysyłany do konta magazynu klienta. Gdy dziennik jest przesyłany na platformę Azure, zmiany na dysku podstawowym są śledzone w innym pliku dziennika w tym samym folderze.
11. Podczas replikacji początkowej i delta można monitorować maszynę wirtualną w witrynie Azure portal.

[Dowiedz się więcej](hyper-v-azure-architecture.md#replication-process) o procesie replikacji.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Czy można replikować na platformie Azure za pomocą sieci VPN lokacja lokacja?

Usługa Site Recovery replikuje dane z lokalnego magazynu do magazynu platformy Azure za pośrednictwem publicznego punktu końcowego lub przy użyciu komunikacji równorzędnej firmy Microsoft w programie ExpressRoute. Replikacja za pośrednictwem sieci VPN typu lokacja-lokacja nie jest obsługiwana.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Czy można replikować na platformie Azure za pomocą usługi ExpressRoute?

Tak, usługa ExpressRoute może służyć do replikowania maszyn wirtualnych na platformie Azure. Usługa Site Recovery replikuje dane do konta usługi Azure Storage za pomocą publicznego punktu końcowego i należy skonfigurować [komunikację równorzędną firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) dla replikacji usługi Site Recovery. Po przejściu maszyn wirtualnych w tryb fail over do sieci wirtualnej platformy Azure, można uzyskać do nich dostęp za pomocą [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Dlaczego nie mogę replikować przez VPN?

Podczas replikowania na platformie Azure ruch replikacji dociera do publicznych punktów końcowych konta usługi Azure Storage. W ten sposób można replikować tylko za pośrednictwem publicznego Internetu za pomocą usługi ExpressRoute (komunikacja równorzędna firmy Microsoft), a sieć VPN nie działa. 

### <a name="what-are-the-replicated-vm-requirements"></a>Jakie są wymagania replikowanej maszyny Wirtualnej?

W przypadku replikacji maszyna wirtualna funkcji Hyper-V musi być uruchomiona w obsługiwanym systemie operacyjnym. Ponadto maszyna wirtualna musi spełniać wymagania dotyczące maszyn wirtualnych platformy Azure. [Dowiedz się więcej](hyper-v-azure-support-matrix.md#replicated-vms) w macierzy obsługi.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak często można replikować na platformie Azure?

Maszyny wirtualne funkcji Hyper V mogą być replikowane co 30 sekund (z wyjątkiem magazynu w wersji premium), 5 minut lub 15 minut.

### <a name="can-i-extend-replication"></a>Czy mogę rozszerzyć replikację?
Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Poproś o tę funkcję na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Czy mogę wykonać replikację początkową w trybie offline?
Ta funkcja nie jest obsługiwana. Poproś o tę funkcję na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Czy mogę wykluczyć dyski?
Tak, można wykluczyć dyski z replikacji. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Czy można replikować maszyny wirtualne za pomocą dysków dynamicznych?
Dyski dynamiczne mogą być replikowane. Dysk systemu operacyjnego musi być dyskiem podstawowym.



## <a name="security"></a>Zabezpieczenia

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Jaki dostęp do hostów funkcji Hyper-V wymaga od odzyskiwania witryny

Usługa Site Recovery potrzebuje dostępu do hostów funkcji Hyper-V w celu replikacji wybranych maszyn wirtualnych. Funkcja Site Recovery instaluje następujące elementy na hostach funkcji Hyper-V:

- Jeśli nie korzystasz z programu VMM, dostawca odzyskiwania witryny azure i agent usług odzyskiwania są zainstalowane na każdym hoście.
- Jeśli korzystasz z programu VMM, agent usług odzyskiwania jest zainstalowany na każdym hoście. Dostawca działa na serwerze programu VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Co program Site Recovery jest instalowy na maszynach wirtualnych z programem Hyper-V?

Usługa Site Recovery nie instaluje niczego w przypadku maszyn wirtualnych z funkcją funkcji Hyper-V, które są włączone do replikacji.




## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii


### <a name="how-do-i-fail-over-to-azure"></a>Jak przejść awaryjnie na platformie Azure?

Planowane lub nieplanowane przejście w tryb failover można uruchomić z maszyn wirtualnych funkcji Hyper-V do platformy Azure.

- Jeśli zostanie uruchomione planowane przejście w tryb failover, źródłowe maszyny wirtualne zostaną wyłączone w celu zapewnienia, że nie będzie miała miejsca utrata danych.
- Jeśli witryna główna nie jest dostępna, można uruchomić nieplanowaną przełączenie awaryjne.
- W tryb failover można przełączyć pojedynczą maszynę lub można utworzyć plany odzyskiwania, aby zaaranżować tryb failover na wielu maszynach.
- Przewijenie awaryjne składa się z dwóch części:
    - Po zakończeniu pierwszego etapu pracy awaryjnej powinien być widoczny utworzone maszyny wirtualne repliki na platformie Azure. Jeśli jest to wymagane, do maszyny wirtualnej można przypisać publiczny adres IP.
    - Następnie zatwierdzić pracy awaryjnej, aby rozpocząć dostęp do obciążenia z repliki maszyny Wirtualnej platformy Azure.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak uzyskać dostęp do maszyn wirtualnych platformy Azure po przemijaniu awaryjnym?
Po przełączeniu w błąd można uzyskać dostęp do maszyn wirtualnych platformy Azure za pośrednictwem bezpiecznego połączenia internetowego, sieci VPN typu lokacja-lokacja lub za pośrednictwem usługi Azure ExpressRoute. Musisz przygotować wiele rzeczy, aby połączyć. [Dowiedz się więcej](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>Czy dane są odporne na dane w stanie failed?
Platforma Azure została zaprojektowana z myślą o odporności danych. Usługa Site Recovery została zaprojektowana z myślą o przeżycie awaryjnym do pomocniczego centrum danych platformy Azure zgodnie z usługą Azure SLA. W przypadku pracy awaryjnej upewnij się, że metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla magazynu.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?
[Praca awaryjna](site-recovery-failover.md) nie jest automatyczna. Zainicjować pracy awaryjnej za pomocą jednego kliknięcia w portalu lub można użyć [programu PowerShell](/powershell/module/az.recoveryservices) wyzwolić pracy awaryjnej.

### <a name="how-do-i-fail-back"></a>Jak wrócić awaryjnie?

Po ponownym uruchomieniu infrastruktury lokalnej można wrócić do komputera. Powrót po awarii występuje w trzech etapach:

1. Rozpoczęcie planowanej pracy awaryjnej z platformy Azure do lokacji lokalnej przy użyciu kilku różnych opcji:

    - Minimalizowanie przestojów: jeśli używasz tej opcji Usługa Site Recovery synchronizuje dane przed przełączeniem awaryjnym. Sprawdza, czy nie ma zmienionych bloków danych i pobiera je do lokacji lokalnej, podczas gdy maszyna wirtualna platformy Azure jest uruchomiona, minimalizując przestoje. Po ręcznym określeniu, że praca awaryjna powinna zakończyć się, maszyna wirtualna platformy Azure jest zamykana, wszelkie ostateczne zmiany delta są kopiowane i rozpoczyna się praca awaryjna.
    - Pełne pobieranie: dzięki tej opcji dane są synchronizowane podczas pracy awaryjnej. Ta opcja powoduje pobranie całego dysku. Jest szybszy, ponieważ nie są obliczane sumy kontrolne, ale jest więcej przestojów. Użyj tej opcji, jeśli używasz repliki maszyn wirtualnych platformy Azure od jakiegoś czasu lub jeśli lokalna maszyna wirtualna została usunięta.

2. Można wybrać, aby powiększyć powiększanie tej samej maszyny wirtualnej lub alternatywnej maszyny Wirtualnej. Można określić, że odzyskiwanie witryny należy utworzyć maszynę wirtualną, jeśli jeszcze nie istnieje.
3. Po zakończeniu synchronizacji początkowej należy wybrać, aby zakończyć przebienie awaryjne. Po jej zakończeniu można zalogować się do lokalnej maszyny Wirtualnej, aby sprawdzić wszystko działa zgodnie z oczekiwaniami. W witrynie Azure portal, można zobaczyć, że maszyny wirtualne platformy Azure zostały zatrzymane.
4. Zatwierdzenie pracy awaryjnej do zakończenia i rozpocząć dostęp do obciążenia z lokalnej maszyny Wirtualnej ponownie.
5. Po zakończeniu pracy obciążeń po awarii, można włączyć replikację odwrotną, tak aby lokalne maszyny wirtualne replikować na platformie Azure ponownie.

### <a name="can-i-fail-back-to-a-different-location"></a>Czy mogę wrócić do innej lokalizacji?
Tak, jeśli po awarii na platformie Azure, można powiększyć po awarii do innej lokalizacji, jeśli oryginalna nie jest dostępna. [Dowiedz się więcej](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).
