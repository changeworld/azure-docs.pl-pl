---
title: Typowe pytania — funkcji Hyper-V odzyskiwania po awarii platformy Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera podsumowanie często zadawane pytania na temat konfigurowania odzyskiwania po awarii dla lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure przy użyciu witryny usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 05/30/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 9979cb97ec578a59ba8263f2eb1fe53d41db862f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399459"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Często zadawane pytania — funkcji Hyper-V odzyskiwania po awarii platformy Azure

Ten artykuł zawiera odpowiedzi na typowe pytania, które widać podczas replikowania lokalnych maszyn wirtualnych z funkcją Hyper-V do platformy Azure. 

## <a name="general"></a>Ogólne

### <a name="how-is-site-recovery-priced"></a>Jak jest rozliczana Usługa Site Recovery?
Przegląd [cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) szczegółowe informacje.

### <a name="how-do-i-pay-for-azure-vms"></a>Jak zapłacić za maszyny wirtualne platformy Azure?
Podczas replikacji dane są replikowane do usługi Azure storage, a nie płacić za wszelkie zmiany maszyny Wirtualnej. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne IaaS platformy Azure. Po tym są naliczane za zasoby obliczeniowe, które zostaną zużyte na platformie Azure.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Co należy w funkcji Hyper-V do organizowania replikacji z usługą Site Recovery?

Wymagania serwera hosta funkcji Hyper-V zależą od scenariusza wdrożenia. Zapoznaj się z wymaganiami wstępnymi funkcji Hyper-V:

* [Replikowanie maszyn wirtualnych z funkcją Hyper-V (bez programu VMM) do platformy Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replikowanie maszyn wirtualnych z funkcją Hyper-V (z programem VMM) do platformy Azure](site-recovery-vmm-to-azure.md)
* [Replikowanie maszyn wirtualnych funkcji Hyper-V do dodatkowego centrum danych](site-recovery-vmm-to-vmm.md)
* Jeśli przeprowadzasz replikację do dodatkowego centrum danych, przeczytaj temat [obsługiwane systemy operacyjne gościa dla maszyn wirtualnych funkcji Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Jeśli przeprowadzasz replikację do platformy Azure, Usługa Site Recovery obsługuje wszystkie systemy operacyjne gościa, które są [obsługiwanym przez platformę Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Gdy funkcja Hyper-V działa w systemie operacyjnym klienta można chronić maszyny wirtualne?
Nie. Maszyny wirtualne muszą znajdować się na serwerze hosta funkcji Hyper-V, który jest uruchomiony na serwerze z obsługiwanym systemem Windows. Jeśli musisz chronić komputer kliencki, możesz replikować go jako maszynę fizyczną do [Azure](site-recovery-vmware-to-azure.md) lub [dodatkowego centrum danych](site-recovery-vmware-to-vmware.md).

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Czy hosty funkcji Hyper-V muszą znajdować się w chmurach programu VMM?
Jeśli chcesz replikować do dodatkowego centrum danych, a następnie maszyn wirtualnych funkcji Hyper-V muszą znajdować się na funkcji Hyper-V hostuje serwery znajdujące się w chmurze programu VMM. Można replikować do platformy Azure, można replikować maszyny wirtualne, z lub bez niej chmur programu VMM. [Dowiedz się więcej](tutorial-hyper-v-to-azure.md) dotyczących replikacji funkcji Hyper-V do platformy Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Czy można replikować maszyny wirtualne funkcji Hyper-V drugiej generacji do platformy Azure?
Tak. Usługa Site Recovery konwertuje z generacji 2 do generacji 1, podczas trybu failover. W przypadku powrotu po awarii maszyny jest konwertowana do generacji 2. [Dowiedz się więcej](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Czy mogę wdrożyć usługę Site Recovery z programem VMM, jeśli mam tylko jeden serwer VMM?

Tak. Można replikować maszyny wirtualne na serwerach funkcji Hyper-V w chmurze programu VMM do platformy Azure lub replikować je między chmurami programu VMM na tym samym serwerze. Dla środowiska lokalnego do replikacji lokalnej zaleca się posiadanie serwera VMM w lokacjach głównych i dodatkowych. 

### <a name="what-do-i-need-in-azure"></a>Czego potrzebujesz na platformie Azure?
Potrzebujesz subskrypcji platformy Azure, magazyn usługi Recovery Services, konta magazynu i sieci wirtualnej. Magazyn, konto magazynu i sieci musi być w tym samym regionie.

### <a name="what-azure-storage-account-do-i-need"></a>Konto usługi Azure storage, które jest potrzebne?
Wymagane jest konto magazynu LRS lub GRS. Zalecamy użycie konta GRS, dzięki czemu dane będą odporne w przypadku regionalnej awarii lub sytuacji, w której nie można odzyskać regionu podstawowego. Usługa Premium storage jest obsługiwana.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Czy Moje konto platformy Azure potrzebuje uprawnień do tworzenia maszyn wirtualnych?
Jeśli jesteś administratorem subskrypcji, masz uprawnienia do replikacji, które są potrzebne. Jeśli nie masz, potrzebujesz uprawnień, aby utworzyć Maszynę wirtualną platformy Azure w grupie zasobów i sieci wirtualnej można określić podczas konfigurowania Site Recovery i uprawnienia do zapisu do wybranego konta magazynu. [Dowiedz się więcej](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Dane replikacji są wysyłane do usługi Site Recovery?
Nie, Usługa Site Recovery nie przechwytuje replikowanych danych, nie ma żadnych informacji na temat co działa na maszynach wirtualnych. Dane replikacji są wymieniane między hostami funkcji Hyper-V i Azure storage. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Usługa Site Recovery jest ISO 27001: 2013, 27018, HIPAA, DPA certyfikowane i jest w trakcie SOC2 i FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Może nam obowiązek przechowywania metadanych w środowisku lokalnym w regionie geograficznym?
Tak. Po utworzeniu magazynu w regionie, Upewniamy się, że wszystkie metadane używane przez pozostaje Site Recovery w ramach tego regionu geograficznego.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Tak, zarówno szyfrowanie przesyłanych w i [szyfrowania na platformie Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) są obsługiwane.


## <a name="deployment"></a>Wdrożenie

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Co można zrobić za pomocą funkcji Hyper-V do platformy Azure replikacji?

- **Odzyskiwanie po awarii**: Możesz skonfigurować odzyskiwanie po awarii w pełnej. W tym scenariuszu replikowania lokalnych maszyn wirtualnych z funkcją Hyper-V do usługi Azure storage:
    - Maszyny wirtualne można replikować na platformę Azure. Jeśli infrastruktury lokalnej jest niedostępny, trybu failover na platformie Azure.
    - Po przełączeniu w tryb failover maszyn wirtualnych platformy Azure są tworzone przy użyciu replikowanych danych. Dostępne aplikacje i obciążenia na maszynach wirtualnych platformy Azure.
    - Gdy ponownie będzie dostępna w lokalnym centrum danych, można powrotu po awarii z platformy Azure do lokacji lokalnej.
- **Migracja**: Usługa Site Recovery umożliwia migrowanie lokalnych maszyn wirtualnych z funkcją Hyper-V do usługi Azure storage. Następnie możesz Failover ze środowiska lokalnego do platformy Azure. Po przejściu w tryb failover swoje aplikacje i obciążenia są dostępne i działają na maszynach wirtualnych platformy Azure.


### <a name="what-do-i-need-on-premises"></a>Co zrobić, należy w środowisku lokalnym?

Potrzebujesz co najmniej jedna maszyna wirtualna uruchomiona na autonomicznego lub klastra hostów funkcji Hyper-V. Można również replikować maszyny wirtualne uruchomione na hostach zarządzanych przez System Center Virtual Machine Manager (VMM).
- Jeśli nie używasz programu VMM podczas wdrażania usługi Site Recovery, zbieranie hostów funkcji Hyper-V i klastrów w lokacji funkcji Hyper-V. Agenci Site Recovery (agent dostawcy usługi Azure Site Recovery i Recovery Services) na każdym hoście funkcji Hyper-V.
- Jeśli hosty funkcji Hyper-V znajdują się w chmurze programu VMM, możesz organizować replikacji w programie VMM. Możesz zainstalować dostawcę usługi Site Recovery na serwerze VMM oraz agenta usług Recovery Services na każdym hoście funkcji Hyper-V. Mapowania sieci logiczne i maszyn wirtualnych program VMM i sieci wirtualnych platformy Azure.
- [Dowiedz się więcej](hyper-v-azure-architecture.md) o funkcji Hyper-V do platformy Azure architektury.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Czy można replikować maszyny wirtualne znajdujące się w klastrze funkcji Hyper-V?

Tak, Usługa Site Recovery obsługuje klastrowane hosty funkcji Hyper-V. Należy pamiętać o następujących kwestiach:

- Wszystkie węzły klastra powinien być zarejestrowany w tym samym magazynie.
- Jeśli nie używasz programu VMM, wszystkie hosty funkcji Hyper-V w klastrze, należy dodać do tej samej lokacji funkcji Hyper-V.
- Zainstaluj agenta dostawcy usługi Azure Site Recovery i Recovery Services na każdym hoście funkcji Hyper-V w klastrze i Dodaj hostów do lokacji funkcji Hyper-V.
- Nie określonej czynności należy wykonać, w klastrze.
- Jeśli uruchamiasz narzędzie planista wdrażania funkcji Hyper-v, narzędzie zbiera dane profilu z węzła, w którym jest uruchomiona i której maszyna wirtualna jest uruchomiona. Narzędzia nie zbierają żadnych danych z węzła, który jest wyłączony, ale będzie śledzić tego węzła. Po skonfigurowaniu i uruchomieniu węzła rozpoczyna zbieranie danych profilu maszyny Wirtualnej z niego (Jeśli maszyna wirtualna jest częścią profilu listy maszyn wirtualnych i jest uruchomiona w węźle).
- Jeśli Maszynę wirtualną na hoście funkcji Hyper-V w magazynie usługi Site Recovery jest migrowana do innego hosta funkcji Hyper-V, w tym samym klastrze lub do hosta autonomicznego, nie jest to negatywny wpływ na replikację maszyny Wirtualnej. Host funkcji Hyper-V musi spełniać [wymagania wstępne](hyper-v-azure-support-matrix.md#on-premises-servers)i być skonfigurowana w magazynie usługi Site Recovery. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Gdy funkcja Hyper-V działa w systemie operacyjnym klienta można chronić maszyny wirtualne?
Nie. Maszyny wirtualne muszą znajdować się na serwerze hosta funkcji Hyper-V, który jest uruchomiony na serwerze z obsługiwanym systemem Windows. Jeśli musisz chronić komputer kliencki może [replikować go jako maszynę fizyczną](physical-azure-disaster-recovery.md) na platformie Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Czy można replikować maszyny wirtualne funkcji Hyper-V drugiej generacji do platformy Azure?
Tak. Usługa Site Recovery konwertuje z generacji 2 do generacji 1, podczas trybu failover. W przypadku powrotu po awarii maszyny jest konwertowana do generacji 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Mogę zautomatyzować usługę Site Recovery scenariuszy przy użyciu zestawu SDK?
Tak. Przepływy pracy usługi Site Recovery można zautomatyzować przy użyciu interfejsu API REST, programu PowerShell lub zestawu SDK platformy Azure. Aktualnie obsługiwanych scenariuszach na potrzeby replikacji funkcji Hyper-V do platformy Azure przy użyciu programu PowerShell:

- [Replikacja funkcji Hyper-V bez programu VMM przy użyciu programu PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replikacja funkcji Hyper-V za pomocą programu VMM przy użyciu programu Powershell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikacja

### <a name="where-do-on-premises-vms-replicate-to"></a>Gdzie są lokalne maszyny wirtualne replikowane do
Dane są replikowane do usługi Azure storage. Po uruchomieniu trybu failover Usługa Site Recovery automatycznie tworzy maszyny wirtualne platformy Azure z konta magazynu.

### <a name="what-apps-can-i-replicate"></a>Jakie aplikacje można replikować?
Można replikować dowolną aplikację lub Obciążenie uruchomionej maszyny Wirtualnej funkcji Hyper-V, który jest zgodny z [wymagania dotyczące replikacji](hyper-v-azure-support-matrix.md#replicated-vms). Usługa Site Recovery obsługuje replikację z uwzględnieniem aplikacji, dzięki czemu aplikacje mogą przełączone w tryb failover i do inteligentnego stanu. Usługa Site Recovery integruje się z aplikacjami firmy Microsoft, takich jak SharePoint, Exchange, Dynamics, SQL Server i usługi Active Directory i ściśle współpracuje z wiodącymi dostawcami oprogramowania, w tym Oracle, SAP, IBM i Red Hat. [Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="whats-the-replication-process"></a>Co to jest proces replikacji?

1. Po wyzwoleniu replikacji początkowej migawka maszyny Wirtualnej funkcji Hyper-V zostanie utworzona.
2. Wirtualne dyski twarde na maszynie Wirtualnej są replikowane pojedynczo, dopóki nie zostaną wszystkie skopiowane na platformę Azure. Może to potrwać kilka minut w zależności od rozmiaru maszyny Wirtualnej i przepustowości sieci. Dowiedz się, jak zwiększyć przepustowość sieci.
3. Jeśli zmiany dysku podczas replikacji początkowej jest w toku, funkcji Hyper-V Replica Replication Tracker śledzi zmiany jako dzienniki replikacji funkcji Hyper-V (hrl). Te pliki dziennika znajdują się w tym samym folderze co dyski. Każdy dysk ma skojarzony plik hrl wysyłanego do magazynu pomocniczego. Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny wirtualnej jest usuwana.
5. Wszelkie zmiany dysku w dzienniku zostaną zsynchronizowane i scalone z dyskiem nadrzędnym.
6. Po zakończeniu replikacji początkowej ochronę Finalize na zadanie maszyny wirtualnej jest uruchamiany. Jego konfiguruje ustawienia sieciowe i inne ustawienia po replikacji, aby maszyna wirtualna jest chroniona.
7. Na tym etapie możesz sprawdzić ustawienia maszyny Wirtualnej, aby upewnić się, że jest gotowa do trybu failover. Możesz uruchomić odzyskiwanie po awarii (Testowanie trybu failover) dla maszyny Wirtualnej, aby sprawdzić, czy niepowodzenia ponad zgodnie z oczekiwaniami.
8. Po przeprowadzeniu replikacji początkowej replikacji różnicowej rozpoczyna się, zgodnie z zasadami replikacji.
9. Zmiany są pliki hrl rejestrowane. Z każdym dyskiem skonfigurowanym pod kątem replikacji jest skojarzony plik hrl.
10. Dziennik jest wysyłany do konta magazynu klienta. Gdy dziennika jest przesyłany do platformy Azure, zmiany na dysku podstawowym są śledzone w innym pliku dziennika, w tym samym folderze.
11. Podczas replikacji początkowej i różnicowej możesz monitorować maszynę Wirtualną w witrynie Azure portal.

[Dowiedz się więcej](hyper-v-azure-architecture.md#replication-process) informacji na temat procesu replikacji.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Można replikować na platformę Azure za pomocą połączenia VPN lokacja lokacja?

Usługa Site Recovery replikuje dane ze środowiska lokalnego do usługi Azure storage za pośrednictwem publicznego punktu końcowego lub przy użyciu usługi ExpressRoute publicznej komunikacji równorzędnej. Replikacja za pośrednictwem sieci VPN typu lokacja lokacja nie jest obsługiwana.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Można replikować na platformę Azure przy użyciu usługi ExpressRoute?

Tak, usługa ExpressRoute może służyć do replikowania maszyn wirtualnych na platformie Azure. Usługa Site Recovery replikuje dane do konta usługi Azure Storage za pośrednictwem publicznego punktu końcowego i należy skonfigurować [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#publicpeering) dla replikacji usługi Site Recovery. Po maszyny wirtualne nie za pośrednictwem sieci wirtualnej platformy Azure, można z nich korzystać przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Dlaczego nie można replikować za pośrednictwem sieci VPN?

W przypadku replikacji na platformie Azure ruch związany z replikacją osiągnie publiczne punkty końcowe konta usługi Azure Storage. Dlatego tylko można replikować za pośrednictwem publicznej sieci internet przy użyciu usługi ExpressRoute (publicznej komunikacji równorzędnej) i sieci VPN nie działa. 

### <a name="what-are-the-replicated-vm-requirements"></a>Co to są replikowane wymagań dotyczących maszyn wirtualnych?

W przypadku replikacji maszyny Wirtualnej funkcji Hyper-V musi działać obsługiwany system operacyjny. Ponadto maszyna wirtualna musi spełniać wymagania dotyczące maszyn wirtualnych platformy Azure. [Dowiedz się więcej](hyper-v-azure-support-matrix.md#replicated-vms) w macierzy obsługi.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak często można replikować na platformę Azure?

Maszyny wirtualne funkcji Hyper-V mogą być replikowane co 30 sekund (z wyjątkiem usługi premium storage), 5 minut lub 15 minut.

### <a name="can-i-extend-replication"></a>Czy mogę rozszerzyć replikację
Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Zażądać tej funkcji w [forum z opiniami](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Można zrobić początkowej replikacji offline?
Ta funkcja nie jest obsługiwana. Zażądać tej funkcji w [forum z opiniami](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Czy można wykluczyć dysków?
Tak, można wykluczyć dyski z replikacji. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Maszyny wirtualne można replikować z dyskami dynamicznymi?
Dyski dynamiczne mogą być replikowane. Dysk systemu operacyjnego musi być dyskiem podstawowym.



## <a name="security"></a>Bezpieczeństwo

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Jakiego dostępu jest konieczne Site Recovery na hostach funkcji Hyper-V

Usługa Site Recovery wymaga dostępu do hostów funkcji Hyper-V można replikować maszyny wirtualne, możesz wybrać. Na hostach funkcji Hyper-V Usługa Site Recovery instaluje następujące czynności:

- Jeśli nie korzystasz z programu VMM, dostawca usługi Azure Site Recovery i agenta usług Recovery Services są zainstalowane na każdym hoście.
- Jeśli korzystasz z programu VMM, agent usługi Recovery Services jest instalowany na każdym hoście. Dostawca jest uruchamiany na serwerze programu VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Co to usługa Site Recovery Zainstaluj na maszynach wirtualnych funkcji Hyper-V?

Usługa Site Recovery jawnie niczego nie instaluje na maszynach wirtualnych funkcji Hyper-V włączona replikacja.




## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii


### <a name="how-do-i-fail-over-to-azure"></a>Jak przełączyć tryb failover do platformy Azure?

Na platformie Azure, można uruchomić planowanego lub nieplanowanego trybu failover z maszynami wirtualnymi funkcji Hyper-V w środowisku lokalnym.
    - Jeśli zostanie uruchomione planowane przejście w tryb failover, źródłowe maszyny wirtualne zostaną wyłączone w celu zapewnienia, że nie będzie miała miejsca utrata danych.
    - Jeśli lokacji głównej nie jest dostępny, można uruchomić nieplanowany tryb failover.
    - W trybie Failover pojedynczą maszynę lub tworzyć plany odzyskiwania, aby organizować tryb failover wiele maszyn.
    - Możesz uruchomić tryb failover. Po zakończeniu pierwszego etapu przejścia w tryb failover, należy można zobaczyć utworzone repliki maszyn wirtualnych na platformie Azure. Jeśli jest to wymagane, do maszyny wirtualnej można przypisać publiczny adres IP. Następnie następuje zatwierdzenie trybu failover, aby można było rozpocząć uzyskiwanie dostępu do obciążenia z poziomu repliki maszyny Wirtualnej platformy Azure.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak dostęp do maszyn wirtualnych platformy Azure po włączeniu trybu failover?
Po przejściu w tryb failover maszyn wirtualnych platformy Azure są dostępne za pośrednictwem bezpiecznego połączenia internetowego, za pośrednictwem sieci VPN lokacja lokacja lub za pośrednictwem usługi Azure ExpressRoute. Należy przygotować kilka rzeczy w celu nawiązania połączenia. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Jest w trybie Failover odporność danych?
Platforma Azure została zaprojektowana z myślą o odporności danych. Usługa Site Recovery został zaprojektowany do trybu failover do pomocniczego centrum danych platformy Azure, zgodnie z umową SLA platformy Azure. Po przejściu do trybu failover, możemy zagwarantować metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla magazynu.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?
[Tryb failover](site-recovery-failover.md) nie jest automatyczny. Zainicjuj tryb failover z jednym kliknięciem w portalu lub użyć [PowerShell](/powershell/module/az.recoveryservices) do wyzwolenia przejścia w tryb failover.

### <a name="how-do-i-fail-back"></a>Jak I powrót po awarii?

Gdy infrastruktury lokalnej będzie uruchomione ponownie, możesz można wykonać powrotu po awarii. Powrót po awarii odbywa się w trzech etapach:

1. Należy rozpocząć planowanego trybu failover z platformy Azure do lokacji lokalnej przy użyciu kilku różnych opcji:

    - Skrócić czas przestoju: Jeśli używasz tej opcji usługa Site Recovery synchronizuje dane przed włączeniem trybu failover. Sprawdza, czy dane zmienione bloki i pobiera je do lokacji lokalnej, podczas przechowuje maszyny Wirtualnej platformy Azure, działa, minimalizując przestoje. Ręczne określenie, czy tryb failover, należy wykonać, zamknięcie maszyny Wirtualnej platformy Azure, wszelkie zmiany różnicowe końcowe są kopiowane i uruchomieniem trybu failover.
    - Pełne pobieranie: Po wybraniu tej opcji dane są synchronizowane podczas pracy awaryjnej. Ta opcja spowoduje pobranie całego dysku. Jest szybsze, ponieważ nie sumy kontrolne są obliczane, ale ma więcej przestojów. Użyj tej opcji, jeśli działała repliki maszyn wirtualnych platformy Azure przez pewien czas lub lokalna maszyna wirtualna została usunięta.

2. Możesz wybrać, czy niepowodzenie powrót do tej samej maszyny Wirtualnej lub do alternatywnej maszyny Wirtualnej. Można określić, Usługa Site Recovery należy utworzyć maszynę Wirtualną, jeśli jeszcze nie istnieje.
3. Po zakończeniu początkowej synchronizacji, należy wybrać do przełączenia w tryb failover. Po zakończeniu instalacji można logowaniu do lokalnej maszyny Wirtualnej do sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami. W witrynie Azure portal widać, że maszyny wirtualne platformy Azure zostały zatrzymane.
4. Należy zatwierdzić tryb failover, aby zakończyć i rozpocząć uzyskiwanie dostępu do obciążenia z lokalnej maszyny Wirtualnej ponownie.
5. Po obciążeń przywrócono po awarii, możesz włączyć replikacji odwrotnej, tak, aby replikować do platformy Azure ponownie lokalnych maszyn wirtualnych.

### <a name="can-i-fail-back-to-a-different-location"></a>Może nie do innej lokalizacji?
Tak, jeśli Failover na platformie Azure można powrotu po awarii do innej lokalizacji Jeśli oryginalny jest niedostępna. [Dowiedz się więcej](hyper-v-azure-failback.md#failback-to-an-alternate-location-in-hyper-v-environment).
