---
title: Zabezpieczenia platformy Azure i zgodności planu - SP NIST 800-171 w magazynie danych
description: Zabezpieczenia platformy Azure i zgodności planu - SP NIST 800-171 w magazynie danych
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: a1850ecfbb21eb9495bb0e6de362dc8dee3026a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609577"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Zabezpieczenia platformy Azure i zgodności planu - SP NIST 800-171 w magazynie danych

## <a name="overview"></a>Omówienie
[Publikacja specjalne NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) zawiera wytyczne dotyczące ochrony informacji unclassified kontrolowanego (CUI), która znajduje się w organizacji i systemów informacyjnych nonfederal. SP NIST 800-171 ustanawia 14 rodzin wymagań dotyczących ochrony poufności CUI zabezpieczeń.

Tych samych zabezpieczeń platformy Azure i zgodności planu znajdują się wskazówki pomagające klientom wdrażanie architektura hurtowni danych, na platformie Azure, która implementuje podzbiór NIST SP 800-171 kontrolki. To rozwiązanie przedstawia sposób, w którym klienci mogą sprostać zabezpieczeń i zgodności z wymaganiami. Służy również jako podstawa do tworzenia i konfigurowanie własnych rozwiązań magazynu danych na platformie Azure.

Ta architektura referencyjna skojarzonego wdrożenia przewodnik i model zagrożeń są przeznaczone do służyć jako podstawa dla klientów dostosować je do swoich wymagań. Nie należy ich używać jako — w środowisku produkcyjnym. Klienci są zobowiązani do przeprowadzania odpowiednie ustawienia zabezpieczeń i zgodności oceny dowolnego rozwiązania utworzone przy użyciu tej architektury. Wymagania mogą się różnić w oparciu o szczegóły implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
To rozwiązanie zapewnia architektury referencyjnej, która implementuje magazyn danych w chmurze o wysokiej wydajności i bezpieczne. Istnieją dwie warstwy danych w ramach tej architektury. Jednej warstwy są gdzie dane są importowane, przechowywane i umieszczane w środowisku klastrowanym SQL. Jest innej warstwy usługi SQL data warehouse. W tej warstwie załadowaniu danych za pomocą narzędzia extract-transform-load (ETL) (na przykład [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) zapytań T-SQL) do przetworzenia. Po dane są przechowywane w usłudze SQL Data Warehouse, analizy można uruchomić na dużą skalę.

Platforma Azure oferuje szereg usług raportowania i analizy dla klienta. To rozwiązanie oferuje program SQL Server Reporting Services do szybkiego tworzenia raportów z usługi SQL data warehouse. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL przez dołączenie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem firma Microsoft zaleca korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.

Usługa Azure SQL Data Warehouse przechowuje dane w tabelach relacyjnych z magazynem kolumnowym. Ten format znacznie zmniejsza koszty przechowywania danych, podczas gdy zwiększa wydajność zapytań. W zależności od wymagań użycia zasobów obliczeniowych usługa SQL Data Warehouse można skalować w górę i w dół lub wyłączony całkowicie Jeśli brak aktywnych procesów wymaga zasobów obliczeniowych.

Moduł równoważenia obciążenia programu SQL Server zarządza ruchem SQL, aby zapewnić wysoką wydajność. Wszystkie maszyny wirtualne (VM) w tej architekturze referencyjnej w wdrożyć zestaw dostępności z wystąpienia programu SQL Server skonfigurowanych w zawsze włączonej grupy dostępności. Ta konfiguracja zapewnia funkcje wysokiej dostępności i odzyskiwania po awarii.

Ta architektura referencyjna magazynu danych obejmuje również warstwą usługi Active Directory do zarządzania zasobami w ramach architektury. Podsieć usługi Active Directory umożliwia łatwe wdrożenie w większych struktury lasu usługi Active Directory. W ten sposób środowiska mogą działać w sposób ciągły, nawet jeśli dostęp do większej lasu jest niedostępny. Wszystkie maszyny wirtualne są przyłączony do warstwy usługi Active Directory. Używają zasad grupy usługi Active Directory do wymuszania zabezpieczeń i zgodności konfiguracji na poziomie systemu operacyjnego.

Rozwiązanie używa konta usługi Azure Storage, w których klienci mogą skonfigurować na potrzeby zachowania poufności danych magazynowanych szyfrowanie usługi Storage. Azure przechowuje trzy kopie danych w obrębie centrum danych wybranego klienta pod kątem odporności. Magazyn geograficznie nadmiarowy gwarantuje, że dane są replikowane do dodatkowego centrum danych setki z mil natychmiast i ponownie przechowywane jako trzy kopie w tym centrum danych. To rozwiązanie zapobiega zdarzenie niepożądane, u klienta podstawowe centrum danych co spowoduje utratę danych.

Aby zwiększyć bezpieczeństwo wszystkie zasoby w tym rozwiązaniu są zarządzane jako grupa zasobów za pomocą usługi Azure Resource Manager. Usługa Azure Active Directory, kontrola dostępu oparta na rolach (Azure AD) (RBAC) służy do kontrolowania dostępu do wdrożonych zasobów. Te zasoby obejmują kluczy klienta w usłudze Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Security Center i Azure Monitor. Klienci, skonfigurować zarówno usług monitorowania do przechwytywania dzienników. Kondycja systemu jest wyświetlany na pojedynczy pulpit nawigacyjny, który jest łatwy w użyciu.

Maszyna wirtualna służy jako zarządzania hostem bastionu. Administratorzy mogą uzyskiwać dostęp do wdrożonych zasobów zapewnia bezpieczne połączenie. Załadowaniu danych do obszaru tymczasowego za pośrednictwem tego zarządzania hostem bastionu. *Firma Microsoft zaleca, aby skonfigurować połączenie sieci VPN lub usługi Azure ExpressRoute dla importu danych i zarządzania do podsieci architektury odwołanie.*

![Hurtownia danych programu NIST SP 800-171 referencyjny diagram architektury](images/nist171-dw-architecture.png "Hurtownia danych programu NIST SP 800-171 referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [architektura wdrożenia](#deployment-architecture) sekcji.

- Zestawy dostępności
    - Kontrolery domeny usługi Active Directory
    - Węzły klastra programu SQL Server i monitora
- Usługa Azure Active Directory
- Azure Data Catalog
- W usłudze Azure Key Vault
- Usługa Azure Monitor (Dzienniki)
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Virtual Machines
    - (1) host bastionu
    - (2) kontrolera domeny active Directory
    - (2) węzeł klastra programu SQL Server
    - (1) Monitor programu SQL Server
- Azure Virtual Network
    - (((1) /16 sieci
    - (4) prefiksie/24 sieci
    - (4) grupy zabezpieczeń sieci
- Magazyn usługi Recovery Services
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Azure SQL Data Warehouse**: [Usługa SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jest magazynu danych przedsiębiorstwa, który wykorzystuje przetwarzanie równoległe na wielką skalę w celu szybkiego uruchamiania złożonych zapytań obejmujących petabajty danych. Użytkownicy mogą używać prostych zapytań PolyBase T-SQL do importowania danych big data do usługi SQL data warehouse i umożliwia uruchamianie analizy o wysokiej wydajności możliwości przetwarzania równoległego na wielką skalę.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) umożliwia szybkie tworzenie raportów przy użyciu tabele, wykresy, mapy, mierniki, macierzach i bardziej usługi SQL Data Warehouse.

**Azure Data Catalog**: [Wykaz danych](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) sprawia, że źródła danych jest łatwe odnajdywanie i zrozumienie przez użytkowników, którzy zarządzają danymi. Typowe źródła danych można zarejestrowany, oznaczone i wyszukiwane dane. Dane pozostają w istniejącej lokalizacji, ale kopia ich metadanych jest dodawany do usługi Data Catalog. Odwołanie do lokalizacji źródła danych jest dołączony. Metadane są indeksowane, aby ułatwić odnajdywanie za pomocą wyszukiwania dla każdego źródła danych. Indeksowanie sprawia, że jej zrozumiałe dla użytkowników, którzy je odnajdą.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, którego użytkownicy mogą uzyskać dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów, zezwalając tylko na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Aby zezwolić na ruch pulpitu zdalnego, źródła ruchu musi być zdefiniowany w sieciowej grupie zabezpieczeń.

To rozwiązanie umożliwia utworzenie maszyny Wirtualnej jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) za pomocą usługi Key Vault.
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny Wirtualnej nie w użyciu.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) jest włączona, aby uruchomić w środowisku chronionego, która jest odizolowana poświadczeń i innych wpisów tajnych z systemem operacyjnym.

### <a name="virtual-network"></a>Sieć wirtualna
Ta architektura referencyjna definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresów 10.0.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) zawierają listy kontroli dostępu, które blokują lub zezwalają na ruch w sieci wirtualnej. Sieciowe grupy zabezpieczeń może służyć do zabezpieczenia ruchu na poziomie podsieci lub poszczególnych maszyn wirtualnych. Istnieją następujące sieciowe grupy zabezpieczeń:
  - Sieciowa grupa zabezpieczeń dla warstwy danych (klastrów programu SQL Server, monitora programu SQL Server i modułu równoważenia obciążenia SQL)
  - Sieciowa grupa zabezpieczeń do zarządzania hostem bastionu
  - Sieciowa grupa zabezpieczeń usługi Active Directory
  - Sieciowa grupa zabezpieczeń dla programu SQL Server Reporting Services

Każdy z sieciowych grup zabezpieczeń ma określone porty i protokoły Otwórz rozwiązanie może pracować bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej sieciowej grupy zabezpieczeń:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu.
  - Dzienniki platformy Azure Monitor jest podłączony do [diagnostyki sieciowej grupy zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Podsieci**: Każda podsieć jest skojarzone z jego odpowiedniego sieciowej grupy zabezpieczeń.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane za pomocą wielu miar. Środki te obejmują szyfrowania i inspekcja bazy danych.

**Azure Storage**: Aby spełnić wymagania dla zaszyfrowanych danych w spoczynku, wszystkie [magazynu](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ta funkcja pomaga chronić i ochrony danych na rzecz organizacji bezpieczeństwa i zgodności z wymaganiami.

**Usługa Azure Disk Encryption**: [Szyfrowanie dysków](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) używa funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. To rozwiązanie integruje się z usługą Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Azure SQL Database**: Wystąpienie bazy danych SQL wykorzystuje następujące środki bezpieczeństwa bazy danych:
-   [Uwierzytelnianie usługi Active Directory i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcji usługi SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
-   Baza danych SQL jest skonfigurowana do używania [technologii transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Wykonuje się w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, do ochrony informacji w stanie spoczynku. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywane dane, nie został narażone na nieautoryzowany dostęp.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania. Dostarcza alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i bazy danych nietypowe wzorce dostępu.
-   [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskać dostęp do danych w postaci zwykłego tekstu.
- [Właściwości rozszerzone](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) może służyć do zaprzestać przetwarzania podmiotów danych. Użytkownicy mogą dodawać właściwości niestandardowych do obiektów bazy danych. Mogą również oznaczać dane jako "Wycofany", do obsługi logiki aplikacji, aby uniemożliwić przetwarzanie skojarzone dane finansowe.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pozwala użytkownikom na definiowanie zasad, aby ograniczyć dostęp do danych, aby zaprzestać przetwarzania.
- [Baza danych SQL dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez maskowanie danych nieuprzywilejowanym użytkownikom lub aplikacji. Można automatycznie wykryć potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. Dynamiczne maskowanie danych pomaga ograniczyć dostęp, tak, aby dane poufne nie zakończyć bazy danych za pośrednictwem przed nieautoryzowanym dostępem. *Klienci są zobowiązani do dostosowywania ustawień stosować się do ich schemat bazy danych.*

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych w środowisku platformy Azure:
-   [Usługa Azure AD](https://azure.microsoft.com/services/active-directory/) jest wielodostępne oparte na chmurze zarządzania katalogami i tożsamościami usługę zarządzania Microsoft. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze Azure AD i obejmują użytkowników, którzy uzyskują dostęp z bazą danych SQL.
-   Do aplikacji uwierzytelniania za pomocą usługi Azure AD. Aby uzyskać więcej informacji, zobacz instrukcje [integrować aplikacje z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Szyfrowanie kolumny bazy danych również używa usługi Azure AD można uwierzytelnić aplikację do usługi SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Usługa Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) może służyć przez administratorów do definiowania uprawnień dostępu w zakresie. Dzięki niemu one można udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast co dostępu użytkownika bez ograniczeń dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do zasobów i danych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługa Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) może służyć przez klientów, aby zminimalizować liczbę użytkowników, którzy mają dostęp do pewnych informacji, takich jak dane. Administratorzy mogą używać usługi Azure AD Privileged Identity Management w celu odnajdywania, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można również wymusić na żądanie, just-in-time dostęp administracyjny w razie.
- [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykrywa potencjalnych luk w zabezpieczeniach, które mają wpływ na tożsamości w organizacji. Konfiguruje automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości w organizacji. Bada go również podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Key Vault ułatwia ochronę kluczy kryptograficznych i wpisów tajnych używanych przez aplikacje w chmurze i usług. Następujące funkcje usługi Key Vault pomagają chronić dane klientów:
- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń. Typ klucza jest sprzętu chronionych modułu zabezpieczeń 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień przy użyciu RBAC.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.

**Zarządzanie poprawkami**: Windows maszyn wirtualnych wdrożonych w ramach tej architektury referencyjnej są domyślnie skonfigurowane, automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To rozwiązanie obejmuje również [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą którego można utworzyć wdrożenia zaktualizowane maszynami wirtualnymi w razie zastosowania poprawki.

**Ochrona przed szkodliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, ułatwiający identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania. Klienci mogą skonfigurować alerty, które generują, gdy znany prób złośliwego lub niechcianego oprogramowania do zainstalowania lub uruchomienia na chronionych maszynach wirtualnych.

**Usługa Azure Security Center**: Za pomocą [usługi Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), klienci mogą centralnie zastosować i zarządzać zasadami zabezpieczeń na potrzeby różnych obciążeń, ograniczenia narażenia na zagrożenia i wykrywanie oraz reagowanie na ataki. Usługa Security Center również uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych.

Usługa Security Center korzysta z rozmaitych możliwości wykrywania powiadamia klientów o potencjalnych ataków, których platformą docelową ich środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Security Center zawiera zbiór [wstępnie zdefiniowane alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , są wyzwalane, gdy zagrożenia lub podejrzanej aktywności ma miejsce. Klienci mogą używać [niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) do definiowania nowych alertów zabezpieczeń, w oparciu o dane, które zostały już zebrane ze swoim środowisku.

Usługa Security Center zapewnia zdarzeń i alertów zabezpieczeń uporządkowanych według priorytetu. Usługa Security Center sprawia, że prostsze dla klientów odkryć i rozwiązać potencjalne problemy z zabezpieczeniami. A [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdej wykrytej zagrożeń. Odpowiedzi na zdarzenia zespołów mogą korzystać z raportów, gdy ich badaniu i usuwaniu zagrożeń.

Ta architektura referencyjna używa również [oceny luk w zabezpieczeniach](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) możliwości są dostępne w usłudze Security Center. Po skonfigurowaniu, agent partnera (na przykład Qualys) raportów danych luki w zabezpieczeniach do platformy zarządzania partnera. W odpowiedzi platforma zarządzania partnera dostarcza dane monitorowania o lukach w zabezpieczeniach i kondycji do usługi Security Center. Klienci mogą używać tych informacji, aby szybko identyfikować zagrożone maszyny wirtualne.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej
**Wysoka dostępność**: Obciążenia serwera są zgrupowane w [zestaw dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) do pomagają zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Co najmniej jedna maszyna wirtualna jest dostępna w przypadku zdarzenia planowaną lub nieplanowaną konserwacją i spełnia 99,95% umowy SLA platformy Azure.

**Magazyn usługi Recovery Services**: [Magazyn usługi Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych w ramach tej architektury. Przy użyciu magazynu usługi Recovery Services klienci mogą przywracać pliki i foldery z maszyny Wirtualnej IaaS bez przywracania całej maszyny Wirtualnej. Ten proces przyspiesza czas ich przywracania.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure często dziennika systemu i aktywności użytkownika, a także kondycji systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki magazynu, dzienników inspekcji usługi Key Vault i Dzienniki dostępu i zapory usługi Azure Application Gateway. Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Użytkownicy mogą skonfigurować okres przechowywania, nawet do 730 dni, aby spełnić konkretne wymagania.

**Dzienniki platformy Azure Monitor**: Te dzienniki i dalszych są skonsolidowane w [dzienniki usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym. Po zebraniu danych są zorganizowane w w oddzielnych tabelach dla każdego typu danych w obszarach roboczych usługi Log Analytics. W ten sposób wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Usługa Security Center integruje się z dziennikami usługi Azure Monitor. Klienci mogą używać zapytania Kusto dostęp do swoich danych zdarzeń zabezpieczeń i łączyć je z danymi z innych usług.

Następujące Azure [rozwiązania do monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) są uwzględniane w ramach tej architektury:
-   [Oceny usługi Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu. Zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu. Daje klientom z priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne. Zgłasza również odpowiadają liczby agentów i liczbę agentów, którzy przesłali danych operacyjnych.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

**Usługa Azure Automation**: [Automatyzacja](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowywane i zarządza elementami runbook i jest uruchamiany. W tym rozwiązaniu elementów runbook pomagają zbieranie dzienników z usługi SQL Database. Klienci mogą korzystać z automatyzacji [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) rozwiązania, aby łatwo identyfikować zmiany w środowisku.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów. Organizacje mogą używać go do inspekcji, tworzyć alerty i archiwizować dane. Mogą również śledzić wywołań interfejsu API w swoich zasobów platformy Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/nist171-dw-tm) lub można znaleźć tutaj. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu wprowadzasz zmiany.

![Hurtownia danych programu NIST SP 800-171 model zagrożeń](images/nist171-dw-threat-model.png "Hurtownia danych programu NIST SP 800-171 model zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Zabezpieczeń platformy Azure i zgodności planu — macierz odpowiedzialności klienta SP NIST 800-171](https://aka.ms/nist171-crm) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez SP NIST 800-171. Ta macierz szczegółowe informacje, czy implementacja każdej kontrolki jest odpowiedzialność firmy Microsoft, klientów, lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i zgodności planu - macierzy implementacji kontroli magazynu danych SP NIST 800-171](https://aka.ms/nist171-dw-cim) zawiera informacje, na które SP NIST 800-171 kontrolki są objęte architektura hurtowni danych. Zawiera szczegółowy opis sposobu wdrożenia spełnia wymagania każdego formantu objętego usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowana do bezpieczne nawiązywanie połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej magazynu danych. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można tworzyć wirtualne prywatne połączenie między siecią lokalną a siecią wirtualną platformy Azure. To połączenie odbywa się za pośrednictwem Internetu. Klienci mogą używać tego połączenia o bezpiecznie "tuneli" wewnątrz zaszyfrowane połączenia między siecią i platformą Azure przez klienta. Sieci VPN typu lokacja lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu sieci VPN jest przesyłane przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje inną opcję połączenia jeszcze bardziej bezpieczne. Usługa ExpressRoute jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Połączenia ExpressRoute łączyć się bezpośrednio z dostawcą usług telekomunikacyjnych klienta. W rezultacie dane nie podróżują, przez Internet i nie jest widoczne. Połączenia te oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Proces wyodrębniania, przekształcania, ładowania
[Program PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) można załadować dane do usługi SQL Data Warehouse bez konieczności stosowania osobnych ETL lub zaimportować narzędzia. Program PolyBase umożliwia dostęp do danych za pomocą zapytań T-SQL. Stos analizy i analizy biznesowej firmy Microsoft oraz narzędzi innych firm, które są zgodne z programem SQL Server może służyć przy użyciu technologii PolyBase.

### <a name="azure-ad-setup"></a>Konfiguracja usługi Azure AD
[Usługa Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest Zarządzanie wdrożeniem i inicjowania obsługi dostępu do personelu wchodzenie w interakcje ze środowiskiem. Lokalne usługi Active Directory można zintegrować z usługą Azure AD w [cztery kliknięcia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Klientów może także powiązać wdrożonej infrastruktura usługi Active Directory (kontrolery domeny) do usługi Azure AD. Aby to zrobić, należy wdrożonej infrastrukturę usługi Active Directory poddomeny las usługi AD systemu Azure.

### <a name="optional-services"></a>Usługi opcjonalne
Platforma Azure oferuje szereg usług na potrzeby magazynu i przemieszczania danych sformatowanego i niesformatowane. Następujące usługi mogą być dodawane do tej architektury referencyjnej, w zależności od wymagań klientów:
-   [Usługa Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) to usługa zarządzana usługa w chmurze, która została stworzona z myślą złożone projekty integracji danych hybrydowych ETL, wyodrębnianie ładowanie transformacja i. Data Factory dysponuje możliwości śledzenia i umieszczanie danych. Wizualizacja i narzędzia do monitorowania zidentyfikować po źródła otrzymanych danych, a w przypadku, gdy dana aplikacja pochodzi. Klientów można tworzyć i zaplanować opartych na danych przepływów pracy nazywanych potoki, które pozyskiwać dane z różnych magazynów danych. One umożliwia potoków pozyskiwania danych ze źródeł wewnętrznych i zewnętrznych. Klienci, a następnie jest przetwarzanie i przekształcanie danych dla danych wyjściowych w magazynach danych, takich jak usługa SQL Data Warehouse.
- Klientów można umieścić dane bez struktury w [usługi Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) umożliwia przechwytywanie danych dowolnego rozmiaru, typu i szybkości wprowadzania w miejscu na potrzeby analizy operacyjnej i rozpoznawczej. Usługa Azure Data Lake ma funkcje, które umożliwiają wyodrębnianie i konwersja danych. Data Lake Store jest zgodna z najbardziej otwarta składniki źródła w ekosystemie usługi Hadoop. On również bardzo dobrze integruje się z innymi usługami platformy Azure, takich jak usługa SQL Data Warehouse.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
 - W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
 - Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
 - Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
