---
title: 'Usługa Azure Site Recovery: Często zadawane pytania | Dokumentacja firmy Microsoft'
description: W tym artykule omówiono często zadawane pytania dotyczące usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: raynew
ms.openlocfilehash: 231533f9609a4cf8cc11bedf88aafdfd37d1cb7e
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050122"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Usługa Azure Site Recovery: często zadawane pytania (FAQ)
Ten artykuł zawiera podsumowanie często zadawane pytania dotyczące usługi Azure Site Recovery. 

## <a name="general"></a>Ogólne
### <a name="what-does-site-recovery-do"></a>Do czego służy usługa Site Recovery?
Usługa Site Recovery przyczynia się do ciągłości działania i strategia odzyskiwania (BCDR), poprzez organizowanie i automatyzowanie replikacji maszyn wirtualnych platformy Azure między regionami, lokalnych maszyn wirtualnych i serwerów fizycznych na platformę Azure i maszyn lokalnych dodatkowego centrum danych. [Dowiedz się więcej](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>Co może chronić Usługa Site Recovery?
* **Maszyny wirtualne platformy Azure**: Usługa Site Recovery może replikować dowolne obciążenia uruchomione na obsługiwanej maszynie Wirtualnej platformy Azure
* **Maszyny wirtualne funkcji Hyper-V**: Usługa Site Recovery może chronić dowolne obciążenia uruchomione na maszynie Wirtualnej funkcji Hyper-V.
* **Serwery fizyczne**: Usługa Site Recovery może chronić serwery fizyczne z systemem Windows lub Linux.
* **Maszyny wirtualne VMware**: Usługa Site Recovery może chronić dowolne obciążenia uruchomione na maszynie wirtualnej VMware.



### <a name="can-i-replicate-azure-vms"></a>Czy można replikować maszyny wirtualne platformy Azure?
Tak, można replikować obsługiwanych maszynach wirtualnych platformy Azure między regionami platformy Azure. [Dowiedz się więcej](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Co należy w funkcji Hyper-V do organizowania replikacji z usługą Site Recovery?
Wymagania serwera hosta funkcji Hyper-V zależą od scenariusza wdrożenia. Zapoznaj się z wymaganiami wstępnymi funkcji Hyper-V:

* [Replicating Hyper-V VMs (without VMM) to Azure (Replikacja maszyn wirtualnych funkcji Hyper-V [bez programu VMM] do platformy Azure)](site-recovery-hyper-v-site-to-azure.md)
* [Replicating Hyper-V VMs (with VMM) to Azure (Replikacja maszyn wirtualnych funkcji Hyper-V [z programem VMM] do platformy Azure)](site-recovery-vmm-to-azure.md)
* [Replicating Hyper-V VMs to a secondary datacenter (Replikacja maszyn wirtualnych funkcji Hyper-V do dodatkowego centrum danych)](site-recovery-vmm-to-vmm.md)
* Jeśli przeprowadzasz replikację do dodatkowego centrum danych, przeczytaj temat [obsługiwane systemy operacyjne gościa dla maszyn wirtualnych funkcji Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Jeśli przeprowadzasz replikację do platformy Azure, Usługa Site Recovery obsługuje wszystkie systemy operacyjne gościa, które są [obsługiwanym przez platformę Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Gdy funkcja Hyper-V działa w systemie operacyjnym klienta można chronić maszyny wirtualne?
Nie. Maszyny wirtualne muszą znajdować się na serwerze hosta funkcji Hyper-V, który jest uruchomiony na serwerze z obsługiwanym systemem Windows. Jeśli musisz chronić komputer kliencki, możesz replikować go jako maszynę fizyczną do [Azure](site-recovery-vmware-to-azure.md) lub [dodatkowego centrum danych](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Jakie obciążenia mogę chronić za pomocą usługi Site Recovery?
Usługa Site Recovery umożliwia ochronę większości obciążeń działających na obsługiwanej maszynie Wirtualnej lub serwera fizycznego. Usługa Site Recovery umożliwia replikację z uwzględnieniem aplikacji, dzięki czemu aplikacje mogą być odzyskiwane do inteligentnego stanu. Integruje się z aplikacjami firmy Microsoft, takich jak SharePoint, Exchange, Dynamics, SQL Server i usługi Active Directory i ściśle współpracuje z wiodącymi dostawcami oprogramowania, w tym Oracle, SAP, IBM i Red Hat. [Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Czy hosty funkcji Hyper-V muszą znajdować się w chmurach programu VMM?
Jeśli chcesz replikować do dodatkowego centrum danych, a następnie maszyn wirtualnych funkcji Hyper-V muszą znajdować się na funkcji Hyper-V hostuje serwery znajdujące się w chmurze programu VMM. Można replikować do platformy Azure, można replikować maszyny wirtualne, z lub bez niej chmur programu VMM. [Dowiedz się więcej](tutorial-hyper-v-to-azure.md) dotyczących replikacji funkcji Hyper-V do platformy Azure.

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Czy mogę wdrożyć usługę Site Recovery z programem VMM, jeśli mam tylko jeden serwer VMM?

Tak. Można replikować maszyny wirtualne na serwerach funkcji Hyper-V w chmurze programu VMM do platformy Azure lub replikować je między chmurami programu VMM na tym samym serwerze. Dla środowiska lokalnego do replikacji lokalnej zaleca się posiadanie serwera VMM w lokacjach głównych i dodatkowych.  

### <a name="what-physical-servers-can-i-protect"></a>Jakie serwery fizyczne mogę chronić?
Teraz można replikować serwery fizyczne z systemem Windows i Linux na platformie Azure lub lokacji dodatkowej. Dowiedz się więcej o wymaganiach dotyczących [replikacji do platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines), i [replikacji do lokacji dodatkowej](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


Należy pamiętać, że fizycznych serwerów będzie działać jako maszyny wirtualne na platformie Azure, jeśli awaria usługi na serwerze lokalnym. Powrót po awarii do środowiska lokalnego serwera fizycznego nie jest obecnie obsługiwane. W przypadku maszyny chronione jako fizyczny można tylko powrót po awarii do maszyny wirtualnej VMware.

### <a name="what-vmware-vms-can-i-protect"></a>Jakie maszyny wirtualne VMware mogę chronić?

Aby chronić maszyny wirtualne VMware, należy posiadać program vSphere Hypervisor i maszyny wirtualne z uruchomionymi narzędziami VMware. Zalecamy również posiadanie serwera VMware vCenter do zarządzania funkcjami hypervisor. Dowiedz się więcej o wymaganiach dotyczących [replikacji do platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines), lub [replikacji do lokacji dodatkowej](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Czy mogę zarządzać odzyskiwaniem po awarii dla oddziałów firmy przy użyciu usługi Site Recovery?
Tak. Podczas używania usługi Site Recovery do organizowania replikacji i trybu failover w biurach oddziałów, uzyskasz ujednoliconego aranżacji i Wyświetl wszystkie obciążenia oddziałów w centralnej lokalizacji. Możesz z łatwością uruchomić tryb failover i zarządzać odzyskiwaniem po awarii wszystkich oddziałów z siedziby, bez konieczności odwiedzania oddziałów.

## <a name="pricing"></a>Cennik
Ceny pytań, można znaleźć na stronie często zadawane pytania pod [cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

## <a name="security"></a>Bezpieczeństwo
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?
Nie, Usługa Site Recovery nie przechwytuje replikowanych danych, nie ma żadnych informacji na temat co działa na maszynach wirtualnych lub serwerach fizycznych.
Dane replikacji są wymieniane między lokalnymi hostami funkcji Hyper-V, funkcjami hypervisor VMware lub serwerami fizycznymi i usługą Azure Storage lub lokacją dodatkową. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Usługa Site Recovery jest ISO 27001: 2013, 27018, HIPAA, DPA certyfikowane i jest w trakcie SOC2 i FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>W celu zachowania zgodności nawet nasze metadanych w środowisku lokalnym musi zawierać się w tym samym regionie geograficznym. Usługa Site Recovery, ułatwisz nam?
Tak. Po utworzeniu magazynu usługi Site Recovery w regionie, Upewniamy się, że wszystkie metadane, którego potrzebujemy do uruchamiania i organizowania replikacji i trybu failover pozostaje w tym regionie użytkownika geograficzne granic.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Dla maszyn wirtualnych i serwerów fizycznych replikowanych między lokalnych witryn szyfrowania przesyłanych w jest obsługiwane. Dla maszyn wirtualnych i serwerów fizycznych replikowanych do platformy Azure, zarówno szyfrowanie przesyłanych w i [szyfrowanie w spoczynku (na platformie Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) są obsługiwane.

## <a name="replication"></a>Replikacja

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Można replikować za pośrednictwem sieci VPN lokacja lokacja na platformie Azure?
Usługa Azure Site Recovery replikuje dane do konta usługi Azure storage lub dyski zarządzane za pośrednictwem publicznego punktu końcowego. Replikacja nie jest za pośrednictwem sieci VPN lokacja lokacja. Można utworzyć sieci VPN lokacja lokacja z siecią wirtualną platformy Azure. To nie koliduje z replikacji usługi Site Recovery.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Replikacja maszyn wirtualnych na platformie Azure mogą używać usługi ExpressRoute?
Tak, [można użyć usługi ExpressRoute](concepts-expressroute-with-site-recovery.md) replikowania lokalnych maszyn wirtualnych do platformy Azure. Usługa Azure Site Recovery replikuje dane do usługi Azure Storage za pośrednictwem publicznego punktu końcowego. Należy skonfigurować [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#publicpeering) lub [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) za pomocą usługi ExpressRoute dla replikacji usługi Site Recovery. Komunikacja równorzędna firmy Microsoft jest zalecanym domen routingu replikacji. Po maszyny wirtualne mają zostały przełączone w tryb failover siecią wirtualną platformy Azure można z nich korzystać przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering) konfiguracji sieci wirtualnej platformy Azure. Replikacja nie jest obsługiwana za pośrednictwem prywatnej komunikacji równorzędnej. W przypadku, gdy są chronione maszyn VMware lub fizycznych komputerów, upewnij się, że [wymagania sieciowe](vmware-azure-configuration-server-requirements.md#network-requirements) spełnione są również do replikacji. 

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Czy istnieją wymagania wstępne związane z replikacją maszyn wirtualnych do platformy Azure?
[Maszyny wirtualne VMware](vmware-physical-azure-support-matrix.md#replicated-machines) i [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) ma zostać zreplikowana na platformie Azure, powinny być zgodne z wymaganiami platformy Azure.

Twoje konto platformy Azure użytkownika musi mieć pewne [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) Aby włączyć replikację nowej maszyny wirtualnej na platformie Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Czy można replikować maszyny wirtualne funkcji Hyper-V drugiej generacji do platformy Azure?
Tak. Usługa Site Recovery konwertuje z generacji 2 do generacji 1, podczas trybu failover. W przypadku powrotu po awarii maszyny jest konwertowana do generacji 2. [Dowiedz się więcej](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>W przypadku replikacji do platformy Azure, jak będę płacić za maszyny wirtualne Azure?
Podczas regularnej replikacji dane są replikowane do magazynu geograficznie nadmiarowego Azure i nie trzeba płacić żadnych opłat maszyny wirtualnej IaaS platformy Azure, zapewniając znaczącą korzyścią. Po uruchomieniu trybu failover na platformie Azure usługa Site Recovery automatycznie utworzy maszyny wirtualne Azure w modelu IaaS. Po tym zostaną naliczone opłaty za zasoby obliczeniowe, które zostaną zużyte na platformie Azure.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Mogę zautomatyzować usługę Site Recovery scenariuszy przy użyciu zestawu SDK?
Tak. Przepływy pracy usługi Site Recovery można zautomatyzować przy użyciu interfejsu API REST, programu PowerShell lub zestawu SDK platformy Azure. Aktualnie obsługiwanych scenariuszach wdrażania usługi Site Recovery przy użyciu programu PowerShell:

* [Replikowanie maszyn wirtualnych funkcji Hyper-V na platformie chmury VMMs do usługi Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replikacja maszyn wirtualnych funkcji Hyper-V, bez programu VMM do usługi Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replikacja VMware do platformy Azure przy użyciu programu PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Jeśli przeprowadzam replikację do platformy Azure, jakiego rodzaju konta magazynu i dysku zarządzanego potrzebuję?
Potrzebny jest magazyn LRS lub GRS. Zalecamy użycie konta GRS, dzięki czemu dane będą odporne w przypadku regionalnej awarii lub sytuacji, w której nie można odzyskać regionu podstawowego. Konto musi znajdować się w tym samym regionie co magazyn Usług odzyskiwania. Usługa Premium storage jest obsługiwane dla maszyny Wirtualnej VMware, maszyn wirtualnych funkcji Hyper-V i replikacji serwera fizycznego, gdy należy wdrożyć usługę Site Recovery w witrynie Azure portal. Dyski zarządzane obsługują tylko magazyn LRS.

### <a name="how-often-can-i-replicate-data"></a>Jak często mogę replikować dane?
* **Funkcja Hyper-V:** Maszyny wirtualne funkcji Hyper-V mogą być replikowane co 30 sekund (z wyjątkiem usługi premium storage), 5 minut lub 15 minut. Jeśli po skonfigurowaniu replikacji sieci SAN replikacja jest synchroniczne.
* **Maszyny wirtualne platformy Azure, VMware i serwery fizyczne:** Częstotliwość replikacji nie jest tutaj istotna. Replikacja jest ciągły.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Czy mogę rozszerzyć replikację z istniejącej lokacji odzyskiwania do innej lokacji trzeciorzędna
Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Zażądać tej funkcji w [forum z opiniami](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Czy mogę przeprowadzić replikację w trybie offline podczas pierwszej replikacji do platformy Azure?
Ta funkcja nie jest obsługiwana. Zażądać tej funkcji w [forum z opiniami](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Czy z replikacji można wykluczyć określone dyski?
Jest to obsługiwane w przypadku replikacji maszyn wirtualnych funkcji Hyper-V i maszyn wirtualnych VMware na platformę Azure przy użyciu witryny Azure portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Czy można replikować maszyny wirtualne z dyskami dynamicznymi?
Dyski dynamiczne są obsługiwane w przypadku replikacji maszyn wirtualnych funkcji Hyper-V. Są one również obsługiwane podczas replikowania maszyn fizycznych i maszyn wirtualnych VMware na platformę Azure. Dysk systemu operacyjnego musi być dyskiem podstawowym.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Można dodać nowego komputera do istniejącej grupy replikacji?
Dodawanie nowych maszyn do istniejącej grupy replikacji jest obsługiwane. Aby to zrobić, wybierz grupę replikacji (z bloku "Zreplikowany elementy") i kliknij prawym przyciskiem myszy wybierz menu kontekstowe dla grupy replikacji, a następnie wybierz odpowiednią opcję.

![Dodaj do grupy replikacji](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Czy mogę ograniczyć przepustowość przydzieloną dla ruchu replikacji funkcji Hyper-V?
Tak. Możesz dowiedzieć się więcej o ograniczanie przepustowości w artykułach wdrożenia:

* [Planowanie wydajności na potrzeby replikacji maszyn wirtualnych VMware i serwerów fizycznych](site-recovery-plan-capacity-vmware.md)
* [Planowanie wydajności na potrzeby replikowania maszyn wirtualnych funkcji Hyper-V do platformy Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>Tryb failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Jeśli jestem przełączanie awaryjne na platformie Azure, jak uzyskać dostęp do maszyn wirtualnych platformy Azure po włączeniu trybu failover?
Maszyny wirtualne Azure są dostępne za pośrednictwem bezpiecznego połączenia internetowego, sieci VPN między lokacjami lub za pośrednictwem usługi Azure ExpressRoute. Należy przygotować kilka rzeczy w celu nawiązania połączenia. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>W przypadku awarii przełączyć się do platformy Azure, w jaki sposób platforma Azure upewnia się, że moje dane są odporne?
Platforma Azure została zaprojektowana z myślą o odporności danych. Usługa Site Recovery już został zaprojektowany do trybu failover do pomocniczego centrum danych platformy Azure, zgodnie z umową SLA platformy Azure, jeśli zajdzie taka potrzeba. W takim przypadku możemy zagwarantować metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla magazynu.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Jeśli Przeprowadzam replikację między dwoma centrami danych co się stanie, jeśli wystąpi nieoczekiwana awaria Moje podstawowym centrum danych?
Możesz wyzwolić nieplanowany tryb failover z lokacji dodatkowej. Usługa Site Recovery nie wymaga łączności z lokacją główną do pracy w trybie failover.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?
Tryb failover nie jest automatyczny. Zainicjuj tryb failover z jednym kliknięciem w portalu lub użyć [odzyskiwania lokacji w programie PowerShell](/powershell/module/az.recoveryservices) do wyzwolenia przejścia w tryb failover. Powrotem jest prostej akcji w portalu usługi Site Recovery.

Możesz zautomatyzować można użyć programu Orchestrator w środowisku lokalnym lub programu Operations Manager, aby wykryć awarię maszyny wirtualnej, a następnie wyzwolić tryb failover przy użyciu zestawu SDK.

* [Dowiedz się więcej](site-recovery-create-recovery-plans.md) informacje o planach odzyskiwania.
* [Dowiedz się więcej](site-recovery-failover.md) informacje o trybie failover.
* [Dowiedz się więcej](site-recovery-failback-azure-to-vmware.md) dotyczące niepowodzenia tworzenia kopii maszyn wirtualnych VMware i serwerów fizycznych

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Jeśli Moje hosta lokalnego nie jest nieodpowiadający lub które uległy awarii, czy można trybu failover do innego hosta?
Tak, umożliwia odzyskiwanie do lokalizacji alternatywnej powrotu po awarii do innego hosta z platformy Azure. Dowiedz się więcej o opcjach dostępnych w poniższych łączy dla maszyn wirtualnych VMware i funkcji Hyper-V.

* [W przypadku maszyn wirtualnych VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [W przypadku maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-failback.md#perform-failback)

## <a name="service-providers"></a>Dostawcy usług
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jestem usługodawcy. Usługa Site Recovery działa dla modeli opartych na infrastrukturze dedykowanej i współdzielonej?
Tak. Usługa Site Recovery obsługuje modele oparte na infrastrukturze dedykowanej i współdzielonej.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Dla dostawcy usług jest tożsamość mojej dzierżawy jest udostępniana usłudze Site Recovery?
Nie. Tożsamość dzierżawy pozostają anonimowe. Twoje dzierżawy nie wymagają dostępu do portalu usługi Site Recovery. Tylko administrator dostawcy usług wykonuje czynności w portalu.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Dane aplikacji dzierżawy nigdy nie przejdzie do platformy Azure?
Podczas replikacji między lokacjami należącymi do dostawcy usług dane aplikacji nigdy nie trafiają do platformy Azure. Dane są szyfrowane podczas przesyłania i replikowane bezpośrednio między lokacjami dostawcy usług.

Jeśli przeprowadzasz replikację do platformy Azure, dane aplikacji są wysyłane do usługi Azure Storage, ale nie do usługi Site Recovery. Dane są szyfrowane podczas przesyłania i pozostają zaszyfrowane na platformie Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Czy moje dzierżawy otrzymają rachunek za jakiekolwiek usługi platformy Azure?
Nie. Relacja rozliczeniowa platformy Azure jest nawiązana bezpośrednio z dostawcą usług. Dostawcy usług są odpowiedzialni za generowanie konkretnych rachunków dla swoich dzierżaw.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Jeśli przeprowadzam replikację do platformy Azure, czy moje maszyny wirtualne muszą być uruchomione na platformie Azure przez cały czas?
Nie, dane są replikowane do usługi Azure storage w ramach subskrypcji. Podczas testowego (test odzyskiwania po awarii) lub rzeczywistego uruchamiania trybu failover usługa Site Recovery automatycznie tworzy maszyny wirtualne w ramach subskrypcji.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Czy podczas replikacji do platformy Azure jest zapewniana izolacja na poziomie dzierżawy?
Tak.

### <a name="what-platforms-do-you-currently-support"></a>Jakie platformy są obecnie obsługiwane?
Obsługujemy pakietu Azure Pack, systemie Cloud Platform System i programu System Center na podstawie wdrożenia (wersja 2012 lub nowsza). [Dowiedz się więcej](https://technet.microsoft.com/library/dn850370.aspx) informacji na temat integracji pakietu Azure Pack i Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Czy są obsługiwane wdrożenia oparte na jednym pakiecie Azure Pack i jednym serwerze VMM?
Tak, można replikować maszyny wirtualne funkcji Hyper-V do platformy Azure lub między lokacjami dostawcy usług.  Należy pamiętać, że Jeśli replikujesz między lokacjami dostawcy usług, Integracja elementów runbook platformy Azure nie jest dostępna.

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z [omówieniem usługi Site Recovery](site-recovery-overview.md)
* Dowiedz się więcej o [architekturze usługi Site Recovery](site-recovery-components.md)  
