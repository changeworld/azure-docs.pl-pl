---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web IaaS dla PCI DSS
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web IaaS dla PCI DSS
services: security
author: meladie
ms.assetid: 9c825380-2abe-4fdd-800c-59d1fed1780b
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 6bff56f70c9ab0ab74e3f30c9571b3e4010b33e0
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946632"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-pci-dss"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure: Aplikacja sieci Web IaaS dla PCI DSS

## <a name="overview"></a>Omówienie

Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera wskazówki dotyczące wdrożenia w środowisku usługi Infrastructure Industry Standard (PCI DSS 3,2) zgodnego z infrastrukturą jako usługą (IaaS), odpowiednią dla kolekcji, magazynu i pobierania dane posiadacza kart. Zawarto w nim wspólną architekturę referencyjną i demonstruje właściwą obsługę danych karty kredytowej (w tym numeru karty, daty ważności i danych weryfikacyjnych) w bezpiecznym, zgodnym i wielowarstwowym środowisku. Ten plan przedstawia kompleksowe rozwiązanie do zaspokajania potrzeb organizacji poszukujących rozwiązań opartych na chmurze w celu zmniejszenia obciążenia i kosztów wdrożenia.

Ta architektura referencyjna, przewodnik implementacji i model zagrożeń stanowią podstawę dla klientów spełniających wymagania PCI DSS 3,2. To rozwiązanie udostępnia linię bazową, która ułatwia klientom wdrażanie obciążeń na platformie Azure w ramach zgodności z PCI DSS 3,2. jednak tego rozwiązania nie należy używać w środowisku produkcyjnym, ponieważ wymagana jest dodatkowa konfiguracja.

Osiągnięcie PCI DSS-zgodność wymaga, aby akredytowany wykwalifikowany podmiot zabezpieczeń (QSA) zaświadczy produkcyjne rozwiązanie dla klientów. Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki

To rozwiązanie służy do wdrażania architektury referencyjnej dla aplikacji sieci Web IaaS z zapleczem SQL Server. Architektura obejmuje warstwę sieci Web, warstwę danych, Active Directory infrastrukturę, Application Gateway i Load Balancer. Maszyny wirtualne wdrożone w warstwach sieci Web i danych są skonfigurowane w zestawie dostępności, a wystąpienia SQL Server są konfigurowane w grupie dostępności zawsze w celu zapewnienia wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny, a zasady grupy Active Directory są używane do wymuszania konfiguracji zabezpieczeń i zgodności na poziomie systemu operacyjnego. Host zarządzania bastionu zapewnia bezpieczne połączenie dla administratorów w celu uzyskania dostępu do wdrożonych zasobów. **Platforma Azure zaleca skonfigurowanie połączenia sieci VPN lub ExpressRoute na potrzeby zarządzania i importowania danych do podsieci architektury referencyjnej.**

![IaaS aplikacji sieci Web dla PCI DSS diagram architektury referencyjnej](images/pcidss-iaaswa-architecture.png "IaaS aplikacji sieci Web dla PCI DSS diagram architektury referencyjnej")

To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

- Zestawy dostępności
    - (1) Active Directory kontrolery domeny
    - (1) węzły klastra SQL
    - (1) sieć Web/IIS
- Usługa Azure Active Directory
- Azure Application Gateway
    - (1) Zapora aplikacji sieci Web
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - Port odbiornika: 443
- W usłudze Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Storage
    - (7) konta magazynu geograficznie nadmiarowego
- Usługa Azure Virtual Machines
    - (1) Zarządzanie/bastionu (Windows Server 2016 Datacenter)
    - (2) Active Directory kontroler domeny (Windows Server 2016 Datacenter)
    - (2) SQL Server węzeł klastra (SQL Server 2017 w systemie Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1)/16 sieci
    - (5)/24 sieci
    - (5) sieciowe grupy zabezpieczeń
- Monitor w chmurze
- Magazyn Recovery Services

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do wdrożonych zasobów w tym środowisku. Host bastionu zapewnia bezpieczne połączenie z wdrożonymi zasobami przez umożliwienie zdalnego ruchu z publicznych adresów IP na bezpiecznej liście. Aby zezwolić na ruch pulpitu zdalnego (RDP), źródło ruchu musi być zdefiniowane w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako przyłączony do domeny Host bastionu z następującymi konfiguracjami:
-   [Rozszerzenie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Diagnostyka Azure rozszerzenie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) przy użyciu Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) w celu ograniczenia użycia zasobów maszyny wirtualnej, gdy nie są używane
-   Włączono funkcję [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) , dzięki czemu poświadczenia i inne wpisy tajne są uruchamiane w chronionym środowisku, które jest odizolowane od działającego systemu operacyjnego

### <a name="virtual-network"></a>Sieć wirtualna

Architektura definiuje prywatną sieć wirtualną z przestrzenią adresową 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: To rozwiązanie wdraża zasoby w architekturze z oddzielną podsiecią internetową, podsiecią bazy danych, podsiecią Active Directoryową i podsiecią zarządzania w sieci wirtualnej. Podsieci są logicznie oddzielone przez reguły sieciowej grupy zabezpieczeń stosowane do poszczególnych podsieci, aby ograniczyć ruch między podsieciami tylko do tych, które są niezbędne dla funkcji systemu i zarządzania.

Zapoznaj się z konfiguracją [sieciowych grup zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożonych z tym rozwiązaniem. Organizacje mogą konfigurować sieciowe grupy zabezpieczeń, edytując plik powyżej za pomocą [tej dokumentacji](../../virtual-network/virtual-network-vnet-plan-design-arm.md) jako przewodnika.

Każda podsieć ma dedykowaną sieciową grupę zabezpieczeń:
- 1 sieciowa Grupa zabezpieczeń dla Application Gateway (LBNSG)
- 1 sieciowa Grupa zabezpieczeń dla hosta bastionu (MGTNSG)
- 1 sieciowa Grupa zabezpieczeń dla kontrolerów domeny podstawowej i zapasowej (ADNSG)
- 1 sieciowa Grupa zabezpieczeń dla serwerów SQL i monitor chmury (SQLNSG)
- 1 sieciowa Grupa zabezpieczeń dla warstwy sieci Web (WEBNSG)

### <a name="data-in-transit"></a>Dane przesyłane

Platforma Azure domyślnie szyfruje całą komunikację do i z centrów danych platformy Azure. Ponadto wszystkie transakcje do usługi Azure Storage za pośrednictwem Azure Portal odbywają się za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane przechowywane przez wiele miar, w tym szyfrowanie i inspekcję bazy danych.

**Azure Storage**: Aby zaspokoić zaszyfrowane dane w wymaganiach dotyczących REST, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używają [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md). Pomaga to w ochronie i ochronie danych posiadaczy kart w celu wspierania zobowiązań w zakresie zabezpieczeń organizacji i wymagań dotyczących zgodności zdefiniowanych przez PCI DSS 3,2.

**Azure Disk Encryption**: Azure Disk Encryption jest używany do szyfrowania dysków maszyn wirtualnych z systemem Windows IaaS. [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków systemu operacyjnego i danych. Rozwiązanie jest zintegrowane z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**SQL Server**: W wystąpieniu SQL Server są stosowane następujące miary zabezpieczeń bazy danych:
- [Active Directory uwierzytelnianie i autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwiają zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
-   SQL Database jest skonfigurowany do używania [transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje szyfrowanie w czasie rzeczywistym oraz odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w celu ochrony informacji przechowywanych w pamięci podręcznej. Transparent Data Encryption zapewnia gwarancję, że przechowywane dane posiadaczy kart nie podlegają nieautoryzowanemu dostępowi.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](../../sql-database/sql-database-threat-detection.md) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalne luki w zabezpieczeniach, ataki z iniekcją SQL i nietypowe wzorce dostępu do bazy danych.
-   [Always Encrypted kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane posiadacza nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- Funkcja [właściwości rozszerzonych](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) może służyć do zaprzestania przetwarzania podmiotów danych, ponieważ umożliwia użytkownikom dodawanie niestandardowych właściwości do obiektów bazy danych i oznaczanie ich jako "nieobsługiwanych" w celu zapewnienia obsługi logiki aplikacji w celu uniemożliwienia przetwarzania skojarzonych dane posiadacza kart.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umożliwiają użytkownikom Definiowanie zasad ograniczających dostęp do danych w celu ich dalszego przetwarzania.
- [SQL Database dynamiczna maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza narażenie danych posiadaczy kart przez maskowanie danych do użytkowników bez uprawnień lub aplikacji. Dynamiczne maskowanie danych może automatycznie odnajdywać potencjalnie poufne dane i zasugerować odpowiednie maski, które mają zostać zastosowane. Pomaga to identyfikować i ograniczać dostęp do danych posiadaczy kart, które nie opuszczają bazy danych za pośrednictwem nieautoryzowanego dostępu. Klienci są odpowiedzialni za dostosowanie ustawień dynamicznego maskowania danych, aby były zgodne ze schematem bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie zapewniają możliwości zarządzania dostępem do danych w środowisku platformy Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) to usługa&#39;zarządzania katalogiem i tożsamościami opartymi na chmurze z wieloma dzierżawcami firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzone w Azure Active Directory, w tym użytkownicy uzyskujący dostęp do Azure SQL Database.
- Uwierzytelnianie w aplikacji odbywa się przy użyciu Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ponadto szyfrowanie kolumny bazy danych używa Azure Active Directory do uwierzytelniania aplikacji w Azure SQL Database. Aby uzyskać więcej informacji, zobacz jak [chronić poufne dane w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Kontrola dostępu oparta na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md) umożliwia administratorom definiowanie szczegółowych uprawnień dostępu w celu udzielania dostępu tylko użytkownikom potrzebnym do wykonywania swoich zadań. Zamiast zapewniać każdemu użytkownikowi nieograniczony dostęp do zasobów platformy Azure, Administratorzy mogą zezwalać na dostęp do danych tylko określonym akcjom. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) pozwala klientom zminimalizować liczbę użytkowników, którzy mają dostęp do określonych informacji. Administratorzy mogą używać Azure Active Directory Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach,&#39;które mają wpływ na tożsamości organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami organizacji&#39;i bada podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.

### <a name="security"></a>Bezpieczeństwo

**Zarządzanie**wpisami tajnymi: Rozwiązanie używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje Azure Key Vault pomagają klientom chronić i uzyskiwać dostęp do tych danych:

- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza to chroniony przez moduł HSM 2048-bitowy klucz RSA.
- Wszyscy użytkownicy i tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.
- Rozwiązanie jest zintegrowane z Azure Key Vault zarządzania dyskami maszyny wirtualnej IaaS — kluczami szyfrowania i wpisami tajnymi.

**Zarządzanie poprawkami**: Maszyny wirtualne z systemem Windows wdrożone w ramach tej architektury referencyjnej są domyślnie skonfigurowane tak, aby otrzymywać aktualizacje automatyczne z usługi Windows Update. To rozwiązanie obejmuje również usługę [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , za pomocą której można tworzyć zaktualizowane wdrożenia w celu zastosowania poprawek do maszyn wirtualnych w razie potrzeby.

**Ochrona przed złośliwym oprogramowaniem**: [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware) Virtual Machines zapewnia ochronę w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie, z konfigurowalnymi alertami, gdy znane złośliwe lub niepożądane oprogramowanie próbuje Zainstaluj lub Uruchom na chronionych maszynach wirtualnych.

**Azure Security Center**: Dzięki [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Ponadto Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.

Azure Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków ukierunkowanych na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Azure Security Center ma zestaw [wstępnie zdefiniowanych alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanego działania. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Azure Security Center umożliwiają klientom definiowanie nowych alertów zabezpieczeń na podstawie danych, które zostały już zebrane z ich środowiska.

Azure Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia, dzięki czemu klienci mogą łatwiej wykrywać i rozwiązywać potencjalne problemy z zabezpieczeniami. [Raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdego wykrytego zagrożenia, aby pomóc zespołom reagowania na incydenty w trakcie badania i korygowaniem zagrożeń.

**Application Gateway platformy Azure**: Architektura zmniejsza ryzyko wystąpienia luk w zabezpieczeniach przy użyciu Application Gateway platformy Azure z skonfigurowaną zaporą aplikacji sieci Web, a zestaw reguł OWASP jest włączony. Dodatkowe możliwości obejmują:

- [Kompleksowa — SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](../../application-gateway/create-ssl-portal.md)
- Wyłącz [protokół TLS w wersji 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](../../application-gateway/waf-overview.md) (tryb zapobiegania)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z zestawem reguł OWASP 3,0
- Włącz [rejestrowanie diagnostyczne](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) i [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewniają dodatkową ochronę i powiadomienia. Azure Security Center udostępnia również system reputacji.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: Rozwiązanie wdraża wszystkie maszyny wirtualne w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności zapewniają, że maszyny wirtualne są rozproszone w wielu izolowanych klastrach sprzętowych w celu zwiększenia dostępności. Co najmniej jedna maszyna wirtualna jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, które spełnia warunki umowy SLA na 99,95%.

**Magazyn Recovery Services**: [Magazyn Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowych i chroni wszystkie konfiguracje Virtual Machines platformy Azure w tej architekturze. W przypadku magazynu Recovery Services klienci mogą przywracać pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej, co umożliwia szybsze przywracanie.

**Monitor chmury**: [Monitor w chmurze](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) to typ monitora kworum klastra trybu failover w systemie Windows Server 2016, który wykorzystuje platformę Azure jako punkt rozstrzygania. Monitor w chmurze, taki jak każdy inny monitor kworum, otrzymuje głos i może uczestniczyć w obliczeniach kworum, ale korzysta ze standardowego publicznie dostępnego Blob Storage platformy Azure. Eliminuje to dodatkowe obciążenie pracą maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja

Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/diagnostic-logs-overview.md) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Dzienniki te obejmują Dzienniki systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Przechowywanie jest możliwe do skonfigurowania przez użytkownika, do 730 dni, w celu spełnienia wymagań dotyczących przechowywania specyficznych dla organizacji.

**Azure monitor dzienników**: Te dzienniki są konsolidowane w [Azure monitor dziennikach](https://azure.microsoft.com/services/log-analytics/) na potrzeby przetwarzania, przechowywania i raportowania na pulpicie nawigacyjnym. Po zebraniu dane są zorganizowane w oddzielne tabele dla każdego typu danych w obszarze obszary robocze Log Analytics, co umożliwia analizowanie wszystkich danych razem niezależnie od oryginalnego źródła. Ponadto Azure Security Center integruje się z dziennikami Azure Monitor, dzięki czemu klienci mogą korzystać z zapytań Kusto w celu uzyskania dostępu do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

Następujące rozwiązania do [monitorowania](../../monitoring/monitoring-solutions.md) platformy Azure są dostępne w ramach tej architektury:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie Active Directory Sprawdzanie kondycji ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie Sprawdzanie kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu oraz zapewnia klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, oraz liczby agentów przesyłających dane operacyjne.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowuje elementy Runbook, uruchamia je i zarządza nimi. W tym rozwiązaniu elementy Runbook ułatwiają zbieranie dzienników z Azure SQL Database. Rozwiązanie [Change Tracking](../../automation/change-tracking.md) Automation umożliwia klientom łatwe identyfikowanie zmian w środowisku.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów przez umożliwienie organizacjom inspekcji, tworzenia alertów i archiwizowania danych, w tym śledzenia wywołań interfejsu API w swoich zasobach platformy Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych (DPD) dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/pcidss-iaaswa-tm) lub można go znaleźć poniżej. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![IaaS aplikacji sieci Web dla PCI DSS diagram architektury referencyjnej](images/pcidss-iaaswa-threat-model.png "IaaS aplikacji sieci Web dla PCI DSS diagram architektury referencyjnej")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności

[Macierz odpowiedzialna za Strategia zabezpieczeń i zgodności z przepisami platformy Azure PCI DSS klienta](https://aka.ms/pcidss-crm) zawiera listę obowiązków związanych ze wszystkimi wymaganiami PCI DSS 3,2.

[Macierz implementacji aplikacji sieci web strategia zabezpieczeń i zgodności z przepisami platformy Azure-PCI DSS IaaS](https://aka.ms/pcidss-iaaswa-cim) zawiera informacje o tym, które wymagania PCI DSS 3,2 są rozwiązywane przez architekturę aplikacji sieci Web IaaS, w tym szczegółowe opisy tego, jak Implementacja spełnia wymagania poszczególnych artykułów objętych usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute

Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web IaaS. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a Virtual Network platformy Azure. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne informacje "tunel" w ramach zaszyfrowanego połączenia między siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. [Tryb tunelowania IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w tej opcji jako mechanizm szyfrowania.

Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną, jeszcze bardziej bezpieczną opcję połączenia. Azure ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie z dostawcą telekomunikacyjnym klienta, dane nie podróżują przez Internet i w związku z tym nie są dla niego ujawniane.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

## <a name="disclaimer"></a>Zastrzeżenie

- Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
- Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
- Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
- Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
- Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
- Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
