---
title: Zabezpieczenia platformy Azure i plan zgodność — PCI DSS w magazynie danych
description: Zabezpieczenia platformy Azure i plan zgodność — PCI DSS w magazynie danych
services: security
author: meladie
ms.assetid: 6d29e5bf-6cd0-4cda-bbd8-59f283b0c86c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 7704d9124846c0c6c82728031327990f8f9b1cf6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608206"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-pci-dss"></a>Zabezpieczenia platformy Azure i zgodności planu: Data Warehouse, PCI DSS

## <a name="overview"></a>Omówienie

Tych samych zabezpieczeń platformy Azure i zgodności plan zawiera wskazówki dotyczące wdrażania architektury magazynu danych na platformie Azure, które pomaga wymagania płatności Karta Industry Data Security Standards (PCI DSS 3.2). On przedstawiono typowe architektury referencyjnej i przedstawia prawidłowego przygotowania danych karty kredytowej (w tym karty numer, wygaśnięcia i weryfikacji danych) w środowisku bezpiecznych, zgodnych i wieloma warstwami. Ten plan pokazuje sposób, w którym klienci mogą sprostać określonych wymagań dotyczących zabezpieczeń i zgodności i służy jako podstawa do tworzenia i konfigurowanie własnych rozwiązań magazynu danych na platformie Azure.

Ta architektura referencyjna przewodnik wdrażania i model zagrożeń Podaj foundation umożliwiające klientom zachowanie zgodności z wymaganiami PCI DSS 3.2. To rozwiązanie zapewnia plan bazowy, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny ze standardami PCI DSS 3.2; Jednak to rozwiązanie nie jest używane jako — jest w środowisku produkcyjnym, ponieważ wymagana jest dodatkowa konfiguracja.

Osiągnięcie zgodności ze standardem PCI DSS wymaga, że akredytowanych kwalifikowaną zabezpieczeń rzeczoznawcę ds (QSA) certyfikować rozwiązania klienta produkcyjnego. Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników

To rozwiązanie zapewnia architekturę referencyjną, która implementuje magazyn danych w chmurze o wysokiej wydajności i bezpieczne. Istnieją dwie warstwy danych w ramach tej architektury: jeden w którym dane są importowane, przechowywane i umieszczane w środowisku klastrowanym SQL, a drugi dla usługi Azure SQL Data Warehouse gdzie dane są ładowane za pomocą do wyodrębniania, przekształcania, ładowania narzędzia (np. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) zapytań T-SQL) do przetworzenia. Gdy dane są przechowywane w usłudze Azure SQL Data Warehouse, analizy można uruchomić na dużą skalę.

Platforma Azure oferuje szereg usług raportowania i analizy dla klienta. To rozwiązanie oferuje program SQL Server Reporting Services (SSRS) do szybkiego tworzenia raportów z usługi Azure SQL Data Warehouse. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL przez dołączenie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem jest Azure zaleca się korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.

Dane w usłudze Azure SQL Data Warehouse są przechowywane w tabelach relacyjnych z magazynem kolumnowym formatu, który znacznie zmniejsza koszty przechowywania danych przy jednoczesnym zwiększeniu wydajności zapytań. W zależności od wymagań użycia usługi Azure SQL Data Warehouse zasoby obliczeniowe można skalować w górę i w dół lub wyłączony całkowicie przypadku Brak aktywnych procesów wymagających zasoby obliczeniowe.

Moduł równoważenia obciążenia SQL zarządza ruch SQL zapewniające wysoką wydajność. Wszystkie maszyny wirtualne w tej architekturze referencyjnej w wdrożyć zestaw dostępności z wystąpienia programu SQL Server skonfigurowanych w zawsze włączonej grupy dostępności funkcji wysokiej dostępności i odzyskiwania po awarii.

Ta architektura referencyjna magazynu danych obejmuje również warstwą usługi Active Directory do zarządzania zasobami w ramach architektury. Podsieć usługi Active Directory umożliwia łatwe przyjęcie w ramach większe struktury lasu usługi Active Directory, co do pracy ciągłej, środowiska, nawet wtedy, gdy dostęp do większej lasu jest niedostępny. Wszystkie maszyny wirtualne są przyłączone do domeny do warstwy usługi Active Directory i wymuszanie konfiguracji zabezpieczeń i zgodności na poziomie systemu operacyjnego za pomocą zasad grupy usługi Active Directory.

Rozwiązanie używa konta usługi Azure Storage, w których klienci mogą skonfigurować na potrzeby zachowania poufności danych magazynowanych szyfrowanie usługi Storage. Azure przechowuje trzy kopie danych w ramach wybranego centrum danych klienta pod kątem odporności. Geograficzne magazyn nadmiarowy gwarantuje danych replikowana do pomocniczego centrum danych setki mil natychmiast i ponownie przechowywane jako trzy kopie w ramach tego centrum danych, uniemożliwiając zdarzenie niepożądane, u klienta podstawowe centrum danych co spowoduje utratę dane.

Aby zwiększyć bezpieczeństwo wszystkie zasoby w tym rozwiązaniu są zarządzane jako grupa zasobów za pomocą usługi Azure Resource Manager. Usługa Azure Active Directory, kontrola dostępu oparta na rolach jest używana do kontrolowania dostępu do wdrożonych zasobów, w tym do kluczy w usłudze Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Security Center i Azure Monitor. Klienci, skonfigurować zarówno usług monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwo można nawigować pulpitu nawigacyjnego.

Maszyna wirtualna służy jako zarządzania hostem bastionu, zapewnienia bezpiecznego połączenia dla administratorów, które uzyskują dostęp do wdrożonych zasobów. Załadowaniu danych do obszaru tymczasowego za pośrednictwem tego zarządzania hostem bastionu. **Firma Microsoft zaleca skonfigurowanie połączenia sieci VPN lub usługi Azure ExpressRoute do importowania danych i zarządzania do podsieci architektury odwołanie.**

![Data Warehouse, PCI DSS referencyjny diagram architektury](images/pcidss-dw-architecture.png "magazynu danych na potrzeby PCI DSS referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Szczegółowe informacje na temat architektury wdrożenia [architektura wdrożenia](#deployment-architecture) sekcji.

- Zestawy dostępności
    - Active Directory Domain Controllers
    - Węzły klastra SQL i monitora
- Usługa Azure Active Directory
- Azure Data Catalog
- W usłudze Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Virtual Machines
    - (1) Host bastionu
    - (2) kontrolera domeny active Directory
    - (2) węzeł klastra serwera SQL
    - (1) Monitor serwera SQL
- Azure Virtual Network
    - (1) /16 Network
    - (4) /24 Networks
    - (4) grupy zabezpieczeń sieci
- Magazyn usługi Recovery Services
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Usługa SQL Data Warehouse**: [Usługa SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jest Enterprise danych magazynu (EDW), który wykorzystuje wysoce równoległe przetwarzanie (MPP) do szybkiego uruchamiania złożonych zapytań obejmujących petabajty danych, zezwalać użytkownikom na identyfikację efektywnie danych finansowych. Użytkownicy mogą używać prostych zapytań PolyBase T-SQL, aby zaimportować dane big data do usługi SQL Data Warehouse i korzystanie z zaawansowanych funkcji architektury MPP do uruchomienia analizy o wysokiej wydajności.

**SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) umożliwia szybkie tworzenie raportów przy użyciu tabele, wykresy, mapy, mierniki, macierzach i bardziej dla usługi Azure SQL Data Warehouse.

**Data Catalog**: [Wykaz danych](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) sprawia, że źródła danych łatwe do zrozumienia i prostsze do odnalezienia przez użytkowników, którzy zarządzają danymi. Wspólnych źródeł danych można zarejestrować, oznaczone i wyszukiwane dane finansowe. Dane pozostają w istniejącej lokalizacji, ale kopia ich metadanych jest dodawany do usługi Data Catalog wraz z odwołaniem do lokalizacji źródła danych. Te metadane są również indeksowane, aby można było je łatwo odnaleźć za pomocą wyszukiwania oraz uczynić je zrozumiałymi dla użytkowników, którzy je odnajdą.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów poprzez zezwolenie wyłącznie na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Zezwalaj ruchu (RDP) pulpitu zdalnego, źródło ruchu musi być zdefiniowany w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) włączone tak, aby poświadczeń i innych wpisów tajnych są uruchamiane w środowisku chronionego, która jest odizolowana od systemem operacyjnym.

### <a name="virtual-network"></a>Sieć wirtualna

Ta architektura referencyjna definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresów 10.0.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają listy kontroli dostępu, które blokują lub zezwalają na ruch w sieci wirtualnej. Sieciowe grupy zabezpieczeń może służyć do zabezpieczenia ruchu na poziomie poszczególnych maszyn wirtualnych lub podsieci. Istnieją następujące grupy zabezpieczeń sieci:

  - Sieciową grupę zabezpieczeń dla warstwy danych (klastry serwera SQL, SQL Server monitora i modułu równoważenia obciążenia programu SQL)
  - Sieciową grupę zabezpieczeń do zarządzania hostem bastionu
  - Sieciową grupę zabezpieczeń usługi Active Directory
  - Sieciową grupę zabezpieczeń dla programu SQL Server Reporting Services

Każdej z grup zabezpieczeń sieci ma określone porty i protokoły Otwórz rozwiązanie może pracować bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej sieciowej grupy zabezpieczeń:

- [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
- Dzienniki platformy Azure Monitor jest podłączony do [sieciowej grupy zabezpieczeń&#39;s dzienników diagnostycznych](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: Każda podsieć jest skojarzona z sieciową grupą zabezpieczeń odpowiednich.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane za pomocą wielu środków, takimi jak szyfrowanie i inspekcja bazy danych.

**Azure Storage**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i chronić dane posiadaczy takich kart we wspieraniu organizacji bezpieczeństwa i zdefiniowane przez PCI DSS 3.2 wymagania dotyczące zgodności.

**Usługa Azure Disk Encryption**: [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. To rozwiązanie integruje się z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Azure SQL Database**: Wystąpienie usługi Azure SQL Database wykorzystuje następujące środki bezpieczeństwa bazy danych:

- [Uwierzytelnianie usługi Active Directory i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
- [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
- Usługa Azure SQL Database jest skonfigurowany do używania [technologii transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i rest usługi pliki dziennika transakcji, aby chronić informacje o. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywanych danych nie została poddana przed nieautoryzowanym dostępem.
- [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
- [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i dostęp do bazy danych nietypowe wzorce.
- [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy można dostęp do danych w postaci zwykłego tekstu.
- [Właściwości rozszerzone](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) może służyć do zaprzestać przetwarzania, których dane dotyczą, ponieważ pozwala ono użytkownikom dodawanie właściwości niestandardowych do obiektów bazy danych i oznaczania tagami danych jako "Wycofany", do obsługi logiki aplikacji, aby uniemożliwić przetwarzanie skojarzone dane finansowe.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pozwala użytkownikom na definiowanie zasad, aby ograniczyć dostęp do danych, aby zaprzestać przetwarzania.
- [Baza danych SQL dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. Dynamiczne maskowanie danych można automatycznie wykryć potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. Pomaga to identyfikować i Ogranicz dostęp do danych w taki sposób, że nie istnieje w bazie danych za pośrednictwem przed nieautoryzowanym dostępem. Klienci są zobowiązani do dostosowywania dane dynamiczne maskowanie ustawienia, aby stosować się do ich schemat bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych w środowisku platformy Azure:

- [Usługa Azure Active Directory](https://azure.microsoft.com/services/active-directory/) Microsoft&#39;s wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami usługi zarządzania. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usługi Azure Active Directory, w tym użytkowników uzyskujących dostęp do usługi Azure SQL Database.
- Do aplikacji uwierzytelniania przy użyciu usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi Azure Active Directory do uwierzytelniania aplikacji w usłudze Azure SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w usłudze Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Kontrola dostępu oparta na rolach na platformie Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom zdefiniować uprawnienia dostępu w zakresie, aby udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast wszystkie uprawnienia użytkownika bez ograniczeń dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługa Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych informacji. Administratorzy mogą używać usługi Azure Active Directory Privileged Identity Management w celu odnajdywania, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Ta funkcja może również wymuszać na żądanie, just-in-time dostęp administracyjny w razie.
- [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykrywa potencjalnych luk w zabezpieczeniach wpływających na organizacji&#39;tożsamości s umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z organizacji&#39;tożsamości s i bada podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo

**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomóc klientom chronić i uzyskać dostęp do takich danych:

- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń. Typ klucza jest HSM chronione 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień za pomocą kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.

**Zarządzanie poprawkami**: Windows wdrożone maszyny wirtualne w ramach tej architektury referencyjnej są domyślnie skonfigurowane, automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To rozwiązanie obejmuje również [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których zaktualizowano wdrożenia mogą być tworzone do poprawki maszyny wirtualne w razie.

**Ochrona przed szkodliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania, można skonfigurować alerty, gdy znany złośliwego lub niechcianego oprogramowania próbuje zainstalowania lub uruchomienia na chronionych maszynach wirtualnych.

**Usługa Azure Security Center**: Za pomocą [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), klienci mogą centralnie zastosować i zarządzać zasadami zabezpieczeń na potrzeby różnych obciążeń, ograniczenia narażenia na zagrożenia i wykrywanie oraz reagowanie na ataki. Ponadto usługa Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych.

Usługa Azure Security Center używa różnych możliwości wykrywania powiadamia klientów przed potencjalnymi atakami wymierzonymi w ich środowiskach. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Azure Security Center zawiera zbiór [wstępnie zdefiniowane alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanej aktywności. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Centrum zabezpieczeń Azure umożliwiają klientom Definiowanie nowych alertów zabezpieczeń, w oparciu o dane, które zostały już zebrane ze swoim środowisku.

Azure Security Center oferuje alerty zabezpieczeń z określonymi priorytetami i zdarzenia, dzięki czemu łatwiejsze dla klientów odkryć i rozwiązać potencjalne problemy z zabezpieczeniami. A [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdej wykrytej przed zagrożeniami, aby pomóc zespołom reagowania na zdarzenia w badanie i korygowanie działań na podstawie zagrożeń.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: Obciążenia serwera są zgrupowane w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) do pomagają zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Co najmniej jednej maszyny wirtualnej są dostępne podczas planowanych lub nieplanowanych zdarzeń związanych z konserwacją spotkania 99,95% umowy SLA platformy Azure.

**Magazyn usług Recovery Services**: [Magazyn usługi Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych platformy Azure w ramach tej architektury. Przy użyciu magazynu usługi Recovery Services klientów można przywrócić pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej, umożliwiając skraca czas ich przywracania.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure często dziennika systemu i aktywności użytkownika, a także kondycji systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki usługi Azure Storage, dzienników inspekcji usługi Key Vault i usługa Application Gateway Dzienniki dostępu i zapory. Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Okres przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, spełniają wymagania specyficzne dla organizacji przechowywania.

**Dzienniki platformy Azure Monitor**: Te dzienniki i dalszych są skonsolidowane w [dzienniki usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym. Po zebraniu dane są organizowane w oddzielnych tabelach dla każdego typu danych w obszarach roboczych usługi Log Analytics, która zezwala na wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Ponadto usługa Azure Security Center integruje się z dzienników usługi Azure Monitor, dzięki czemu klienci mogą korzystać z zapytania Kusto dostęp do swoich danych zdarzeń zabezpieczeń i łączyć je z danymi z innych usług.

Następujące Azure [rozwiązania do monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) są uwzględniane w ramach tej architektury:
-   [Ocena usługi Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

**Usługa Azure Automation**: [Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowywane i zarządza elementami runbook i jest uruchamiany. W tym rozwiązaniu elementów runbook pomagają zbieranie dzienników z usługi Azure SQL Database. Automatyzację [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) rozwiązanie umożliwia klientom łatwo identyfikować zmiany w środowisku.

**Azure Monitor**: [Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów, umożliwiając organizacjom inspekcję, tworzyć alerty i archiwizować dane, w tym śledzenia wywołań interfejsu API w swoich zasobów platformy Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/pcidss-dw-tm) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![Data warehouse, PCI DSS referencyjny diagram architektury](images/pcidss-dw-threat-model.png "aplikacji sieci Web PaaS dla model zagrożeń PCI DSS")

## <a name="compliance-documentation"></a>Dokumentacja zgodności

[Zabezpieczeń platformy Azure i plan zgodność — PCI DSS klienta odpowiedzialność macierzy](https://aka.ms/pcidss-crm) Wyświetla odpowiedzialność za wszystkie wymagania PCI DSS 3.2.

[Zabezpieczeń platformy Azure i plan zgodność — PCI DSS Data Warehouse implementacji macierzy](https://aka.ms/pcidss-dw-cim) zawiera informacje, na które ze standardami PCI DSS 3.2 wymagania są rozwiązywane przez architektury magazynu danych, w tym szczegółowe opisy jak wdrożenia spełnia wymagania dotyczące każdej kontrolki objętego usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute

Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi zostać skonfigurowany nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej magazynu danych. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można utworzyć wirtualnego prywatne połączenie między siecią lokalną i usługą Azure Virtual Network. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne "tuneli" informacji wewnątrz zaszyfrowane połączenia między siecią i platformą Azure przez klienta. Sieci VPN typu lokacja-lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu VPN przechodzą przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje opcję inny, jeszcze bardziej bezpieczne połączenia. Usługa ExpressRoute systemu Azure jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto ponieważ jest to bezpośrednie połączenie klienta, dostawca usług telekomunikacyjnych, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest uwidaczniana, aby go.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Proces wyodrębniania, przekształcania, ładowania

[Program PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) można ładowanie danych do usługi Azure SQL Data Warehouse bez konieczności oddzielnego wyodrębniania, przekształcania, załadowania lub zaimportowania narzędzia. Program PolyBase umożliwia dostęp do danych za pomocą zapytań T-SQL. Przy użyciu technologii PolyBase może służyć przez firmę Microsoft analizy biznesowej i stos analizy, a także narzędzia innych producentów są zgodne z programem SQL Server.

### <a name="azure-active-directory-setup"></a>Konfiguracja usługi Azure Active Directory

[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest Zarządzanie wdrożeniem i inicjowania obsługi dostępu do personelu wchodzenie w interakcje ze środowiskiem. Istniejący serwer Active Directory systemu Windows można zintegrować z usługą Azure Active Directory w [cztery kliknięcia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Klienci mogą również powiązanie wdrożonej infrastrukturę usługi Active Directory (kontrolery domeny) istniejących usług Azure Active Directory, wprowadzając poddomeny lasu usługi Azure Active Directory wdrożone infrastrukturę usługi Active Directory.

### <a name="optional-services"></a>Usługi opcjonalne

Platforma Azure oferuje szereg usług na potrzeby magazynu i przemieszczania danych sformatowanego i niesformatowane. Następujące usługi mogą być dodawane do tej architektury referencyjnej, w zależności od wymagań klientów:
-   [Usługa Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) to usługa zarządzana usługa w chmurze stworzonemu dla kompleksowych, hybrydowych extract-transform-load i projekty integracji danych. Usługa Azure Data Factory ma możliwości śledzenia i Znajdź dane posiadaczy takich kart, łącznie z wizualizacją i monitorowania narzędzi do identyfikacji, gdy źródła otrzymanych danych, a, z którego pochodzi. Za pomocą usługi Azure Data Factory, klientów można tworzyć i planować opartych na danych przepływy pracy o nazwie potoki, które pozyskiwać dane z różnych magazynów danych. Te potoki Zezwalaj klientom na odbieranie danych z wewnętrznych i zewnętrznych źródeł. Klienci, a następnie jest przetwarzanie i przekształcanie danych dla danych wyjściowych w magazynach danych, takich jak Azure SQL Data Warehouse.
- Klientów można umieścić dane bez struktury w [usługi Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), które umożliwia przechwytywanie danych o dowolnym rozmiarze, typie, i pozyskiwania szybkości w jednym miejscu na potrzeby analizy operacyjnej i rozpoznawczej.  Usługa Azure Data Lake ma funkcje, które umożliwiają wyodrębnianie i konwersja danych. Azure Data Lake Store jest zgodna z najbardziej otwarta składnikami źródła w ekosystemie usługi Hadoop i bardzo dobrze integruje się z innymi usługami Azure, takich jak Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
 - W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
 - Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
 - Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
