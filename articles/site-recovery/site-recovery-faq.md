---
title: 'Usługa Azure Site Recovery: Często zadawane pytania | Dokumentacja firmy Microsoft'
description: W tym artykule omówiono popularnych pytania dotyczące usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 0ec7f9e692dd4269ccb428d644c673e5c27b413a
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Usługa Azure Site Recovery: często zadawane pytania (FAQ)
Ten artykuł zawiera często zadawane pytania dotyczące usługi Azure Site Recovery. Jeśli masz pytania po przeczytaniu tego artykułu, opublikuj je na [Forum usług odzyskiwania Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Ogólne
### <a name="what-does-site-recovery-do"></a>Do czego służy usługa Site Recovery?
Usługa Site Recovery przyczynia się do ciągłość prowadzenia działalności biznesowej i strategia odzyskiwania (BCDR), poprzez organizowanie i automatyzowanie replikacji maszyn wirtualnych Azure między regionami, lokalnych maszyn wirtualnych i serwerów fizycznych do platformy Azure i lokalnych maszyn dodatkowego centrum danych. [Dowiedz się więcej](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>Co może chronić Usługa Site Recovery?
* **Maszyny wirtualne platformy Azure**: Usługa Site Recovery może replikować dowolne obciążenia uruchomione na obsługiwanej maszynie Wirtualnej Azure
* **Maszyny wirtualne funkcji Hyper-V**: Usługa Site Recovery może chronić dowolne obciążenia uruchomione na maszynie Wirtualnej funkcji Hyper-V.
* **Serwery fizyczne**: Usługa Site Recovery może chronić serwery fizyczne z systemem Windows lub Linux.
* **Maszyny wirtualne VMware**: Usługa Site Recovery może chronić dowolne obciążenia uruchomione na maszynie wirtualnej VMware.



### <a name="can-i-replicate-azure-vms"></a>Czy można replikować maszyny wirtualne Azure?
Tak, obsługiwanych maszynach wirtualnych platformy Azure można replikować między regiony platformy Azure. [Dowiedz się więcej](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Co należy w funkcji Hyper-V do organizowania replikacji z usługą Site Recovery?
Wymagania serwera hosta funkcji Hyper-V zależą od scenariusza wdrożenia. Zapoznaj się z wymaganiami wstępnymi funkcji Hyper-V:

* [Replikowanie maszyn wirtualnych funkcji Hyper-V (bez VMM) na platformie Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replikowanie maszyn wirtualnych funkcji Hyper-V (w programie VMM) na platformie Azure](site-recovery-vmm-to-azure.md)
* [Replikowanie maszyn wirtualnych funkcji Hyper-V do dodatkowego centrum danych](site-recovery-vmm-to-vmm.md)
* Jeśli przeprowadzasz replikację do dodatkowego centrum danych, przeczytaj o [obsługiwane systemy operacyjne gościa dla maszyn wirtualnych funkcji Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Jeśli przeprowadzasz replikację do platformy Azure, Usługa Site Recovery obsługuje wszystkie systemy operacyjne gościa są [obsługiwany przez platformę Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Maszyny wirtualne można chronić po funkcji Hyper-V działa w systemie operacyjnym klienta?
Nie. Maszyny wirtualne muszą znajdować się na serwerze hosta funkcji Hyper-V, który jest uruchomiony na serwerze z obsługiwanym systemem Windows. Jeśli musisz chronić komputer kliencki, możesz replikować go jako maszynę fizyczną do [Azure](site-recovery-vmware-to-azure.md) lub [dodatkowego centrum danych](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Jakie obciążenia mogę chronić za pomocą usługi Site Recovery?
Usługa Site Recovery służy do ochrony większości obciążenia uruchomione na obsługiwanej maszynie Wirtualnej lub serwerze fizycznym. Usługa Site Recovery zapewnia obsługę replikacji obsługującej, dzięki czemu aplikacje mogą być odzyskiwane do inteligentnego stanu. Integruje się z aplikacjami firmy Microsoft, takich jak SharePoint, Exchange, Dynamics, SQL Server i usługi Active Directory i ściśle współpracuje z wiodącymi dostawcami oprogramowania, w tym Oracle, SAP, IBM i Red Hat. [Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Czy hosty funkcji Hyper-V muszą znajdować się w chmurach programu VMM?
Jeśli chcesz replikować do dodatkowego centrum danych, a następnie maszyn wirtualnych funkcji Hyper-V muszą znajdować się na funkcji Hyper-V obsługuje serwery znajdujące się w chmurze VMM. Aby replikować do platformy Azure, można replikować maszyny wirtualne, z chmur VMM lub bez. [Dowiedz się więcej](tutorial-hyper-v-to-azure.md) o replikacji funkcji Hyper-V do platformy Azure.

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Czy mogę wdrożyć usługę Site Recovery z programem VMM, jeśli mam tylko jeden serwer VMM?

Tak. Możesz replikować maszyny wirtualne na serwerach funkcji Hyper-V w chmurze VMM do platformy Azure lub replikować je między chmurami VMM na tym samym serwerze. Dla lokalnej do lokalnej replikacji zalecamy posiadanie serwera VMM w lokacjach głównych i dodatkowych.  

### <a name="what-physical-servers-can-i-protect"></a>Jakie serwery fizyczne mogę chronić?
Serwery fizyczne z systemem Windows i Linux na platformie Azure lub lokacji dodatkowej można replikować. Dowiedz się więcej o wymaganiach dotyczących [replikacji do platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines), i [replikacji do lokacji dodatkowej](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


Należy pamiętać, że serwery fizyczne będzie uruchamiany jako maszyn wirtualnych na platformie Azure, jeśli spadnie do serwera lokalnego. Powrót po awarii do lokalnego serwera fizycznego nie jest obecnie obsługiwany. Dla komputera chronionego jak fizyczną można tylko powrotu po awarii do maszyny wirtualnej VMware.

### <a name="what-vmware-vms-can-i-protect"></a>Jakie maszyny wirtualne VMware mogę chronić?

Aby chronić maszyny wirtualne VMware, należy posiadać program vSphere Hypervisor i maszyny wirtualne z uruchomionymi narzędziami VMware. Zalecamy również posiadanie serwera VMware vCenter do zarządzania funkcjami hypervisor. Dowiedz się więcej o wymaganiach dotyczących [replikacji do platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines), lub [replikacji do lokacji dodatkowej](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Czy mogę zarządzać odzyskiwaniem po awarii dla oddziałów firmy przy użyciu usługi Site Recovery?
Tak. Gdy używasz usługi Site Recovery do organizowania replikacji i trybu failover w biurach oddziałów, uzyskasz ujednoliconego aranżacji i widoku wszystkie obciążenia oddziałów w centralnej lokalizacji. Możesz z łatwością uruchomić tryb failover i zarządzać odzyskiwaniem po awarii wszystkich oddziałów z siedziby, bez konieczności odwiedzania oddziałów.

## <a name="pricing"></a>Cennik
Cennik pytania związane z, można znaleźć na stronie często zadawane pytania pod [cennik usługi Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

## <a name="security"></a>Bezpieczeństwo
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?
Nie, Usługa Site Recovery nie przechwytuje replikowanych danych, nie ma żadnych informacji o co działa na maszynach wirtualnych lub serwerach fizycznych.
Dane replikacji są wymieniane między lokalnymi hostami funkcji Hyper-V, funkcjami hypervisor VMware lub serwerami fizycznymi i usługą Azure Storage lub lokacją dodatkową. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Usługa Site Recovery jest ISO 27001: 2013, 27018, HIPAA, DPA certyfikowane i jest w trakcie oceny SOC2 i FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Ze względu na zgodność nawet naszych lokalnych metadanych muszą pozostać w tym samym regionie geograficznym. Usługa Site Recovery pomaga nam?
Tak. Po utworzeniu magazynu usługi Site Recovery w regionie, Upewniamy się, że wszystkie metadane, należy włączyć i organizowania replikacji i trybu failover pozostaje w tym regionie obiektu geograficznego granic.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Dla maszyn wirtualnych i serwerów fizycznych replikacji między lokalnymi witryn szyfrowania podczas przesyłania danych jest obsługiwane. Dla maszyn wirtualnych i serwerów fizycznych replikację do platformy Azure, zarówno szyfrowanie podczas przesyłania danych i [szyfrowania na rest (na platformie Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) są obsługiwane.

## <a name="replication"></a>Replikacja

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Można replikować za pośrednictwem sieci VPN lokacja lokacja Azure?
Usługa Azure Site Recovery replikuje dane do konta magazynu Azure za pośrednictwem publicznego punktu końcowego. Replikacja nie jest za pośrednictwem sieci VPN lokacja lokacja. Można utworzyć sieci VPN lokacja lokacja z sieci wirtualnej platformy Azure. To nie zakłóca replikacji usługi Site Recovery.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Replikacja maszyn wirtualnych na platformie Azure mogą używać usługi ExpressRoute?
Tak, [można ExpressRoute](concepts-expressroute-with-site-recovery.md) do replikowania lokalnych maszyn wirtualnych na platformie Azure. Usługa Azure Site Recovery replikuje dane do konta magazynu platformy Azure za pośrednictwem publicznego punktu końcowego. Należy skonfigurować [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) lub [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoft-peering) ExpressRoute na potrzeby replikacji usługi Site Recovery. Komunikacji równorzędnej firmy Microsoft jest zalecane domeny routingu dla replikacji. Po maszyny wirtualne zostały nie za pośrednictwem sieci wirtualnej platformy Azure będą dostępne za pomocą [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) Instalatora z sieci wirtualnej platformy Azure. Replikacja nie jest obsługiwana w prywatnej komunikacji równorzędnej.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Czy istnieją wymagania wstępne związane z replikacją maszyn wirtualnych do platformy Azure?
[Maszyny wirtualne VMware](vmware-physical-azure-support-matrix.md#replicated-machines) i [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) chcesz replikować do platformy Azure muszą być zgodne z wymaganiami platformy Azure.

Twoje konto użytkownika usługi Azure musi mieć niektórych [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) Aby włączyć replikację nowej maszyny wirtualnej na platformie Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Czy można replikować maszyny wirtualne funkcji Hyper-V drugiej generacji do platformy Azure?
Tak. Usługa Site Recovery konwertuje z generacji 2 generacji 1 podczas pracy awaryjnej. W przypadku powrotu po awarii komputera jest konwertowana z powrotem do generacji 2. [Dowiedz się więcej](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>W przypadku replikacji do platformy Azure, jak będę płacić za maszyny wirtualne Azure?
Podczas regularnej replikacji dane są replikowane do magazynu geograficznie nadmiarowego Azure i nie trzeba płacić opłaty maszyny wirtualne Azure IaaS dostarczanie znaczących korzyści. Po uruchomieniu trybu failover na platformie Azure usługa Site Recovery automatycznie utworzy maszyny wirtualne Azure w modelu IaaS. Po tym zostaną naliczone opłaty za zasoby obliczeniowe, które zostaną zużyte na platformie Azure.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Czy można zautomatyzować scenariuszy odzyskiwania lokacji przy użyciu zestawu SDK?
Tak. Przepływy pracy usługi Site Recovery można zautomatyzować przy użyciu interfejsu API REST, programu PowerShell lub zestawu SDK platformy Azure. Aktualnie obsługiwanych scenariuszach wdrażania usługi Site Recovery przy użyciu programu PowerShell:

* [Replikowanie maszyn wirtualnych funkcji Hyper-V w chmurach VMMs do Menedżera zasobów Azure PowerShell](hyper-v-vmm-powershell-resource-manager.md)
* [Replikowanie maszyn wirtualnych funkcji Hyper-V bez programu VMM do Menedżera zasobów Azure PowerShell](hyper-v-azure-powershell-resource-manager.md)
* [Replikacja VMware do platformy Azure za pomocą Menedżera zasobów programu PowerShell](vmware-azure-disaster-recovery-powershell.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Jeśli przeprowadzam replikację do platformy Azure, jakiego konta magazynu potrzebuję?
Potrzebujesz konta magazynu LRS lub GRS. Zalecamy użycie konta GRS, dzięki czemu dane będą odporne w przypadku regionalnej awarii lub sytuacji, w której nie można odzyskać regionu podstawowego. Konto musi znajdować się w tym samym regionie co magazyn Usług odzyskiwania. Magazyn w warstwie Premium jest obsługiwana dla maszyny Wirtualnej VMware, maszyna wirtualna funkcji Hyper-V i replikacji serwera fizycznego, podczas wdrażania usługi Site Recovery w portalu Azure.

### <a name="how-often-can-i-replicate-data"></a>Jak często mogę replikować dane?
* **Funkcja Hyper-V:** maszyn wirtualnych funkcji Hyper-V mogą być replikowane co 30 sekund (z wyjątkiem magazyn w warstwie premium), 5 minut lub 15 minut. Jeśli po skonfigurowaniu replikacji sieci SAN replikacja jest synchroniczne.
* **Maszyny wirtualne VMware i serwery fizyczne:** częstotliwość replikacji nie jest tutaj istotna. Replikacja jest ciągły.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Czy mogę rozszerzyć replikację z istniejącej lokacji odzyskiwania do innej lokacji trzeciorzędny?
Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Żądanie tę funkcję w [forum opinii](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Czy mogę przeprowadzić replikację w trybie offline podczas pierwszej replikacji do platformy Azure?
Ta funkcja nie jest obsługiwana. Żądanie tę funkcję w [forum opinii](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Czy z replikacji można wykluczyć określone dyski?
Jest to obsługiwane w przypadku replikacji maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V w systemie Azure przy użyciu portalu Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Czy można replikować maszyny wirtualne z dyskami dynamicznymi?
Dyski dynamiczne są obsługiwane w przypadku replikacji maszyn wirtualnych funkcji Hyper-V. Są one również obsługiwane podczas replikowania maszyn wirtualnych VMware i maszyn fizycznych do platformy Azure. Dysk systemu operacyjnego musi być dyskiem podstawowym.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Można dodać nowego komputera do istniejącej grupy replikacji?
Dodawanie nowych maszyn do istniejących grup replikacji jest obsługiwane. Aby to zrobić, wybierz grupę replikacji (z bloku "Zreplikowane elementy") i kliknij prawym przyciskiem myszy wybierz menu kontekstowe w grupie replikacji, a następnie wybierz odpowiednią opcję.

![Dodaj do grupy replikacji](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Czy mogę ograniczyć przepustowość przydzieloną dla ruchu replikacji funkcji Hyper-V?
Tak. Możesz przeczytać więcej na temat ograniczania przepustowości w artykułach wdrażania:

* [Planowanie wydajności dla replikowanie maszyn wirtualnych VMware oraz serwerach fizycznych](site-recovery-plan-capacity-vmware.md)
* [Planowanie wydajności dla replikację maszyn wirtualnych funkcji Hyper-V do platformy Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>Tryb failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Czy mogę używam przechodzenie w tryb failover na platformie Azure, jak uzyskać dostęp do maszyn wirtualnych platformy Azure po trybu failover?
Maszyny wirtualne Azure są dostępne za pośrednictwem bezpiecznego połączenia internetowego, sieci VPN między lokacjami lub za pośrednictwem usługi Azure ExpressRoute. Musisz przygotować kilka rzeczy, aby można było połączyć. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Jeśli na platformie Azure, jak platforma Azure upewnia się za pośrednictwem nie moje dane są odporne?
Platforma Azure została zaprojektowana z myślą o odporności danych. Usługi Site Recovery jest już zaprojektowana z myślą o trybu failover do pomocniczego centrum danych Azure, zgodnie z warunkami umowy SLA platformy Azure, jeśli zajdzie taka potrzeba. W takim przypadku Upewniamy metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla magazynu.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Jeśli Przeprowadzam replikację między dwoma centrami danych co się stanie w przypadku awarii napotka Moje podstawowego centrum danych?
Możesz wyzwolić nieplanowany tryb failover z lokacji dodatkowej. Usługa Site Recovery nie wymaga łączności z lokacją główną do pracy w trybie failover.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?
Tryb failover nie jest automatyczny. Zainicjuj tryb failover z jednym kliknięciem w portalu lub użyć [PowerShell odzyskiwania lokacji](/powershell/module/azurerm.siterecovery) aby wyzwolić tryb failover. Niepowodzenie wstecz jest proste działania w portalu usługi Site Recovery.

Można zautomatyzować można użyć lokalnego narzędzia Orchestrator lub programu Operations Manager można wykryć awarię maszyny wirtualnej i wyzwolić tryb failover przy użyciu zestawu SDK.

* [Dowiedz się więcej](site-recovery-create-recovery-plans.md) o planach odzyskiwania.
* [Dowiedz się więcej](site-recovery-failover.md) o pracy awaryjnej.
* [Dowiedz się więcej](site-recovery-failback-azure-to-vmware.md) o niepowodzeniu kopię maszyn wirtualnych VMware oraz serwerach fizycznych

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Jeśli host Moje lokalnymi nie jest nieodpowiadający lub awaria, czy można trybu failover do innego hosta?
Tak, można użyć odzyskiwanie do lokalizacji alternatywnej do powrotu po awarii do innego hosta z platformy Azure. Dowiedz się więcej o opcjach dostępnych w poniższych łączy dla maszyn wirtualnych VMware i funkcji Hyper-V.

* [W przypadku maszyn wirtualnych VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [W przypadku maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-failback.md#perform-failback)

## <a name="service-providers"></a>Dostawcy usług
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jestem dostawcą usług. Usługa Site Recovery działa dla modeli opartych na infrastrukturze dedykowanej i współdzielonej?
Tak. Usługa Site Recovery obsługuje modele oparte na infrastrukturze dedykowanej i współdzielonej.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Dla dostawcy usług jest tożsamość mojej dzierżawy jest udostępniana usłudze Site Recovery?
Nie. Tożsamość dzierżawcy pozostaje anonimowy. Twoje dzierżawy nie wymagają dostępu do portalu usługi Site Recovery. Tylko administrator dostawcy usług wykonuje czynności w portalu.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Dane aplikacji dzierżawy kiedykolwiek przejdzie do platformy Azure?
Podczas replikacji między lokacjami należącymi do dostawcy usług dane aplikacji nigdy nie trafiają do platformy Azure. Dane są szyfrowane podczas przesyłania i replikowane bezpośrednio między lokacjami dostawcy usług.

Jeśli przeprowadzasz replikację do platformy Azure, dane aplikacji są wysyłane do usługi Azure Storage, ale nie do usługi Site Recovery. Dane są szyfrowane podczas przesyłania i pozostają zaszyfrowane na platformie Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Czy moje dzierżawy otrzymają rachunek za jakiekolwiek usługi platformy Azure?
Nie. Relacja rozliczeniowa platformy Azure jest nawiązana bezpośrednio z dostawcą usług. Dostawcy usług są odpowiedzialni za generowanie konkretnych rachunków dla swoich dzierżaw.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Jeśli przeprowadzam replikację do platformy Azure, czy moje maszyny wirtualne muszą być uruchomione na platformie Azure przez cały czas?
Nie, dane są replikowane do konta magazynu Azure w ramach subskrypcji. Podczas testowego (test odzyskiwania po awarii) lub rzeczywistego uruchamiania trybu failover usługa Site Recovery automatycznie tworzy maszyny wirtualne w ramach subskrypcji.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Czy podczas replikacji do platformy Azure jest zapewniana izolacja na poziomie dzierżawy?
Tak.

### <a name="what-platforms-do-you-currently-support"></a>Jakie platformy są obecnie obsługiwane?
Firma Microsoft obsługuje pakietu Azure Pack, systemie Cloud Platform System i wdrażania (2012 i nowsze) opartego na programie System Center. [Dowiedz się więcej](https://technet.microsoft.com/library/dn850370.aspx) o integracji pakietu Azure Pack i Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Czy są obsługiwane wdrożenia oparte na jednym pakiecie Azure Pack i jednym serwerze VMM?
Tak, można replikować maszyny wirtualne funkcji Hyper-V do platformy Azure lub między lokacjami dostawcy usług.  Należy pamiętać, że Jeśli replikujesz między lokacjami dostawcy usług integracji elementów runbook platformy Azure jest niedostępna.

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z [omówieniem usługi Site Recovery](site-recovery-overview.md)
* Dowiedz się więcej o [architekturze usługi Site Recovery](site-recovery-components.md)  
