---
title: 'Usługa Azure Site Recovery: Często zadawane pytania | Dokumentacja firmy Microsoft'
description: W tym artykule omówiono często zadawane pytania dotyczące usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/08/2019
ms.author: raynew
ms.openlocfilehash: 74ccc76ff139cae21e3583b0fea11596f5fd6b62
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413925"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Usługa Azure Site Recovery: często zadawane pytania (FAQ)
Ten artykuł zawiera podsumowanie często zadawane pytania dotyczące usługi Azure Site Recovery.</br>
Określone zapytań na różne usługi ASR scenariuszy można znaleźć w scenariuszu określonych — często zadawane pytania.<br>

- [Odzyskiwanie po awarii maszyn wirtualnych platformy Azure do platformy Azure](azure-to-azure-common-questions.md)
- [Odzyskiwanie po awarii maszyn wirtualnych programu VMware do platformy Azure](vmware-azure-common-questions.md)
- [Odzyskiwanie po awarii maszyn wirtualnych funkcji Hyper-V do platformy Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Ogólne

### <a name="what-does-site-recovery-do"></a>Do czego służy usługa Site Recovery?
Usługa Site Recovery przyczynia się do ciągłości działania i strategia odzyskiwania (BCDR), poprzez organizowanie i automatyzowanie replikacji maszyn wirtualnych platformy Azure między regionami, lokalnych maszyn wirtualnych i serwerów fizycznych na platformę Azure i maszyn lokalnych dodatkowego centrum danych. [Dowiedz się więcej](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Czy mogę chronić maszynę wirtualną zawierającą dysk z platformy Docker

Nie, jest to nieobsługiwany scenariusz.

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

## <a name="pricing"></a>Cennik

### <a name="where-can-i-find-pricing-information"></a>Gdzie mogę znaleźć informacje o cenach?
Przegląd [cenach usługi Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) szczegółowe informacje.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Jak obliczyć przybliżoną opłaty za podczas stosowania Site Recovery

Możesz użyć [Kalkulator cen](https://aka.ms/asr_pricing_calculator) do szacowania kosztów przy użyciu usługi Site Recovery.

Aby uzyskać szczegółową prognozę kosztów, uruchom narzędzie planista wdrażania na potrzeby [VMware](https://aka.ms/siterecovery_deployment_planner) lub [funkcji Hyper-V](https://aka.ms/asr-deployment-planner)i użyj [raport szacowania kosztów](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Dyski zarządzane są umożliwia replikowanie maszyn wirtualnych VMware i serwerów fizycznych. Czy są naliczane dodatkowe opłaty za konto magazynu pamięci podręcznej w przypadku dysków zarządzanych?

Nie istnieją żadne dodatkowe opłaty dla pamięci podręcznej. W przypadku replikacji konta magazynu w warstwie standardowa to pamięć podręczna jest częścią tego samego konta magazynu docelowego.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Jestem użytkownikiem usługi Azure Site Recovery od ponad miesiąca. Czy nadal uzyskam pierwsze 31 bezpłatnych dni dla każdego chronionego wystąpienia?

Tak. Przez pierwsze 31 dni opłaty usługi Azure Site Recovery dla każdego chronionego wystąpienia nie są naliczane. Na przykład jeśli ochronie 10 wystąpień przez ostatnie 6 miesięcy a 11 wystąpienie usługą Azure Site Recovery, istnieją nie opłaty za wystąpienie 11 przez pierwsze 31 dni. Pierwsze 10 wystąpień będą nadal naliczane opłaty za usługę Azure Site Recovery, ponieważ zostały one chronione przez dłużej niż 31 dni.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Czy podczas pierwszych 31 dni będą naliczane inne opłaty usługi Azure?

Tak, nawet jeśli usługa Site Recovery będzie bezpłatna przez pierwsze 31 dni dla chronionego wystąpienia, mogą zostać naliczone opłaty dla usługi Azure Storage, transakcje magazynowe i transfer danych. Opłaty za zasoby obliczeniowe platformy Azure mogą zostać również naliczone dla odzyskanej maszyny wirtualnej.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Wiążą się do awarii i testowania odzyskiwania po awarii w tryb failover?

Istnieje nie oddzielnych koszt testowania odzyskiwania po awarii. Nastąpi opłaty za zasoby obliczeniowe, po utworzeniu maszyny Wirtualnej po testowy tryb failover.



## <a name="security"></a>Bezpieczeństwo

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?
Nie, Usługa Site Recovery nie przechwytuje replikowanych danych, nie ma żadnych informacji na temat co działa na maszynach wirtualnych lub serwerach fizycznych.
Dane replikacji są wymieniane między lokalnymi hostami funkcji Hyper-V, funkcjami hypervisor VMware lub serwerami fizycznymi i usługą Azure Storage lub lokacją dodatkową. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Usługa Site Recovery jest ISO 27001: 2013, 27018, HIPAA, DPA certyfikowane i jest w trakcie SOC2 i FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>W celu zachowania zgodności nawet nasze metadanych w środowisku lokalnym musi zawierać się w tym samym regionie geograficznym. Usługa Site Recovery, ułatwisz nam?
Tak. Po utworzeniu magazynu usługi Site Recovery w regionie, Upewniamy się, że wszystkie metadane, którego potrzebujemy do uruchamiania i organizowania replikacji i trybu failover pozostaje w tym regionie użytkownika geograficzne granic.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Dla maszyn wirtualnych i serwerów fizycznych replikowanych między lokalnych witryn szyfrowania przesyłanych w jest obsługiwane. Dla maszyn wirtualnych i serwerów fizycznych replikowanych do platformy Azure, zarówno szyfrowanie przesyłanych w i [szyfrowanie w spoczynku (na platformie Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) są obsługiwane.




## <a name="disaster-recovery"></a>Odzyskiwanie po awarii

### <a name="what-can-site-recovery-protect"></a>Co może chronić Usługa Site Recovery?
* **Maszyny wirtualne platformy Azure**: Usługa Site Recovery może replikować dowolne obciążenia uruchomione na obsługiwanej maszynie Wirtualnej platformy Azure
* **Maszyny wirtualne funkcji Hyper-V**: Usługa Site Recovery może chronić dowolne obciążenia uruchomione na maszynie Wirtualnej funkcji Hyper-V.
* **Serwery fizyczne**: Usługa Site Recovery może chronić serwery fizyczne z systemem Windows lub Linux.
* **Maszyny wirtualne VMware**: Usługa Site Recovery może chronić dowolne obciążenia uruchomione na maszynie wirtualnej VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Jakie obciążenia mogę chronić za pomocą usługi Site Recovery?
Usługa Site Recovery umożliwia ochronę większości obciążeń działających na obsługiwanej maszynie Wirtualnej lub serwera fizycznego. Usługa Site Recovery umożliwia replikację z uwzględnieniem aplikacji, dzięki czemu aplikacje mogą być odzyskiwane do inteligentnego stanu. Integruje się z aplikacjami firmy Microsoft, takich jak SharePoint, Exchange, Dynamics, SQL Server i usługi Active Directory i ściśle współpracuje z wiodącymi dostawcami oprogramowania, w tym Oracle, SAP, IBM i Red Hat. [Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Czy mogę zarządzać odzyskiwaniem po awarii dla oddziałów firmy przy użyciu usługi Site Recovery?
Tak. Podczas używania usługi Site Recovery do organizowania replikacji i trybu failover w biurach oddziałów, uzyskasz ujednoliconego aranżacji i Wyświetl wszystkie obciążenia oddziałów w centralnej lokalizacji. Możesz z łatwością uruchomić tryb failover i zarządzać odzyskiwaniem po awarii wszystkich oddziałów z siedziby, bez konieczności odwiedzania oddziałów.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Odzyskiwanie po awarii jest obsługiwane dla maszyn wirtualnych platformy Azure?

Tak, Usługa Site Recovery obsługuje po awarii dla maszyn wirtualnych platformy Azure między regionami platformy Azure. [Przejrzyj często zadawane pytania dotyczące](azure-to-azure-common-questions.md) temat odzyskiwania po awarii maszyny Wirtualnej platformy Azure.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Odzyskiwanie po awarii jest obsługiwane dla maszyn wirtualnych VMware?

Tak, Usługa Site Recovery obsługuje odzyskiwanie po awarii lokalnych maszyn wirtualnych programu VMware. [Przejrzyj często zadawane pytania dotyczące](vmware-azure-common-questions.md) do odzyskiwania po awarii maszyn wirtualnych programu VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Odzyskiwanie po awarii jest obsługiwane dla maszyn wirtualnych funkcji Hyper-V?
Tak, Usługa Site Recovery obsługuje odzyskiwanie po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V. [Przejrzyj często zadawane pytania dotyczące](hyper-v-azure-common-questions.md) do odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Odzyskiwanie po awarii jest obsługiwane dla serwerów fizycznych?
Tak, Usługa Site Recovery obsługuje odzyskiwanie po awarii lokalnych fizycznych serwerów z systemem Windows i Linux na platformie Azure lub lokacji dodatkowej. Dowiedz się więcej o wymaganiach dotyczących odzyskiwania po awarii na [Azure](vmware-physical-azure-support-matrix.md#replicated-machines), a[lokacji dodatkowej](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Należy pamiętać, że fizycznych serwerów będzie działać jako maszyny wirtualne na platformie Azure po włączeniu trybu failover. Powrót po awarii z platformy Azure do serwera fizycznego w środowisku lokalnym nie jest obecnie obsługiwane. Można tylko powrotu po awarii do maszyny wirtualnej VMware.





## <a name="replication"></a>Replikacja

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Można replikować za pośrednictwem sieci VPN lokacja lokacja na platformie Azure?
Usługa Azure Site Recovery replikuje dane do konta usługi Azure storage lub dyski zarządzane za pośrednictwem publicznego punktu końcowego. Replikacja nie jest za pośrednictwem sieci VPN lokacja lokacja. 

### <a name="why-cant-i-replicate-over-vpn"></a>Dlaczego nie można replikować za pośrednictwem sieci VPN?

W przypadku replikacji na platformie Azure ruch związany z replikacją osiągnie publicznych punktów końcowych usługi Azure Storage. Dlatego tylko można replikować za pośrednictwem publicznej sieci internet przy użyciu usługi ExpressRoute (publicznej komunikacji równorzędnej) i sieci VPN nie działa.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Czy można używać Riverbed SteelHeads w przypadku replikacji?

Nasz partner Riverbed, zawiera szczegółowe wskazówki na temat pracy z usługą Azure Site Recovery. Przejrzyj ich [Przewodnik po rozwiązaniu](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Replikacja maszyn wirtualnych na platformie Azure mogą używać usługi ExpressRoute?
Tak, [można użyć usługi ExpressRoute](concepts-expressroute-with-site-recovery.md) replikowania lokalnych maszyn wirtualnych do platformy Azure.

- Usługa Azure Site Recovery replikuje dane do usługi Azure Storage za pośrednictwem publicznego punktu końcowego. Należy skonfigurować [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#publicpeering) lub [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) za pomocą usługi ExpressRoute dla replikacji usługi Site Recovery.
- Komunikacja równorzędna firmy Microsoft jest zalecanym domen routingu replikacji.
- Po maszyny wirtualne mają zostały przełączone w tryb failover siecią wirtualną platformy Azure można z nich korzystać przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering) konfiguracji sieci wirtualnej platformy Azure.
- Replikacja nie jest obsługiwana za pośrednictwem prywatnej komunikacji równorzędnej.
- Jeśli w przypadku ochrony maszyn VMware i fizycznych komputerów, upewnij się, że serwer konfiguracji jest zgodny z [wymagania sieciowe](vmware-azure-configuration-server-requirements.md#network-requirements) replikacji. 



### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Jeśli przeprowadzam replikację do platformy Azure, jakiego rodzaju konta magazynu i dysku zarządzanego potrzebuję?

Potrzebny jest magazyn LRS lub GRS. Zalecamy użycie konta GRS, dzięki czemu dane będą odporne w przypadku regionalnej awarii lub sytuacji, w której nie można odzyskać regionu podstawowego. Konto musi znajdować się w tym samym regionie co magazyn Usług odzyskiwania. Usługa Premium storage jest obsługiwane dla maszyny Wirtualnej VMware, maszyn wirtualnych funkcji Hyper-V i replikacji serwera fizycznego, gdy należy wdrożyć usługę Site Recovery w witrynie Azure portal. Dyski zarządzane obsługują tylko magazyn LRS.

### <a name="how-often-can-i-replicate-data"></a>Jak często mogę replikować dane?
* **Funkcja Hyper-V:** Maszyny wirtualne funkcji Hyper-V mogą być replikowane co pięć minut, o 30 sekund (z wyjątkiem usługi premium storage)
* **Azure maszyny wirtualne, maszyny wirtualne VMware, serwery fizyczne:** Częstotliwość replikacji nie jest tutaj istotna. Replikacja jest ciągły.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Czy mogę rozszerzyć replikację z istniejącej lokacji odzyskiwania do innej lokacji trzeciorzędna
Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Zażądać tej funkcji w [forum z opiniami](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Czy mogę przeprowadzić replikację w trybie offline podczas pierwszej replikacji do platformy Azure?
Ta funkcja nie jest obsługiwana. Zażądać tej funkcji w [forum z opiniami](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Czy z replikacji można wykluczyć określone dyski?
Jest to obsługiwane w przypadku replikacji maszyn wirtualnych funkcji Hyper-V i maszyn wirtualnych VMware na platformę Azure przy użyciu witryny Azure portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Czy można replikować maszyny wirtualne z dyskami dynamicznymi?
Dyski dynamiczne są obsługiwane podczas replikowania maszyn wirtualnych funkcji Hyper-V, a podczas replikowania maszyn fizycznych i maszyn wirtualnych VMware na platformę Azure. Dysk systemu operacyjnego musi być dyskiem podstawowym.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Czy mogę ograniczyć przepustowość przydzieloną dla ruchu związanego z replikacją
Tak. Możesz dowiedzieć się więcej o ograniczanie przepustowości w następujących artykułach:

* [Planowanie wydajności na potrzeby replikacji maszyn wirtualnych VMware i serwerów fizycznych](site-recovery-plan-capacity-vmware.md)
* [Planowanie wydajności na potrzeby replikowania maszyn wirtualnych funkcji Hyper-V do platformy Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Tryb failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Jeśli jestem przełączanie awaryjne na platformie Azure, jak uzyskać dostęp do maszyn wirtualnych platformy Azure po włączeniu trybu failover?

Maszyny wirtualne Azure są dostępne za pośrednictwem bezpiecznego połączenia internetowego, sieci VPN między lokacjami lub za pośrednictwem usługi Azure ExpressRoute. Należy przygotować kilka rzeczy w celu nawiązania połączenia. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>W przypadku awarii przełączyć się do platformy Azure, w jaki sposób platforma Azure upewnia się, że moje dane są odporne?
Platforma Azure została zaprojektowana z myślą o odporności danych. Usługa Site Recovery już został zaprojektowany do trybu failover do pomocniczego centrum danych platformy Azure, zgodnie z umową SLA platformy Azure. W takim przypadku możemy zagwarantować metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla magazynu.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Jeśli Przeprowadzam replikację między dwoma centrami danych co się stanie, jeśli wystąpi nieoczekiwana awaria Moje podstawowym centrum danych?
Możesz wyzwolić nieplanowany tryb failover z lokacji dodatkowej. Usługa Site Recovery nie wymaga łączności z lokacją główną do pracy w trybie failover.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?
Tryb failover nie jest automatyczny. Zainicjuj tryb failover z jednym kliknięciem w portalu lub użyć [odzyskiwania lokacji w programie PowerShell](/powershell/module/az.recoveryservices) do wyzwolenia przejścia w tryb failover. Powrotem jest prostej akcji w portalu usługi Site Recovery.

Możesz zautomatyzować można użyć programu Orchestrator w środowisku lokalnym lub programu Operations Manager, aby wykryć awarię maszyny wirtualnej, a następnie wyzwolić tryb failover przy użyciu zestawu SDK.

* [Dowiedz się więcej](site-recovery-create-recovery-plans.md) informacje o planach odzyskiwania.
* [Dowiedz się więcej](site-recovery-failover.md) informacje o trybie failover.
* [Dowiedz się więcej](site-recovery-failback-azure-to-vmware.md) dotyczące niepowodzenia tworzenia kopii maszyn wirtualnych VMware i serwerów fizycznych

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Jeśli Moje hosta lokalnego nie odpowiada lub uległ awarii, można można powracać po awarii do innego hosta?
Tak, umożliwia odzyskiwanie do lokalizacji alternatywnej powrotu po awarii do innego hosta z platformy Azure.

* [W przypadku maszyn wirtualnych VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [W przypadku maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-failback.md#perform-failback)

## <a name="automation"></a>Automatyzacja

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Mogę zautomatyzować usługę Site Recovery scenariuszy przy użyciu zestawu SDK?
Tak. Przepływy pracy usługi Site Recovery można zautomatyzować przy użyciu interfejsu API REST, programu PowerShell lub zestawu SDK platformy Azure. Aktualnie obsługiwanych scenariuszach wdrażania usługi Site Recovery przy użyciu programu PowerShell:

* [Replikowanie maszyn wirtualnych funkcji Hyper-V na platformie chmury VMMs do usługi Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replikacja maszyn wirtualnych funkcji Hyper-V, bez programu VMM do usługi Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replikacja VMware do platformy Azure przy użyciu programu PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Składnik/dostawca uaktualnienia

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Gdzie mogę znaleźć pakiety zbiorcze aktualizacji/na informacje o wersji uaktualnień Site Recovery

[Dowiedz się,](site-recovery-whats-new.md) o nowych aktualizacjach i [uzyskać informacje dotyczące pakietu zbiorczego](service-updates-how-to.md).

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z [omówieniem usługi Site Recovery](site-recovery-overview.md)

