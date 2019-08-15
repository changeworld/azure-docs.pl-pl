---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — magazyn danych dla Wielkiej Brytanii NHS
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — magazyn danych dla Wielkiej Brytanii NHS
services: security
author: jomolesk
ms.assetid: f4e4b939-88db-4d78-8fa9-c2a12b2c025b
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 3519b3f2c13349c3764e95bcc6898eeb73a79a81
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946623"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-uk-nhs"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure: Magazyn danych dla Wielkiej Brytanii NHS

## <a name="overview"></a>Omówienie

Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera architekturę referencyjną i wskazówki dotyczące rozwiązania magazynu danych odpowiednie do bezpiecznego pozyskiwania, przemieszczania, przechowywania i korzystania z poufnych danych opieki zdrowotnej. W tym rozwiązaniu pokazano, w jaki sposób klienci mogą przestrzegać wskazówek dotyczących usługi [Cloud Security dobry Practice Guide](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) opublikowanej przez [NHS Digital](https://digital.nhs.uk/), partnera działu IT (Zjednoczone Królestwo) ds. zdrowia i opieki społecznej (DHSC). Przewodnik dotyczący zabezpieczeń w chmurze jest oparty na 14 [zasadach zabezpieczeń w chmurze](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) opublikowanych przez narodowe Zjednoczone Królestwo cybernetycznymi Security Centre (NCSC).

Ta architektura referencyjna, przewodnik implementacji i model zagrożeń mają służyć jako podstawa dla klientów, którzy dostosowują się do określonych wymagań i nie powinny być używane w środowisku produkcyjnym bez dodatkowej konfiguracji. Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki

To rozwiązanie zapewnia architekturę referencyjną, która implementuje magazyn danych w chmurze o wysokiej wydajności i bezpiecznych. W tej architekturze są dwie osobne warstwy danych: jedno miejsce, w którym dane są importowane, przechowywane i przemieszczane w ramach klastrowanego środowiska SQL, a drugi dla Azure SQL Data Warehouse, w którym dane są ładowane przy użyciu narzędzia wyodrębniania, przekształcania, ładowania (np. [Base ](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Zapytania T-SQL) do przetwarzania. Gdy dane są przechowywane w Azure SQL Data Warehouse, analiza może być uruchamiana na dużą skalę.

Platforma Azure oferuje różne usługi raportowania i analiz dla klienta. To rozwiązanie zawiera SQL Server Reporting Services do szybkiego tworzenia raportów z Azure SQL Data Warehouse. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL w ramach dołączania certyfikatów z podpisem własnym. Najlepszym rozwiązaniem jest to, że platforma Azure zaleca korzystanie z zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.

Dane w Azure SQL Data Warehouse są przechowywane w tabelach relacyjnych z magazynem kolumnowym, co znacznie zmniejsza koszty magazynowania danych przy jednoczesnym zwiększeniu wydajności zapytań.  W zależności od wymagań dotyczących użycia Azure SQL Data Warehouse zasoby obliczeniowe mogą być skalowane w górę lub w dół lub wyłączane całkowicie, jeśli nie ma aktywnych procesów wymagających zasobów obliczeniowych.

Moduł równoważenia obciążenia SQL zarządza ruchem SQL, zapewniając wysoką wydajność. Wszystkie maszyny wirtualne w tej architekturze referencyjnej wdrażają się w zestawie dostępności z wystąpieniami SQL Server skonfigurowanymi w zawsze włączonym grupie dostępności na potrzeby funkcji wysokiej dostępności i odzyskiwania po awarii.

W celu zwiększenia bezpieczeństwa wszystkie zasoby w tym rozwiązaniu są zarządzane jako Grupa zasobów za pomocą Azure Resource Manager. Azure Active Directory kontroli dostępu opartej na rolach służy do kontrolowania dostępu do wdrożonych zasobów i kluczy w Azure Key Vault. Kondycja systemu jest monitorowana za poorednictwem Azure Security Center i Azure Monitor. Klienci konfigurują obie usługi monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwym do nawigacji Pulpitie nawigacyjnym.


Ta architektura referencyjna magazynu danych obejmuje również warstwę Active Directory na potrzeby zarządzania zasobami w ramach architektury. Podsieć Active Directory umożliwia łatwe wdrażanie w ramach większej struktury lasów Active Directory, co pozwala na ciągłą eksploatację środowiska, nawet gdy dostęp do większego lasu jest niedostępny. Wszystkie maszyny wirtualne są przyłączone do domeny Active Directory i używają zasad grupy Active Directory, aby wymusić konfiguracje zabezpieczeń i zgodności na poziomie systemu operacyjnego.

Maszyna wirtualna służy jako host zarządzania bastionu, co zapewnia bezpieczne połączenie dla administratorów w celu uzyskania dostępu do wdrożonych zasobów. Dane są ładowane do obszaru przejściowego za pomocą tego hosta zarządzania bastionu. **Firma Microsoft zaleca skonfigurowanie połączenia sieci VPN lub usługi Azure ExpressRoute na potrzeby zarządzania i importowania danych do podsieci architektury referencyjnej.**

![Magazyn danych dla diagramu architektury referencyjnej dla Wielkiej Brytanii NHS](images/uknhs-datawarehouse-architecture.png?raw=true "Magazyn danych dla diagramu architektury referencyjnej dla Wielkiej Brytanii NHS")

To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

- Zestawy dostępności
    - Active Directory kontrolery domeny
    - Węzły klastra SQL i monitor
- Usługa Azure Active Directory
- Azure Data Catalog
- W usłudze Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Automation
- Usługa Azure Virtual Machines
    - (1) Host bastionu
    - (2) Active Directory kontroler domeny
    - (2) SQL Server węzeł klastra
    - (1) monitor SQL Server
- Azure Virtual Network
    - (1)/16 sieci
    - (4)/24 sieci
    - (4) sieciowe grupy zabezpieczeń
- Magazyn Recovery Services
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) to magazyn danych przedsiębiorstwa, który wykorzystuje wysoce równoległe przetwarzanie, aby szybko uruchamiać złożone zapytania na petabajtów danych, dzięki czemu użytkownicy mogą skutecznie identyfikować dane dotyczące opieki zdrowotnej. Użytkownicy mogą używać prostych zapytań T-SQL w celu zaimportowania danych Big Data do SQL Data Warehouse i wykorzystaniu możliwości masowego przetwarzania równoległego w celu uruchomienia analizy o wysokiej wydajności.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) umożliwia szybkie tworzenie raportów za pomocą tabel, wykresów, map, mierników, macierzy i innych elementów Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](../../data-catalog/overview.md) umożliwia łatwe odnajdywanie źródeł danych i zrozumienie ich przez użytkowników, którzy zarządzają danymi. Typowe źródła danych mogą być rejestrowane, otagowane i przeszukiwane pod kątem danych związanych z kondycją. Dane pozostają w istniejącej lokalizacji, ale kopia jej metadanych zostanie dodana do Data Catalog wraz z odwołaniem do lokalizacji źródła danych. Te metadane są również indeksowane, aby można było je łatwo odnaleźć za pomocą wyszukiwania oraz uczynić je zrozumiałymi dla użytkowników, którzy je odnajdą.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do wdrożonych zasobów w tym środowisku. Host bastionu zapewnia bezpieczne połączenie z wdrożonymi zasobami przez umożliwienie zdalnego ruchu z publicznych adresów IP na bezpiecznej liście. Aby zezwolić na ruch pulpitu zdalnego (RDP), źródło ruchu musi być zdefiniowane w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako przyłączony do domeny Host bastionu z następującymi konfiguracjami:
-   [Rozszerzenie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Diagnostyka Azure rozszerzenie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) przy użyciu Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) w celu ograniczenia użycia zasobów maszyny wirtualnej, gdy nie są używane
-   Włączono funkcję [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) , dzięki czemu poświadczenia i inne wpisy tajne są uruchamiane w chronionym środowisku, które jest odizolowane od działającego systemu operacyjnego

### <a name="virtual-network"></a>Sieć wirtualna

Ta architektura referencyjna definiuje prywatną sieć wirtualną z przestrzenią adresową 10.0.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) zawierają listy kontroli dostępu, które zezwalają na ruch lub go odmawiają w ramach sieci wirtualnej. Sieciowe grupy zabezpieczeń mogą służyć do zabezpieczania ruchu w podsieci lub na poziomie poszczególnych maszyn wirtualnych. Istnieją następujące sieciowe grupy zabezpieczeń:

  - Sieciowa Grupa zabezpieczeń dla warstwy danych (klastrów SQL Server, SQL Server monitora i modułu równoważenia obciążenia SQL)
  - Sieciowa Grupa zabezpieczeń dla hosta usługi Management bastionu
  - Sieciowa Grupa zabezpieczeń dla Active Directory
  - Sieciowa Grupa zabezpieczeń dla SQL Server Reporting Services

Każda z sieciowych grup zabezpieczeń ma otwarte określone porty i protokoły, dzięki czemu rozwiązanie może być bezpieczne i poprawne. Ponadto dla każdej sieciowej grupy zabezpieczeń są włączone następujące konfiguracje:

- [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
- Dzienniki Azure Monitor są połączone z [dziennikami diagnostycznymi&#39;sieciowych grup zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: Każda podsieć jest skojarzona z odpowiednią sieciową grupą zabezpieczeń.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane przechowywane przez wiele miar, w tym szyfrowanie i inspekcję bazy danych.

Architektura chroni dane przechowywane przez szyfrowanie, inspekcję bazy danych i inne miary.

**Azure Storage**: Aby zaspokoić zaszyfrowane dane w wymaganiach dotyczących REST, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używają [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md). Pomaga to w ochronie i ochronie danych w celu obsługi zobowiązań w zakresie zabezpieczeń organizacji i wymagań dotyczących zgodności zdefiniowanych przez NHS Digital.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków danych. Rozwiązanie integruje się z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**Azure SQL Database**: W wystąpieniu Azure SQL Database są stosowane następujące miary zabezpieczeń bazy danych:

- [Active Directory uwierzytelnianie i autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwiają zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
- [Inspekcja bazy danych SQL](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
- Azure SQL Database jest skonfigurowany do korzystania z funkcji [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), która wykonuje szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, aby chronić informacje przechowywane w czasie rzeczywistym. Przezroczyste szyfrowanie danych zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.
- [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
- [Wykrywanie zagrożeń SQL](../../sql-database/sql-database-threat-detection.md) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalne luki w zabezpieczeniach, ataki z iniekcją SQL i nietypowe wzorce dostępu do bazy danych.
- [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [SQL Database Dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza narażenie na dane poufne przez zamaskowanie danych do użytkowników nieuprzywilejowanych lub aplikacji. Dynamiczne maskowanie danych może automatycznie odnajdywać potencjalnie poufne dane i zasugerować odpowiednie maski, które mają zostać zastosowane. Pomaga to identyfikować i ograniczać dostęp do danych w taki sposób, aby nie zamykał bazy danych za pośrednictwem nieautoryzowanego dostępu. Klienci są odpowiedzialni za dostosowanie ustawień dynamicznego maskowania danych, aby były zgodne ze schematem bazy danych.

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
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza jest sprzętowym modułem zabezpieczeń chronionym 2048-bitowym kluczem RSA.
- Wszyscy użytkownicy i tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.

**Zarządzanie poprawkami**: Maszyny wirtualne z systemem Windows wdrożone w ramach tej architektury referencyjnej są domyślnie skonfigurowane tak, aby otrzymywać aktualizacje automatyczne z usługi Windows Update. To rozwiązanie obejmuje również usługę [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , za pomocą której można tworzyć zaktualizowane wdrożenia w celu zastosowania poprawek do maszyn wirtualnych w razie potrzeby.

**Ochrona przed złośliwym oprogramowaniem**: [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware) dla maszyn wirtualnych zapewnia ochronę w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie, z konfigurowalnymi alertami, gdy znane złośliwe lub niepożądane oprogramowanie próbuje Zainstaluj lub Uruchom na chronionych maszynach wirtualnych.

**Azure Security Center**: Dzięki [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Ponadto Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.

Azure Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków ukierunkowanych na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Azure Security Center ma zestaw [wstępnie zdefiniowanych alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanego działania. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Azure Security Center umożliwiają klientom definiowanie nowych alertów zabezpieczeń na podstawie danych, które zostały już zebrane z ich środowiska.

Azure Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia, dzięki czemu klienci mogą łatwiej wykrywać i rozwiązywać potencjalne problemy z zabezpieczeniami. [Raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdego wykrytego zagrożenia, aby pomóc zespołom reagowania na incydenty w trakcie badania i korygowaniem zagrożeń.

Ponadto ta architektura referencyjna wykorzystuje [ocenę luk](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) w zabezpieczeniach w Azure Security Center. Po skonfigurowaniu Agent partnera (np. Qualys) zgłasza luki w zabezpieczeniach do platformy zarządzania partnera. Z kolei Platforma zarządzania przez partnera zapewnia informacje dotyczące luk i monitorowania kondycji z powrotem do Azure Security Center, dzięki czemu klienci mogą szybko identyfikować zagrożone maszyny wirtualne.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: Obciążenia serwera są pogrupowane w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , aby zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Co najmniej jedna maszyna wirtualna jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, które spełnia warunki umowy SLA na 99,95%.

**Magazyn Recovery Services**: [Magazyn Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowych i chroni wszystkie konfiguracje maszyn wirtualnych platformy Azure w tej architekturze. W przypadku magazynu Recovery Services klienci mogą przywracać pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej, co umożliwia szybsze przywracanie.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja

Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/diagnostic-logs-overview.md) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Dzienniki te obejmują Dzienniki systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Przechowywanie jest możliwe do skonfigurowania przez użytkownika, do 730 dni, w celu spełnienia wymagań dotyczących przechowywania specyficznych dla organizacji.

**Azure monitor dzienników**: Te dzienniki są konsolidowane w [Azure monitor dziennikach](https://azure.microsoft.com/services/log-analytics/) na potrzeby przetwarzania, przechowywania i raportowania na pulpicie nawigacyjnym. Po zebraniu dane są organizowane w oddzielnych tabelach dla każdego typu danych, dzięki czemu wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Ponadto Azure Security Center integruje się z dziennikami Azure Monitor, dzięki czemu klienci mogą korzystać z zapytań Kusto w celu uzyskania dostępu do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

Następujące rozwiązania do [monitorowania](../../monitoring/monitoring-solutions.md) platformy Azure są dostępne w ramach tej architektury:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie Active Directory Sprawdzanie kondycji ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie Sprawdzanie kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu oraz zapewnia klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, oraz liczby agentów przesyłających dane operacyjne.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowuje elementy Runbook, uruchamia je i zarządza nimi. W tym rozwiązaniu elementy Runbook ułatwiają zbieranie dzienników z Azure SQL Database. Rozwiązanie [Change Tracking](../../automation/change-tracking.md) Automation umożliwia klientom łatwe identyfikowanie zmian w środowisku.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów przez umożliwienie organizacjom inspekcji, tworzenia alertów i archiwizowania danych, w tym śledzenia wywołań interfejsu API w swoich zasobach platformy Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/uknhs-dw-tm) lub można go znaleźć poniżej. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![Magazyn danych dla modelu zagrożeń NHS UK](images/uknhs-datawarehouse-threat-model.png?raw=true "Magazyn danych dla modelu zagrożeń NHS UK")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności

W [macierzy strategia zabezpieczeń i zgodności z przepisami platformy Azure – UK NHS — Wielka Brytania](https://aka.ms/uknhs-crm) zawiera wszystkie wymagania NHS. Ta macierz zawiera szczegółowe informacje o tym, czy implementacja każdej z tych zasad jest odpowiedzialna za firmę Microsoft, klienta, czy współdzielona między nimi.

[Macierz implementacji magazynu danych NHS — Wielka Brytania](https://aka.ms/uknhs-dw-cim) zawiera informacje o tym, które wymagania dotyczące Wielkiej Brytanii NHS są rozwiązywane przez architekturę magazynu danych, w tym szczegółowy opis sposobu implementacji strategia zabezpieczeń i zgodności z przepisami platformy Azure spełnia wymagania każdej z tych zasad.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute

Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web PaaS. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a siecią wirtualną platformy Azure. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne &quot;przekazywanie&quot; informacji w ramach zaszyfrowanego połączenia między&#39;siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. [Tryb tunelowania IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w tej opcji jako mechanizm szyfrowania.

Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną, jeszcze bardziej bezpieczną opcję połączenia. Azure ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie z dostawcą telekomunikacyjnym klienta&#39;, dane nie są przesyłane przez Internet i w związku z tym nie są dostępne.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

### <a name="extract-transform-load-process"></a>Proces wyodrębniania i ładowania

[Baza](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) danych może ładować dane do Azure SQL Data Warehouse bez potrzeby oddzielnego narzędzia wyodrębniania, przekształcania, ładowania i importowania. Baza danych wielobase umożliwia dostęp do nich za poorednictwem zapytań T-SQL. Stos analizy biznesowej i analizy firmy Microsoft oraz narzędzia innych firm zgodne z SQL Server mogą być używane z bazą danych.

### <a name="azure-active-directory-setup"></a>Konfiguracja Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) ma zasadnicze znaczenie dla zarządzania wdrożeniem i zapewnianiem dostępu do pracowników, którzy współpracują ze środowiskiem. Istniejące Active Directory systemu Windows Server można zintegrować z Azure Active Directory za pomocą [czterech kliknięć](../../active-directory/hybrid/how-to-connect-install-express.md). Klienci mogą również powiązać wdrożoną infrastrukturę Active Directory (kontrolery domeny) z istniejącym Azure Active Directory przez nadanie wdrożonej infrastruktury Active Directory poddomeny lasu Azure Active Directory.

### <a name="optional-services"></a>Opcjonalne usługi

Platforma Azure oferuje różne usługi ułatwiające przechowywanie i przenoszenie sformatowanych i niesformatowanych danych. Do tej architektury referencyjnej można dodać następujące usługi, w zależności od wymagań klienta:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) to zarządzana usługa w chmurze, która jest oparta na złożonych projektach hybrydowych typu extract-transform-load i integracji danych. Azure Data Factory zawiera funkcje ułatwiające śledzenie i lokalizowanie danych związanych z kondycją, w tym narzędzia do wizualizacji i monitorowania, które umożliwiają identyfikowanie, kiedy dane dotarły i skąd pochodzą. Korzystając z Azure Data Factory, klienci mogą tworzyć i planować przepływy pracy oparte na danych nazywane potokami, które pozyskiwanie danych z różnych magazynów danych. Te potoki umożliwiają klientom pozyskiwanie danych ze źródeł wewnętrznych i zewnętrznych. Klienci mogą następnie przetwarzać i przekształcać dane wyjściowe w magazyny danych, takie jak Azure SQL Data Warehouse.
- Klienci mogą przemieszczać dane niestrukturalne w [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), co umożliwia przechwytywanie danych dowolnego rozmiaru, typu i szybkości wprowadzania w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.  Azure Data Lake zawiera funkcje, które umożliwiają wyodrębnianie i konwertowanie danych. Azure Data Lake Store jest zgodny z większością składników typu open source w ekosystemie usługi Hadoop i integruje dobrze z innymi usługami platformy Azure, takimi jak Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Zastrzeżenie

 - Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
 - Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
 - Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
 - Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
 - Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
 - Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
