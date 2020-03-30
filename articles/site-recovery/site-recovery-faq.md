---
title: Ogólne pytania dotyczące usługi Azure Site Recovery
description: W tym artykule omówiono popularne ogólne pytania dotyczące usługi Azure Site Recovery.
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: a9d0ae4a6e60a72bbb1148aca1a75c44506b2e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257682"
---
# <a name="general-questions-about-azure-site-recovery"></a>Ogólne pytania dotyczące usługi Azure Site Recovery

W tym artykule podsumowano często zadawane pytania dotyczące usługi Azure Site Recovery. W przypadku konkretnych scenariuszy zapoznaj się z tymi artykułami

- [Pytania dotyczące odzyskiwania po awarii maszyny Wirtualnej platformy Azure na platformie Azure](azure-to-azure-common-questions.md)
- [Pytania dotyczące odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure](vmware-azure-common-questions.md)
- [Pytania dotyczące odzyskiwania po awarii maszyny Wirtualnej funkcji Hyper V na platformie Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Ogólne

### <a name="what-does-site-recovery-do"></a>Do czego służy usługa Site Recovery?
Usługa Site Recovery przyczynia się do zapewnienia ciągłości działania i odzyskiwania po awarii (BCDR), organizując i automatyzując replikację maszyn wirtualnych platformy Azure między regionami, lokalnymi maszynami wirtualnymi i serwerami fizycznymi na platformie Azure oraz na komputerach lokalnych w celu dodatkowego centrum danych. [Dowiedz się więcej](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Czy mogę chronić maszynę wirtualną, która ma dysk platformy Docker?

Nie, jest to scenariusz nieobsługiwał.

## <a name="service-providers"></a>Usługodawców

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jestem usługodawcą. Czy odzysk witryny działa w przypadku modeli infrastruktury dedykowanej i współdzielonej?
Tak. Usługa Site Recovery obsługuje modele oparte na infrastrukturze dedykowanej i współdzielonej.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Czy dla dostawcy usług tożsamość dzierżawcy jest współużytkowana usłudze Site Recovery?
Nie. Tożsamość dzierżawy pozostaje anonimowa. Twoje dzierżawy nie wymagają dostępu do portalu usługi Site Recovery. Tylko administrator dostawcy usług wykonuje czynności w portalu.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Czy dane aplikacji dzierżawy kiedykolwiek przejść do platformy Azure?
Podczas replikacji między lokacjami należącymi do dostawcy usług dane aplikacji nigdy nie trafiają do platformy Azure. Dane są szyfrowane podczas przesyłania i replikowane bezpośrednio między lokacjami usługodawców.

Jeśli przeprowadzasz replikację do platformy Azure, dane aplikacji są wysyłane do usługi Azure Storage, ale nie do usługi Site Recovery. Dane są szyfrowane podczas przesyłania i pozostają zaszyfrowane na platformie Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Czy moje dzierżawy otrzymają rachunek za jakiekolwiek usługi platformy Azure?
Nie. Relacja rozliczeniowa platformy Azure jest nawiązana bezpośrednio z dostawcą usług. Dostawcy usług są odpowiedzialni za generowanie konkretnych rachunków dla swoich dzierżaw.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Jeśli przeprowadzam replikację do platformy Azure, czy moje maszyny wirtualne muszą być uruchomione na platformie Azure przez cały czas?
Nie, dane są replikowane do magazynu platformy Azure w ramach subskrypcji. Podczas testowego (test odzyskiwania po awarii) lub rzeczywistego uruchamiania trybu failover usługa Site Recovery automatycznie tworzy maszyny wirtualne w ramach subskrypcji.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Czy podczas replikacji do platformy Azure jest zapewniana izolacja na poziomie dzierżawy?
Tak.

### <a name="what-platforms-do-you-currently-support"></a>Jakie platformy są obecnie obsługiwane?
Obsługujemy wdrożenia oparte na pakiecie Azure Pack, systemie platformy Cloud Platform system i usłudze System Center (2012 i nowszych). [Dowiedz się więcej](https://technet.microsoft.com/library/dn850370.aspx) o pakiecie Azure Pack i integracji usługi Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Czy są obsługiwane wdrożenia oparte na jednym pakiecie Azure Pack i jednym serwerze VMM?
Tak, można replikować maszyny wirtualne funkcji Hyper-V na platformie Azure lub między lokacjami dostawców usług.  Należy zauważyć, że w przypadku replikacji między lokacjami dostawców usług integracja z programem Runbook platformy Azure nie jest dostępna.

## <a name="pricing"></a>Cennik

### <a name="where-can-i-find-pricing-information"></a>Gdzie mogę znaleźć informacje o cenach?
Przejrzyj szczegóły [dotyczące cen usługi Site Recovery.](https://azure.microsoft.com/pricing/details/site-recovery/)


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Jak obliczyć przybliżone opłaty podczas korzystania z usługi Site Recovery?

Kalkulator [cen](https://aka.ms/asr_pricing_calculator) służy do szacowania kosztów podczas korzystania z usługi Site Recovery.

Aby uzyskać szczegółowe oszacowanie kosztów, uruchom narzędzie do planowania wdrażania dla [VMware](https://aka.ms/siterecovery_deployment_planner) lub [Hyper-V](https://aka.ms/asr-deployment-planner)i użyj [raportu szacowania kosztów](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Dyski zarządzane są teraz używane do replikowania maszyn wirtualnych VMware i serwerów fizycznych. Czy ponoć dodatkowe opłaty za konto magazynu pamięci podręcznej z dyskami zarządzanymi?

Nie, nie ma żadnych dodatkowych opłat za pamięć podręczną. Podczas replikowania do standardowego konta magazynu, ten magazyn pamięci podręcznej jest częścią tego samego konta magazynu docelowego.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Jestem użytkownikiem usługi Azure Site Recovery od ponad miesiąca. Czy nadal otrzymuję pierwsze 31 dni za darmo dla każdego chronionego wystąpienia?

Tak. Każde chronione wystąpienie nie pobiera żadnych opłat za usługę Azure Site Recovery przez pierwsze 31 dni. Na przykład jeśli chronisz 10 wystąpień w ciągu ostatnich 6 miesięcy i połączysz 11 wystąpienie z usługą Azure Site Recovery, nie ma żadnych opłat za 11 wystąpienie przez pierwsze 31 dni. Pierwsze 10 wystąpień nadal ponosi opłaty usługi Azure Site Recovery, ponieważ są chronione przez ponad 31 dni.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Czy w ciągu pierwszych 31 dni poniesiem inne opłaty za korzystanie z platformy Azure?

Tak, mimo że usługa Site Recovery jest bezpłatna w ciągu pierwszych 31 dni chronionego wystąpienia, możesz naliczenie opłat za usługę Azure Storage, transakcje magazynu i transfer danych. Odzyskana maszyna wirtualna może również ponieść opłaty obliczeniowe platformy Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Czy istnieje koszt związany z wykonywaniem ćwiczeń odzyskiwania po awarii/testowania w pełni sprawniku awaryjnym?

Nie ma oddzielnych kosztów dla dredy. Będą pobierane opłaty obliczeniowe po utworzeniu maszyny Wirtualnej po przejściu w stan failover testu.



## <a name="security"></a>Zabezpieczenia

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?
Nie, usługa Site Recovery nie przechwytuje replikowanych danych i nie ma żadnych informacji o tym, co jest uruchomione na maszynach wirtualnych lub serwerach fizycznych.
Dane replikacji są wymieniane między lokalnymi hostami funkcji Hyper-V, funkcjami hypervisor VMware lub serwerami fizycznymi i usługą Azure Storage lub lokacją dodatkową. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Site Recovery posiada certyfikat ISO 27001:2013, 27018, HIPAA, DPA i jest w trakcie ocen SOC2 i FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Ze względu na zgodność nawet nasze lokalne metadane muszą pozostać w tym samym regionie geograficznym. Czy odzyskiwanie witryny może nam pomóc?
Tak. Podczas tworzenia magazynu odzyskiwania witryny w regionie, zapewniamy, że wszystkie metadane, które musimy włączyć i zorganizować replikacji i pracy awaryjnej pozostaje w granicach geograficznych tego regionu.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
W przypadku maszyn wirtualnych i serwerów fizycznych replikowanie między lokacjami lokalnymi szyfrowania w tranzycie jest obsługiwane. W przypadku maszyn wirtualnych i serwerów fizycznych replikujących na platformę Azure obsługiwane są zarówno szyfrowanie w tranzycie, jak i [szyfrowanie w spoczynku (na platformie Azure).](https://docs.microsoft.com/azure/storage/storage-service-encryption)

### <a name="how-can-i-enforce-tls-12-on-all-on-premises-azure-site-recovery-components"></a>Jak wymusić TLS 1.2 we wszystkich lokalnych składnikach usługi Azure Site Recovery?
Agenci mobilności zainstalowane na zreplikowanych elementach komunikują się z serwerem process tylko w wersji TLS 1.2. Jednak komunikacja z serwera konfiguracji do platformy Azure i z serwera przetwarzania na platformę Azure może być na TLS 1.1 lub 1.0. Postępuj zgodnie [ze wskazówkami,](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) aby wymusić TLS 1.2 na wszystkich serwerach konfiguracji i serwerach przetwarzania skonfigurowanych przez Ciebie.


## <a name="disaster-recovery"></a>Odzyskiwanie po awarii

### <a name="what-can-site-recovery-protect"></a>Co może chronić odzyskiwanie witryny?
* **Maszyny wirtualne platformy Azure:** Usługa Site Recovery może replikować dowolne obciążenie uruchomione na obsługiwanej maszynie wirtualnej platformy Azure
* **Maszyny wirtualne funkcji Hyper-V:** Odzyskiwanie lokacji może chronić każde obciążenie uruchomione na maszynie wirtualnej funkcji Hyper-V.
* **Serwery fizyczne**: Usługa Site Recovery może chronić serwery fizyczne z systemem Windows lub Linux.
* **Maszyny wirtualne VMware:** Odzyskiwanie witryny może chronić każde obciążenie uruchomione w maszynie wirtualnej VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Jakie obciążenia mogę chronić za pomocą usługi Site Recovery?
Za pomocą usługi Site Recovery można chronić większość obciążeń działających na obsługiwanej maszynie wirtualnej lub na serwerze fizycznym. Usługa Site Recovery zapewnia obsługę replikacji obsługującej aplikacje, dzięki czemu aplikacje można odzyskać do stanu inteligentnego. Integruje się z aplikacjami firmy Microsoft, takimi jak SharePoint, Exchange, Dynamics, SQL Server i Active Directory, i ściśle współpracuje z wiodącymi dostawcami, w tym Oracle, SAP, IBM i Red Hat. [Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Czy mogę zarządzać odzyskiwaniem po awarii dla oddziałów firmy przy użyciu usługi Site Recovery?
Tak. Korzystając z usługi Site Recovery do organizowania replikacji i pracy awaryjnej w oddziałach firmy, otrzymasz ujednoliconą aranżację i wyświetlanie wszystkich obciążeń oddziału w centralnej lokalizacji. Możesz z łatwością uruchomić tryb failover i zarządzać odzyskiwaniem po awarii wszystkich oddziałów z siedziby, bez konieczności odwiedzania oddziałów.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Czy odzyskiwanie po awarii jest obsługiwane dla maszyn wirtualnych platformy Azure?

Tak, usługa Site Recovery obsługuje awarię maszyn wirtualnych platformy Azure między regionami platformy Azure. [Zapoznaj się z typowymi pytaniami](azure-to-azure-common-questions.md) dotyczącymi odzyskiwania po awarii maszyny Wirtualnej platformy Azure.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Czy odzyskiwanie po awarii jest obsługiwane dla maszyn wirtualnych VMware?

Tak, odzyskiwanie witryny obsługuje odzyskiwanie po awarii lokalnych maszyn wirtualnych VMware. [Przejrzyj typowe pytania](vmware-azure-common-questions.md) dotyczące odzyskiwania po awarii maszyn wirtualnych VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Czy odzyskiwanie po awarii jest obsługiwane dla maszyn wirtualnych z programem Hyper-V?
Tak, funkcja Odzysk lokacji obsługuje odzyskiwanie po awarii lokalnych maszyn wirtualnych z technologią Hyper-V. [Przejrzyj typowe pytania](hyper-v-azure-common-questions.md) dotyczące odzyskiwania po awarii maszyn wirtualnych z programem Hyper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Czy odzyskiwanie po awarii jest obsługiwane dla serwerów fizycznych?
Tak, usługa Site Recovery obsługuje odzyskiwanie po awarii lokalnych serwerów fizycznych z systemem Windows i Linux na platformie Azure lub w lokacji dodatkowej. Dowiedz się więcej o wymaganiach dotyczących odzyskiwania po awarii na [platformie Azure](vmware-physical-azure-support-matrix.md#replicated-machines)i[w lokacji dodatkowej.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)
Należy zauważyć, że serwery fizyczne będą działać jako maszyny wirtualne na platformie Azure po przemijaniu awaryjnym. Powrót po awarii z platformy Azure do lokalnego serwera fizycznego nie jest obecnie obsługiwany. Można tylko po awarii z powrotem do maszyny wirtualnej VMware.





## <a name="replication"></a>Replikacja

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Czy można replikować za pośrednictwem sieci VPN lokacji lokacji na platformie Azure?
Usługa Azure Site Recovery replikuje dane do konta magazynu platformy Azure lub dysków zarządzanych za pomocą publicznego punktu końcowego. Replikacja nie jest w sieci VPN lokacji lokacji. 

### <a name="why-cant-i-replicate-over-vpn"></a>Dlaczego nie mogę replikować przez VPN?

Podczas replikowania na platformie Azure ruch replikacji dociera do publicznych punktów końcowych usługi Azure Storage. W ten sposób można replikować tylko za pośrednictwem publicznego Internetu lub za pośrednictwem usługi ExpressRoute (komunikacja równorzędna firmy Microsoft lub istniejąca publiczna komunikacja równorzędna).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Czy mogę używać Riverbed SteelHeads do replikacji?

Nasz partner, Riverbed, zawiera szczegółowe wskazówki dotyczące pracy z usługą Azure Site Recovery. Zapoznaj się z [ich przewodnikiem po rozwiązaniach](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Czy można używać usługi ExpressRoute do replikowania maszyn wirtualnych na platformie Azure?
Tak, [usługa ExpressRoute może służyć](concepts-expressroute-with-site-recovery.md) do replikowania lokalnych maszyn wirtualnych na platformie Azure.

- Usługa Azure Site Recovery replikuje dane do usługi Azure Storage za pomocą publicznego punktu końcowego. Aby używać usługi ExpressRoute dla replikacji usługi Site Recovery, należy skonfigurować [komunikację równorzędną](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) firmy Microsoft lub użyć istniejącej [komunikacji równorzędnej](../expressroute/about-public-peering.md) (przestarzałej dla nowych obwodów).
- Komunikacja równorzędna firmy Microsoft jest zalecaną domeną routingu dla replikacji.
- Replikacja nie jest obsługiwana przez prywatną komunikację równorzędna.
- Jeśli chronisz maszyny VMware lub komputery fizyczne, upewnij się, że spełnione są również [wymagania dotyczące sieci](vmware-azure-configuration-server-requirements.md#network-requirements) dla serwera konfiguracji. Łączność z określonymi adresami URL jest wymagana przez serwer konfiguracji do aranżacji replikacji odzyskiwania lokacji. Nie można użyć usługi ExpressRoute dla tej łączności.
- Po maszynach wirtualnych zostały przejęte awaryjnie do sieci wirtualnej platformy Azure można uzyskać do nich dostęp przy użyciu prywatnej konfiguracji [komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering) z siecią wirtualną platformy Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Jeśli replikuję na platformie Azure, jakiego rodzaju konta magazynu lub dysku zarządzanego potrzebuję?

Potrzebujesz magazynu LRS lub GRS. Zalecamy użycie konta GRS, dzięki czemu dane będą odporne w przypadku regionalnej awarii lub sytuacji, w której nie można odzyskać regionu podstawowego. Konto musi znajdować się w tym samym regionie co magazyn Usług odzyskiwania. Magazyn w wersji Premium jest obsługiwany dla maszyn wirtualnych VMware, maszyny wirtualnej funkcji Hyper-V i replikacji serwera fizycznego podczas wdrażania usługi Site Recovery w witrynie Azure portal. Dyski zarządzane obsługują tylko lrs.

### <a name="how-often-can-i-replicate-data"></a>Jak często mogę replikować dane?
* **Funkcja Hyper-V:** Maszyny wirtualne funkcji Hyper V mogą być replikowane co 30 sekund (z wyjątkiem magazynu w wersji premium), pięć minut lub 15 minut.
* **Maszyny wirtualne platformy Azure, maszyny wirtualne VMware, serwery fizyczne:** Częstotliwość replikacji nie ma tu znaczenia. Replikacja jest ciągła.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Czy można rozszerzyć replikację z istniejącego lokacji odzyskiwania do innej lokacji trzeciorzędnej?
Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Poproś o tę funkcję na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Czy mogę przeprowadzić replikację w trybie offline podczas pierwszej replikacji do platformy Azure?
Ta funkcja nie jest obsługiwana. Poproś o tę funkcję na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Czy z replikacji można wykluczyć określone dyski?
Jest to obsługiwane podczas replikowania maszyn wirtualnych i maszyn wirtualnych funkcji Hyper-V na platformie Azure przy użyciu witryny Azure portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Czy można replikować maszyny wirtualne z dyskami dynamicznymi?
Dyski dynamiczne są obsługiwane podczas replikowania maszyn wirtualnych funkcji Hyper-V oraz podczas replikowania maszyn wirtualnych i maszyn fizycznych na platformie Azure. Dysk systemu operacyjnego musi być dyskiem podstawowym.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Czy można ograniczyć przepustowość przydzieloną dla ruchu replikacji?
Tak. Więcej informacji na temat ograniczania przepustowości można przeczytać w następujących artykułach:

* [Planowanie pojemności do replikowania maszyn wirtualnych i serwerów fizycznych](site-recovery-plan-capacity-vmware.md)
* [Planowanie pojemności do replikowania maszyn wirtualnych funkcji Hyper V na platformie Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Tryb failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Jeśli przewiduję awaryjną usługę Azure, jak uzyskać dostęp do maszyn wirtualnych platformy Azure po przełączeniu w ten sposób po awarii?

Maszyny wirtualne Azure są dostępne za pośrednictwem bezpiecznego połączenia internetowego, sieci VPN między lokacjami lub za pośrednictwem usługi Azure ExpressRoute. Musisz przygotować wiele rzeczy, aby połączyć. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Jeśli przejmę awaryjną platformę Azure, w jaki sposób platforma Azure upewnij się, że moje dane są odporne?
Platforma Azure została zaprojektowana z myślą o odporności danych. Usługa Site Recovery została już zaprojektowana pod kątem pracy awaryjnej w pomocniczym centrum danych platformy Azure zgodnie z usługą Azure SLA. Jeśli tak się stanie, upewnimy się, że metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla przechowalni.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Jeśli replikuję między dwoma centrami danych, co się stanie, jeśli moje główne centrum danych wystąpi nieoczekiwana awaria?
Możesz wyzwolić nieplanowany tryb failover z lokacji dodatkowej. Usługa Site Recovery nie wymaga łączności z lokacją główną do pracy w trybie failover.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?
Tryb failover nie jest automatyczny. Zainicjować pracy awaryjnej za pomocą jednego kliknięcia w portalu lub można użyć [programu Site Recovery PowerShell](/powershell/module/az.recoveryservices) wyzwolić pracy awaryjnej. Niepowodzenie z powrotem jest prostą akcją w portalu odzyskiwania witryny.

Aby zautomatyzować można użyć lokalnego programu Orchestrator lub programu Operations Manager do wykrycia awarii maszyny wirtualnej, a następnie wyzwolić pracę awaryjną przy użyciu SDK.

* [Dowiedz się więcej](site-recovery-create-recovery-plans.md) o planach odzyskiwania.
* [Dowiedz się więcej](site-recovery-failover.md) o pracy awaryjnej.
* [Dowiedz się więcej](site-recovery-failback-azure-to-vmware.md) o awarii maszyn wirtualnych VMware i serwerów fizycznych

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Jeśli mój host lokalny nie odpowiada lub uległ awarii, czy mogę wrócić do innego hosta?
Tak, można użyć odzyskiwania lokalizacji alternatywnej do powrotu po awarii do innego hosta z platformy Azure.

* [Dla maszyn wirtualnych VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Dla maszyn wirtualnych Hyper-V](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automatyzacja

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Czy można zautomatyzować scenariusze odzyskiwania witryny za pomocą sdk?
Tak. Przepływy pracy usługi Site Recovery można zautomatyzować przy użyciu interfejsu API REST, programu PowerShell lub zestawu SDK platformy Azure. Obecnie obsługiwane scenariusze wdrażania usługi Site Recovery przy użyciu programu PowerShell:

* [Replikowanie maszyn wirtualnych funkcji Hyper V w chmurach maszyn wirtualnych do menedżera zasobów programu Azure PowerShell](hyper-v-vmm-powershell-resource-manager.md)
* [Replikowanie maszyn wirtualnych funkcji Hyper V bez programu VMM do menedżera zasobów programu Azure PowerShell](hyper-v-azure-powershell-resource-manager.md)
* [Replikowanie oprogramowania VMware na platformę Azure za pomocą menedżera zasobów programu PowerShell](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Uaktualnienie składnika/dostawcy

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Gdzie można znaleźć informacje o wersji/pakiety zbiorcze aktualizacji usługi Site Recovery

[Dowiedz się więcej](site-recovery-whats-new.md) o nowych aktualizacjach i [uzyskaj informacje o zestawieniu](service-updates-how-to.md).

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [omówieniem usługi Site Recovery](site-recovery-overview.md)

