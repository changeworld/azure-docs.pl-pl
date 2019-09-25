---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — magazyn danych dla NIST SP 800-171
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — magazyn danych dla NIST SP 800-171
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: d5c7ab3cc8d4de788da5d18f31c6aacb31b5d551
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259881"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure — magazyn danych dla NIST SP 800-171

## <a name="overview"></a>Przegląd
[Specjalna publikacja NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) zawiera wytyczne dotyczące ochrony kontrolowanych niesklasyfikowanych informacji (CUI), które znajdują się w niefederalnych systemach i organizacjach informacyjnych. NIST SP 800-171 ustanawia 14 rodzin wymagań dotyczących zabezpieczeń, aby chronić poufność CUI.

Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera wskazówki ułatwiające klientom wdrożenie architektury magazynu danych na platformie Azure, która implementuje podzestaw formantów NIST SP 800-171. W tym rozwiązaniu pokazano, w jaki sposób klienci mogą spełniać określone wymagania w zakresie zabezpieczeń i zgodności. Służy ona również jako podstawa dla klientów do kompilowania i konfigurowania własnych rozwiązań magazynu danych na platformie Azure.

Ta architektura referencyjna, skojarzony Przewodnik implementacji oraz model zagrożeń mają służyć jako podstawa dla klientów, którzy dostosowują się do określonych wymagań. Nie powinny być używane w środowisku produkcyjnym. Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury. Wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki
To rozwiązanie zapewnia architekturę referencyjną, która implementuje magazyn danych w chmurze o wysokiej wydajności i bezpiecznych. W tej architekturze są dwie osobne warstwy danych. Jedna warstwa to miejsce, w którym dane są importowane, przechowywane i przemieszczane w ramach klastrowanego środowiska SQL. Inna warstwa dotyczy usługi SQL Data Warehouse. W przypadku tej warstwy dane są ładowane przy użyciu narzędzia Extract-transform-load (ETL) (na przykład [zapytania T-](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) SQL) w celu przetworzenia. Gdy dane są przechowywane w SQL Data Warehouse, analiza może być uruchamiana na dużą skalę.

Platforma Azure oferuje różne usługi raportowania i analiz dla klienta. To rozwiązanie zawiera SQL Server Reporting Services do szybkiego tworzenia raportów z usługi SQL Data Warehouse. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL w ramach dołączania certyfikatów z podpisem własnym. Najlepszym rozwiązaniem jest użycie zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.

Azure SQL Data Warehouse przechowuje dane w tabelach relacyjnych z magazynem kolumnowym. Ten format znacznie zmniejsza koszty magazynowania danych, podczas gdy zwiększa wydajność zapytań. W zależności od wymagań dotyczących użycia SQL Data Warehouse zasoby obliczeniowe mogą być skalowane w górę lub w dół lub wyłączane całkowicie, jeśli żadne aktywne procesy nie wymagają zasobów obliczeniowych.

Moduł równoważenia obciążenia SQL Server zarządza ruchem SQL w celu zapewnienia wysokiej wydajności. Wszystkie maszyny wirtualne w tej architekturze referencyjnej są wdrażane w zestawie dostępności z wystąpieniami SQL Server skonfigurowanymi w zawsze włączone grupy dostępności. Ta konfiguracja zapewnia możliwości wysokiej dostępności i odzyskiwania po awarii.

Ta architektura referencyjna magazynu danych obejmuje również warstwę Active Directory na potrzeby zarządzania zasobami w ramach architektury. Podsieć Active Directory umożliwia łatwe wdrażanie w ramach większej Active Directory struktury lasu. Dzięki temu środowisko może działać w sposób ciągły, nawet jeśli dostęp do większego lasu jest niedostępny. Wszystkie maszyny wirtualne są przyłączone do warstwy Active Directory. Używają Active Directory zasad grupy do wymuszania konfiguracji zabezpieczeń i zgodności na poziomie systemu operacyjnego.

Rozwiązanie korzysta z kont usługi Azure Storage, które mogą być szyfrowanie usługi Storage konfigurowane przez klientów w celu zachowania poufności danych przechowywanych w spoczynku. Platforma Azure przechowuje trzy kopie danych w wybranym centrum danych klienta pod kątem odporności. Magazyn Geograficznie nadmiarowy gwarantuje, że dane są replikowane do pomocniczego centrum danych w setkach kilometrów i ponownie przechowywane jako trzy kopie w tym centrum danych. To rozwiązanie zapobiega niekorzystnemu zdarzeniu w podstawowym centrum danych klienta, co spowodowało utratę danych.

W celu zwiększenia bezpieczeństwa wszystkie zasoby w tym rozwiązaniu są zarządzane jako Grupa zasobów za pomocą Azure Resource Manager. Usługa Azure Active Directory (Azure AD) Kontrola dostępu oparta na rolach (RBAC) służy do kontrolowania dostępu do wdrożonych zasobów. Te zasoby obejmują klucze klienta w Azure Key Vault. Kondycja systemu jest monitorowana za poorednictwem Azure Security Center i Azure Monitor. Klienci konfigurują obie usługi monitorowania do przechwytywania dzienników. Kondycja systemu jest wyświetlana na jednym pulpicie nawigacyjnym łatwym w użyciu.

Maszyna wirtualna służy jako host bastionu zarządzania. Zapewnia to bezpieczne połączenie dla administratorów umożliwiające dostęp do wdrożonych zasobów. Dane są ładowane do obszaru przejściowego za pomocą tego hosta zarządzania bastionu. *Zalecamy skonfigurowanie połączenia sieci VPN lub usługi Azure ExpressRoute na potrzeby zarządzania i importowania danych do podsieci architektury referencyjnej.*

![Magazyn danych dla programu NIST SP 800-171 diagram architektury referencyjnej](images/nist171-dw-architecture.png "Magazyn danych dla programu NIST SP 800-171 diagram architektury referencyjnej")

To rozwiązanie używa następujących usług platformy Azure. Aby uzyskać więcej informacji, zobacz sekcję [architektury wdrożenia](#deployment-architecture) .

- Zestawy dostępności
    - Active Directory kontrolery domeny
    - SQL Server węzły klastra i monitor
- Usługa Azure Active Directory
- Azure Data Catalog
- W usłudze Azure Key Vault
- Azure Monitor (dzienniki)
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Usługa Azure Virtual Machines
    - (1) Host bastionu
    - (2) Active Directory kontroler domeny
    - (2) SQL Server węzeł klastra
    - (1) monitor SQL Server
- Azure Virtual Network
    - (1)/16 sieci
    - (4)/24 sieci
    - (4) sieciowe grupy zabezpieczeń
- magazyn usług Recovery Services
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

**Azure SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) to magazyn danych przedsiębiorstwa, który wykorzystuje wysoce równoległe przetwarzanie, aby szybko uruchamiać złożone zapytania w petabajtów danych. Użytkownicy mogą używać prostych zapytań T-SQL w celu zaimportowania danych Big Data do usługi SQL Data Warehouse i użycia zaawansowanego przetwarzania równoległego w celu uruchomienia analizy o wysokiej wydajności.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) umożliwia szybkie tworzenie raportów za pomocą tabel, wykresów, map, mierników, macierzy i innych elementów SQL Data Warehouse.

**Azure Data Catalog**: [Data Catalog](../../data-catalog/overview.md) ułatwiają odnajdywanie źródeł danych i zrozumienie ich przez użytkowników, którzy zarządzają danymi. Typowe źródła danych mogą być rejestrowane, otagowane i przeszukiwane pod kątem danych. Dane pozostają w istniejącej lokalizacji, ale kopia jej metadanych zostanie dodana do Data Catalog. Dołączono odwołanie do lokalizacji źródła danych. Metadane są indeksowane, aby ułatwić odnajdywanie każdego źródła danych za pośrednictwem wyszukiwania. Indeksowanie sprawia również, że są zrozumiałe dla użytkowników, którzy je odnajdują.

**Host bastionu**: Host bastionu to pojedynczy punkt wprowadzania, za pomocą którego użytkownicy mogą uzyskać dostęp do wdrożonych zasobów w tym środowisku. Host bastionu zapewnia bezpieczne połączenie ze wdrożonymi zasobami, zezwalając na bezpieczną listę tylko ruchu zdalnego z publicznych adresów IP. Aby zezwolić na ruch pulpitu zdalnego, źródło ruchu musi być zdefiniowane w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako przyłączony do domeny Host bastionu z następującymi konfiguracjami:
-   [Rozszerzenie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Diagnostyka Azure rozszerzenie](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) przy użyciu Key Vault.
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) w celu ograniczenia użycia zasobów maszyn wirtualnych, gdy nie są używane.
-   [Funkcja Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) jest włączona, aby poświadczenia i inne wpisy tajne były uruchamiane w chronionym środowisku, które jest odizolowane od działającego systemu operacyjnego.

### <a name="virtual-network"></a>Sieć wirtualna
Ta architektura referencyjna definiuje prywatną sieć wirtualną z przestrzenią adresową 10.0.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (Sieciowych grup zabezpieczeń) zawierają listy kontroli dostępu, które zezwalają na ruch w sieci wirtualnej lub odmawiają go. Sieciowych grup zabezpieczeń może służyć do zabezpieczania ruchu w podsieci lub na poziomie poszczególnych maszyn wirtualnych. Następująca sieciowych grup zabezpieczeń istnieje:
  - SIECIOWEJ grupy zabezpieczeń dla warstwy danych (klastrów SQL Server, monitora SQL Server i modułu równoważenia obciążenia SQL)
  - SIECIOWEJ grupy zabezpieczeń dla hosta zarządzania bastionu
  - SIECIOWEJ grupy zabezpieczeń dla Active Directory
  - SIECIOWEJ grupy zabezpieczeń dla SQL Server Reporting Services

Każdy z sieciowych grup zabezpieczeń ma określone porty i protokoły, dzięki czemu rozwiązanie może być bezpieczne i poprawne. Ponadto dla każdego sieciowej grupy ZABEZPIECZEŃu są włączone następujące konfiguracje:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu.
  - Dzienniki Azure Monitor są połączone z [diagnostyką sieciowej grupy zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Podsieci**: Każda podsieć jest skojarzona z odpowiadającą jej sieciowej grupy zabezpieczeń.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane przechowywane przez wiele miar. Te miary obejmują szyfrowanie i inspekcję bazy danych.

**Azure Storage**: Aby spełnić wymagania dotyczące szyfrowanych danych przechowywanych w magazynie, wszystkie [magazyny](https://azure.microsoft.com/services/storage/) są używane [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md). Ta funkcja pomaga chronić i zabezpieczać dane w celu zapewnienia obsługi wymagań w zakresie bezpieczeństwa organizacji i zgodności.

**Azure Disk Encryption**: [Szyfrowanie dysków](../azure-security-disk-encryption-overview.md) używa funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla systemu operacyjnego i dysków danych. Rozwiązanie integruje się z Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**Azure SQL Database**: W wystąpieniu SQL Database są stosowane następujące miary zabezpieczeń bazy danych:
-   [Active Directory uwierzytelnianie i autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwiają zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja SQL Database](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
-   SQL Database jest skonfigurowany do używania [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Wykonuje szyfrowanie w czasie rzeczywistym oraz odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, aby chronić informacje przechowywane w pamięci podręcznej. Przezroczyste szyfrowanie danych zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](../../sql-database/sql-database-threat-detection.md) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania. Zapewnia alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalnych luk w zabezpieczeniach, ataków iniekcji SQL i nietypowych wzorców dostępu do bazy danych.
-   [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [Właściwości rozszerzone](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) mogą służyć do zaprzestania przetwarzania podmiotów danych. Użytkownicy mogą dodawać niestandardowe właściwości do obiektów bazy danych. Mogą także oznaczać, że dane są oznaczane jako "nieobsługiwane" w celu zapewnienia obsługi logiki aplikacji w celu zapobiegania przetwarzaniu skojarzonych danych finansowych.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umożliwiają użytkownikom Definiowanie zasad ograniczających dostęp do danych w celu ich dalszego przetwarzania.
- [SQL Database Dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza narażenie na dane poufne przez zamaskowanie danych do nieuprzywilejowanych użytkowników lub aplikacji. Może automatycznie odnajdywać potencjalnie wrażliwe dane i zasugerować odpowiednie maski, które mają zostać zastosowane. Dynamiczne maskowanie danych pomaga ograniczyć dostęp, tak aby dane poufne nie wyłączać bazy danych za pośrednictwem nieautoryzowanego dostępu. *Klienci są odpowiedzialni za dostosowanie ustawień, aby były zgodne ze schematem bazy danych.*

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości zarządzania dostępem do danych w środowisku platformy Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) to usługa zarządzania katalogiem i tożsamościami opartymi na chmurze firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzeniu w usłudze Azure AD i obejmują użytkowników, którzy uzyskują dostęp do bazy danych SQL.
-   Uwierzytelnianie w aplikacji odbywa się przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz jak [zintegrować aplikacje z usługą Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Szyfrowanie kolumny bazy danych używa również usługi Azure AD do uwierzytelniania aplikacji w SQL Database. Aby uzyskać więcej informacji, zobacz jak [chronić poufne dane w SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Administratorzy [systemu Azure](../../role-based-access-control/role-assignments-portal.md) mogą używać administratorów do definiowania szczegółowych uprawnień dostępu. Z działem IT mogą przyznawać tylko dostęp, który użytkownicy potrzebują do wykonywania swoich zadań. Zamiast zapewniać wszystkim użytkownikom nieograniczony dostęp do zasobów platformy Azure, Administratorzy mogą zezwalać na dostęp do zasobów i danych tylko określonym akcjom. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) mogą być używane przez klientów w celu zminimalizowania liczby użytkowników, którzy mają dostęp do określonych informacji, takich jak dane. Administratorzy mogą używać Azure AD Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości organizacji. Służy do konfigurowania automatycznych odpowiedzi w celu wykrycia podejrzanych działań związanych z tożsamościami organizacji. Bada także podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Key Vault pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi w chmurze. Następujące funkcje Key Vault pomagają klientom chronić dane:
- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza jest sprzętowego sprzętu zabezpieczenia-chroniony przez moduł 2048-bitowy klucz RSA.
- Wszyscy użytkownicy i tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu RBAC.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.

**Zarządzanie poprawkami**: Maszyny wirtualne z systemem Windows wdrożone w ramach tej architektury referencyjnej są domyślnie skonfigurowane do odbierania automatycznych aktualizacji z usługi Windows Update. To rozwiązanie obejmuje również usługę [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , za pomocą której można tworzyć zaktualizowane wdrożenia, aby zastosować poprawki do maszyn wirtualnych w razie potrzeby.

**Ochrona przed złośliwym oprogramowaniem**: [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania. Klienci mogą konfigurować alerty, które generują, kiedy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować lub uruchomić na chronionych maszynach wirtualnych.

**Azure Security Center**: Dzięki [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Security Center również uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.

Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków, które są ukierunkowane na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Security Center zawiera zestaw [wstępnie zdefiniowanych alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , które są wyzwalane w przypadku wystąpienia zagrożeń lub podejrzanych działań. Klienci mogą używać [niestandardowych reguł alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w celu definiowania nowych alertów zabezpieczeń opartych na danych, które zostały już zebrane z ich środowiska.

Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia. Security Center ułatwia klientom odnajdywanie potencjalnych problemów z zabezpieczeniami i ich rozwiązywanie. Dla każdego wykrytego zagrożenia jest generowany [Raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) . Zespoły reagowania na incydenty mogą korzystać z raportów podczas badania i korygowania zagrożeń.

Ta architektura referencyjna używa również funkcji [oceny luk w zabezpieczeniach](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) w Security Center. Po jego skonfigurowaniu Agent partnera (na przykład Qualys) zgłasza luki w zabezpieczeniach do platformy zarządzania partnera. W odpowiedzi platforma zarządzania partnera dostarcza dane monitorowania o lukach w zabezpieczeniach i kondycji do usługi Security Center. Klienci mogą używać tych informacji, aby szybko identyfikować podatne na nie maszyny wirtualne.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej
**Wysoka dostępność**: Obciążenia serwera są pogrupowane w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , aby zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Podczas planowanego lub nieplanowanego zdarzenia konserwacji jest dostępna co najmniej jedna maszyna wirtualna, która spełnia warunki umowy SLA na 99,95%.

**Magazyn Recovery Services**: [Magazyn Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowych i chroni wszystkie konfiguracje maszyn wirtualnych w tej architekturze. W przypadku magazynu Recovery Services klienci mogą przywracać pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej. Ten proces przyspiesza czas przywracania.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/resource-logs-overview.md) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Te dzienniki obejmują Dzienniki systemu Windows, dzienniki magazynu, dzienniki inspekcji Key Vault i usługi Azure Application Gateway Access i zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Użytkownicy mogą skonfigurować okres przechowywania (do 730 dni) w celu spełnienia określonych wymagań.

**Azure monitor dzienników**: Te dzienniki są konsolidowane w [Azure monitor dziennikach](https://azure.microsoft.com/services/log-analytics/) na potrzeby przetwarzania, przechowywania i raportowania na pulpicie nawigacyjnym. Po zebraniu danych są one podzielone na oddzielne tabele dla każdego typu danych w obszarze obszary robocze Log Analytics. W ten sposób wszystkie dane można analizować razem niezależnie od oryginalnego źródła. Security Center integruje się z dziennikami Azure Monitor. Klienci mogą używać zapytań Kusto, aby uzyskać dostęp do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

Następujące rozwiązania do [monitorowania](../../monitoring/monitoring-solutions.md) platformy Azure są dostępne w ramach tej architektury:
-   [Ocena Active Directory](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie do sprawdzania kondycji Active Directory ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu. Zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Ocena SQL](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie sprawdzania kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu. Zapewnia ona klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną. Raporty te również przedstawiają liczbę agentów, które nie odpowiadają, oraz liczbę agentów przesyłających dane operacyjne.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

**Azure Automation**: [Usługi Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowują elementy Runbook, uruchamiają je i zarządzają nimi. W tym rozwiązaniu elementy Runbook ułatwiają zbieranie dzienników z SQL Database. Klienci mogą korzystać z rozwiązania [Change Tracking](../../automation/change-tracking.md) Automation, aby łatwo identyfikować zmiany w środowisku.

**Azure monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów. Organizacje mogą używać go do inspekcji, tworzenia alertów i archiwizowania danych. Mogą również śledzić wywołania interfejsu API w swoich zasobach platformy Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/nist171-dw-tm) lub można go znaleźć tutaj. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![Magazyn danych dla modelu zagrożeń NIST SP 800-171](images/nist171-dw-threat-model.png "Magazyn danych dla modelu zagrożeń NIST SP 800-171")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności
W [zestawie strategia zabezpieczeń i zgodności z przepisami platformy Azure — NIST sp 800-171 Customer odpowiedzialność klienta](https://aka.ms/nist171-crm) znajduje się lista wszystkich kontroli zabezpieczeń wymaganych przez instytut NIST SP 800-171. Ta macierz zawiera szczegółowe informacje o tym, czy implementacja poszczególnych kontroli jest odpowiedzialna za firmę Microsoft, klienta, czy współdzielona między nimi.

[Macierz implementacji kontroli hurtowni danych w programie strategia zabezpieczeń i zgodności z przepisami platformy Azure-NIST sp 800-171](https://aka.ms/nist171-dw-cim) zawiera informacje o tym, które kontrolki NIST SP 800-171 są objęte architekturą magazynu danych. Zawiera szczegółowy opis sposobu, w jaki implementacja spełnia wymagania poszczególnych formantów objętych usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej magazynu danych. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a siecią wirtualną platformy Azure. To połączenie odbywa się za pośrednictwem Internetu. Klienci mogą używać tego połączenia, aby bezpiecznie "tunelować" w ramach zaszyfrowanego połączenia między siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. [Tryb tunelowania IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w tej opcji jako mechanizm szyfrowania.

Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną jeszcze bezpieczną opcję połączenia. ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Połączenia ExpressRoute nawiązują połączenie bezpośrednio z dostawcą telekomunikacyjnym klienta. W związku z tym dane nie podróżują przez Internet i nie są w nim ujawniane. Połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

### <a name="extract-transform-load-process"></a>Proces wyodrębniania i ładowania
[Baza](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) danych może ładować dane do SQL Data Warehouse bez potrzeby oddzielnego narzędzia ETL lub importu. Baza danych wielobase umożliwia dostęp do nich za poorednictwem zapytań T-SQL. Stos analizy biznesowej firmy Microsoft i narzędzia innych firm, które są zgodne z SQL Server mogą być używane z bazą.

### <a name="azure-ad-setup"></a>Konfiguracja usługi Azure AD
[Usługa Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) jest niezwykle ważna do zarządzania wdrożeniem i zapewnianiem dostępu do pracowników, którzy współpracują ze środowiskiem. Active Directory lokalnych można zintegrować z usługą Azure AD za pomocą [czterech kliknięć](../../active-directory/hybrid/how-to-connect-install-express.md). Klienci mogą również powiązać wdrożoną infrastrukturę Active Directory (kontrolery domeny) z usługą Azure AD. W tym celu należy wprowadzić wdrożoną infrastrukturę Active Directory poddomeną lasu usługi Azure AD.

### <a name="optional-services"></a>Opcjonalne usługi
Platforma Azure oferuje różne usługi ułatwiające przechowywanie i przenoszenie sformatowanych i niesformatowanych danych. Następujące usługi można dodać do tej architektury referencyjnej na podstawie wymagań klienta:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) to zarządzana usługa w chmurze, która jest oparta na złożonych projektach hybrydowych, wyodrębniających, przekształceń i integracji danych. Data Factory zawiera funkcje ułatwiające śledzenie i lokalizowanie danych. Narzędzia do wizualizacji i monitorowania określają, kiedy dane dotarły i skąd pochodzą. Klienci mogą tworzyć i planować przepływy pracy oparte na danych, nazywane potokami, które pobierają dane z różnych magazynów danych. Mogą używać potoków do pozyskiwania danych ze źródeł wewnętrznych i zewnętrznych. Klienci mogą następnie przetwarzać i przekształcać dane wyjściowe w magazyny danych, takie jak SQL Data Warehouse.
- Klienci mogą przemieszczać dane bez struktury w [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) , aby przechwytywać dane dowolnego rozmiaru, typu i szybkości wprowadzania w jednym miejscu na potrzeby analiz operacyjnych i poznawczych. Azure Data Lake zawiera funkcje, które umożliwiają wyodrębnianie i konwertowanie danych. Data Lake Store jest zgodny z większością składników Open Source w ekosystemie usługi Hadoop. Integruje się ona również z dobrze innymi usługami platformy Azure, takimi jak SQL Data Warehouse.

## <a name="disclaimer"></a>Zastrzeżenie

 - Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
 - Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
 - Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
 - Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
 - Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
 - Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
