---
title: Zabezpieczeń platformy Azure i plan zgodności - magazynu danych na potrzeby GDPR
description: Zabezpieczeń platformy Azure i plan zgodności - magazynu danych na potrzeby GDPR
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: a091f4c941b1f4a338ad23956dbba3a7b89b87e8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Zabezpieczeń platformy Azure i plan zgodności: magazynu danych na potrzeby GDPR

## <a name="overview"></a>Przegląd
Ogólne rozporządzenia ochrony danych (GDPR) zawiera wiele wymagania w zakresie zbierania, przechowywania i przy użyciu informacji osobistych, w tym jak organizacje zidentyfikować i zabezpieczania danych osobowych, uwzględnienia wymogów dotyczących przezroczystości, wykrywania i raportu naruszenia danych osobowych i personel prywatności pociągu i innym pracownikom. GDPR zapewnia większą kontrolę nad jego danych osobowych użytkowników indywidualnych i nakłada wiele nowych obowiązków w organizacji, które zbierania, obsługiwać lub analizowania danych osobowych. Nowe zasady w organizacji, które oferują towarów nakłada GDPR i usług do osób w Unii Europejskiej (UE), lub że zbieranie i analizowanie danych powiązane mieszkańców Unii Europejskiej. Niezależnie od tego, w którym znajduje się organizacja stosuje się GDPR.

Zaprojektowana Azure z branży środków bezpieczeństwa i zasady zachowania poufności informacji w celu zapewnienia ochrony danych w chmurze, łącznie z kategoriami danych osobowych identyfikowane przez GDPR przez firmę Microsoft. Firmy Microsoft [warunków umowy](http://aka.ms/Online-Services-Terms) zatwierdzić Microsoft wymagania procesorów.

Ten Azure zabezpieczeń i zgodności plan zawiera wskazówki dotyczące wdrażania to architektura magazynu danych na platformie Azure, która ułatwia spełnianie wymagań GDPR. To rozwiązanie przedstawia sposób, w którym klienci mogą spełniać określone wymagania zabezpieczeń i zgodności i stanowi podstawę do tworzenia i konfigurowania ich własnych rozwiązań magazynu danych na platformie Azure. Klienci może wykorzystywać o architekturze wzorcowej i wykonać firmy Microsoft [czterech kroków procesu](https://aka.ms/gdprebook) w podróży GDPR zgodności:
1. Odnajdywanie: Zidentyfikować istnieje które dane osobowe oraz jej lokalizację.
2. Zarządzanie: Regulują sposób osobistych danych jest używany i dostępny.
3. Ochrona: Ustanowić środki zabezpieczające zapobiec, wykrywanie i reagowanie na luki w zabezpieczeniach i naruszeń danych.
4. Raport: Przechowywania dokumentacji wymagane i zarządzanie żądaniami danych i naruszyć powiadomienia.

To architektura referencyjna, skojarzone z dokumentacją i modelu zagrożeń mają służyć jako podstawę do klientów dostosować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym. Pamiętaj o następujących kwestiach:
- Architektura zapewnia linii bazowej, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z GDPR.
- Klienci są zobowiązani do przeprowadzania odpowiednich ustawień zabezpieczeń i zgodności oceny dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat wdrażania poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
To rozwiązanie zapewnia architektura referencyjna struktury, która implementuje chmury wysokiej wydajności i bezpiecznego magazynu danych. Istnieją dwie warstwy danych w ramach tej architektury: co gdzie dane są importowane, przechowywane i umieszczane w środowisku klastrowanym SQL, a druga dla usługi Azure SQL Data Warehouse w przypadku, gdy dane są ładowane przy użyciu narzędzia ETL (np. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Zapytania T-SQL) do przetwarzania. Gdy dane są przechowywane w usłudze Azure SQL Data Warehouse, analytics można uruchomić w bardzo dużej skali.

Azure oferuje wiele usług raportowania i analiz dla klienta. To rozwiązanie obejmuje usługi SQL Server Reporting Services (SSRS) do szybkiego tworzenia raportów z magazynu danych SQL Azure. Cały ruch SQL są szyfrowane przy użyciu protokołu SSL przez dodanie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem Azure zalecane jest stosowanie zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.

Dane w magazynie danych SQL Azure są przechowywane w tabel relacyjnych z magazynem kolumnowy, format, który znacznie zmniejsza koszty przechowywania danych podczas poprawę wydajności zapytań.  W zależności od wymagań użycia zasobów obliczeniowych Azure SQL Data Warehouse można skalować w górę lub w dół lub wyłączone całkowicie Jeśli brak aktywnych procesów wymagających zasobów obliczeniowych.

Równoważenia obciążenia SQL zarządza SQL ruchu, zapewniających wysoką wydajność. Wszystkie maszyny wirtualne w tej architekturze referencyjnej wdrożyć w zestawie wystąpieniami programu SQL Server skonfigurowanych w zawsze włączonej grupy dostępności funkcji wysokiej dostępności i odzyskiwania po awarii dostępności.

Informacje o architekturze wzorcowej magazynu danych obejmuje również warstwą Active Directory (AD) do zarządzania zasobami w architekturze. Podsieć usługi Active Directory umożliwia łatwy przyjęcie w ramach większej struktury lasu usługi AD, co pozwala na działanie ciągłe środowiska, nawet wtedy, gdy jest niedostępny do większych lasu. Wszystkie maszyny wirtualne są przyłączone do domeny do warstwy usługi Active Directory i wymuszanie konfiguracji zgodności i zabezpieczeń na poziomie systemu operacyjnego za pomocą zasad grupy usługi Active Directory.

Maszyna wirtualna służy jako zarządzania hosta bastionu, zapewnienia bezpiecznego połączenia dla administratorów, aby wdrożyć dostęp do zasobów. Ładuje dane do obszaru przemieszczania za pomocą tego hosta bastionu zarządzania. **Azure zaleca się Konfigurowanie połączenia sieci VPN lub rozwiązania Azure ExpressRoute do importowania danych i zarządzania w podsieci architektura odwołania.**

![Magazynu danych na potrzeby diagram architektury odwołanie GDPR](images/gdpr-datawarehouse-architecture.png?raw=true "magazynu danych na potrzeby diagram architektury GDPR odwołania")

To rozwiązanie wymaga następujących usług platformy Azure. Szczegółowe informacje o architekturze wdrożenia są w [wdrożenia Architecture](#deployment-architecture) sekcji.

-   Azure Virtual Machines
    - (1) Host bastionu
    -   (2) active Directory kontrolera domeny
    -   (2) węzeł klastra programu SQL Server
    -   (1) Monitor serwera SQL
-   Zestawy dostępności
    - Kontrolery domeny usługi Active Directory
    - Węzły klastra SQL i monitora
-   Virtual Network
    - (4) podsieci
    -   (4) grupy zabezpieczeń sieci
- SQL Data Warehouse
- SQL Server Reporting Services
- Moduł równoważenia obciążenia Azure SQL
- Usługa Azure Active Directory
- Magazyn usług odzyskiwania
- W usłudze Azure Key Vault
- Operations Management Suite (OMS)
- Azure Data Catalog
- Azure Security Center

## <a name="deployment-architecture"></a>Architektura wdrażania
Poniższa sekcja zawiera szczegóły dotyczące wdrażania i wdrażania elementów.

**Usługa SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jest Enterprise danych magazynu (EDW) wykorzystującego Massively Parallel przetwarzania (MPP) do szybkiego uruchamiania złożonych zapytań między petabajtów danych, dzięki czemu użytkownicy mogą efektywne zidentyfikować dane osobowe. Użytkownicy mogą używać prostych kwerend PolyBase T-SQL do importowania danych big data do usługi SQL Data Warehouse i wykorzystywać możliwości MPP w celu uruchomienia analizy wysokiej wydajności.

**SQL Server Reporting Services (SSRS)**: [programu SQL Server Reporting Services](https://docs.microsoft.com/en-us/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) zapewnia szybkie tworzenie raportów z tabel, wykresy, mapy, mierników, macierze oraz więcej magazyn danych SQL Azure.

**Wykaz danych**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) sprawia, że źródła danych łatwo odnaleźć i zrozumieć przez użytkowników, którzy zarządzają danych. Wspólnych źródeł danych może być zarejestrowane, oznakowane i przeszukiwana danych osobowych. Dane pozostają w istniejącej lokalizacji, ale kopia metadanych jest dodawana do usługi Data Catalog wraz z odwołaniem do lokalizacji źródła danych. Te metadane są również indeksowane, aby można było je łatwo odnaleźć za pomocą wyszukiwania oraz uczynić je zrozumiałymi dla użytkowników, którzy je odnajdą.

**Host bastionu**: host bastionu jest pojedynczy punkt wejścia, który umożliwia użytkownikom dostęp do wdrożonych zasobów, w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów, zezwalając tylko ruchu zdalnego z publicznych adresów IP na liście bezpiecznych. Aby zezwolić na zdalny pulpitu ruch (RDP), źródło ruchu musi być zdefiniowana w grupy zabezpieczeń sieci (NSG).

To rozwiązanie tworzy maszynę wirtualną jako hosta domeny bastionu z następujących konfiguracji:
-   [Rozszerzenie ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozszerzenie pakietu OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozszerzenie Diagnostyka Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Szyfrowanie dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault
-   [Zasad automatyczne zamykanie](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane
-   [Windows Defender poświadczeń Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) włączane poświadczeń i innych informacji poufnych uruchamiane w chronionym środowisku, która jest odizolowana od systemem operacyjnym

### <a name="virtual-network"></a>Sieć wirtualna
Informacje o architekturze wzorcowej definiuje się na przestrzeń adresową z 10.0.0.0/16 prywatnej sieci wirtualnej.

**Sieciowe grupy zabezpieczeń**: [grup NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają listy kontroli dostępu (ACL) akceptujące lub odrzucające ruchu w sieci wirtualnej. Grupy NSG mogą służyć do zabezpieczenia ruchu w podsieci lub poziomie maszyny Wirtualnej. Następujące grupy NSG istnieje:
  - Grupa NSG dla warstwy danych (klastrów serwerów SQL, programu SQL Server monitora i SQL usługi równoważenia obciążenia)
  - Grupa NSG dla hosta bastionu zarządzania
  - Grupa NSG dla usługi Active Directory
  - Grupa NSG dla programu SQL Server Reporting Services

Każdy z grup NSG mieć określone porty i protokoły umożliwiający rozwiązanie może działać bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej grupy NSG:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i są przechowywane na koncie magazynu
  - Analiza dzienników OMS jest podłączony do [NSG dla diagnostyki](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: każdej podsieci jest skojarzony z jego odpowiedniej grupy NSG.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane przechowywane przez kilka miar, takimi jak szyfrowanie i inspekcja bazy danych.

**Magazyn Azure**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Magazyn](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i ochrony danych osobowych wesprzeć zobowiązań zabezpieczeń organizacji i zdefiniowanych przez GDPR wymagań dotyczących zgodności.

**Szyfrowanie dysków Azure**: [szyfrowania dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi. Rozwiązanie integruje się z usługą Azure Key Vault, aby kontrolować i zarządzać kluczami szyfrowania dysków.

**Baza danych SQL Azure**: wystąpienie bazy danych SQL Azure używa następujących środków zabezpieczeń bazy danych:
-   [AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania konta magazynu platformy Azure.
-   Baza danych SQL jest skonfigurowana do używania [funkcji przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych, i Zatrzymaj plików dzienników transakcji do ochrony informacji w. Funkcji TDE zawiera gwarantują, że przechowywane dane osobowe nie zostało poddane nieautoryzowanego dostępu.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwić wszystkie dostęp do serwerów baz danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, podając alertów zabezpieczeń bazy danych podejrzanych działań, potencjalnych luk w zabezpieczeniach, ataki i nietypowych bazy danych programu access wzorce.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że wrażliwe dane osobowe nigdy nie jest wyświetlana jako zwykłego tekstu w systemie bazy danych. Po włączeniu szyfrowania danych, tylko aplikacje klienckie lub serwery aplikacji dostęp do kluczy można uzyskać dostępu do danych w postaci zwykłego tekstu.
- [Właściwości rozszerzone](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funkcja może być używana zaprzestania przetwarzania podmiotów danych, który zezwala użytkownikom na dodawanie właściwości niestandardowych do obiektów bazy danych i oznaczać dane jako "Wycofany", do obsługi aplikacji logiki, aby zapobiec Przetwarzanie skojarzone dane osobiste.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pozwala użytkownikom na definiowanie zasad, aby ograniczyć dostęp do danych, aby przerwać przetwarzania.
- [SQL bazy danych dynamicznych danych maskowania (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnieniem poufnych danych osobowych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. DDM można automatycznie wykryć potencjalnie poufnych danych i zaproponuje odpowiednie maski ma zostać zastosowany. Dzięki temu identyfikację danych osobowych kwalifikujących się do ochrony GDPR i zmniejszenia dostępu w taki sposób, że nie istnieje w bazie danych za pośrednictwem nieautoryzowanego dostępu. **Uwaga: Użytkownicy będą musieli ustawienia DDM stosować się do schematu bazy danych.**

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych osobowych w środowisku platformy Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) jest wielodostępne oparte na chmurze katalogami i tożsamościami zarządzania usługi Microsoft. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze AAD, w tym użytkownikom dostęp do bazy danych SQL.
-   Aby aplikacja uwierzytelniania przy użyciu usługi AAD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi AAD do uwierzytelniania aplikacji z bazą danych SQL Azure. Aby uzyskać więcej informacji, zobacz temat jak [chronią poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure opartej na rolach kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom definiowanie uprawnień szczegółowych dostępu, aby udzielić tylko takiego dostępu, użytkownicy muszą wykonać swoje zadania. Zamiast nadanie co nieograniczonych uprawnień dla zasobów platformy Azure, Administratorzy mogą umożliwić tylko pewne akcje do uzyskiwania dostępu do danych osobowych. Subskrypcja dostęp jest ograniczony do administratora subskrypcji.
- [Zarządzanie tożsamości usługi AAD uprzywilejowanych (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych informacji takich jak dane osobowe.  Administratorzy mogą używać usługi AAD Privileged Identity Management do odnajdywania, ograniczenia i monitorowanie tożsamości uprzywilejowanych oraz ich dostępu do zasobów. Tej funkcji można również wymusić na żądanie, w czasie dostęp administracyjny w razie potrzeby.
- [Ochronę tożsamości usługi AAD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki tożsamości organizacji, konfiguruje automatyczne odpowiedzi wykryte podejrzane działania związane z tożsamości organizacji i sprawdza podejrzanych zdarzenia podjęcie odpowiednich działań, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie kluczy tajnych**: w tym rozwiązaniu zastosowano [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) zarządzania kluczy i kluczy tajnych. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomóc klientom ochrony danych osobowych i dostęp do tych danych:
- Zasady dostępu zaawansowane są konfigurowane na potrzeby.
- Zasady dostępu do magazynu kluczy są zdefiniowane z minimalne uprawnienia wymagane do kluczy i kluczy tajnych.
- Wszystkie klucze i kluczy tajnych w magazynie kluczy mają datę wygaśnięcia.
- Wszystkie klucze w magazynie kluczy chronionych przez specjalne sprzętowych modułów zabezpieczeń (HSM). Typ klucza jest kluczem RSA HSM chronione 2048-bitowego.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień przy użyciu funkcji RBAC.
- Dzienniki diagnostyczne dla usługi Key Vault są włączone w okres przechowywania dla co najmniej 365 dni.
- Dozwolonych operacji kryptograficznych dla kluczy są ograniczone do tych wymagane.

**Zarządzanie poprawkami**: maszyny wirtualne systemu Windows wdrożone w ramach tej architektury odwołania są domyślnie skonfigurowane, odbierania automatycznych aktualizacji z usługi Windows Update. To rozwiązanie obejmuje również OMS [usługi Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których można utworzyć wdrożenia zaktualizowane maszynom wirtualnym poprawki w razie potrzeby.

**Ochrona przed złośliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, że pomaga zidentyfikować i usunąć wirusy, programy szpiegujące lub inne złośliwe oprogramowanie, można skonfigurować alerty Jeśli znane złośliwego lub niechcianego oprogramowania próbuje zainstalować lub uruchomić na chronionych maszyn wirtualnych.

**Alerty zabezpieczeń**: [Centrum zabezpieczeń Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) służy do monitorowania ruchu, zbierz dzienniki i analizować źródeł danych w przypadku zagrożeń. Ponadto Centrum zabezpieczeń Azure uzyskuje dostęp do istniejącej konfiguracji usług platformy Azure, konfiguracji i zalecenia usługi, aby poprawić stan zabezpieczeń i ochrony danych osobowych. Centrum zabezpieczeń Azure obejmuje [raportu analizy zagrożeń](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) każdego wykrytych zagrożeń ułatwiających zespoły odpowiedzi na zdarzenia zbadać i eliminowanie zagrożeń.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej
**Wysoka dostępność**: obciążenia serwera są pogrupowane w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pomaga zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Co najmniej jednej maszyny wirtualnej jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, spotkania 99,95% umowy SLA platformy Azure.

**Magazyn usług odzyskiwania**: [magazyn usług odzyskiwania](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. W magazynie usług odzyskiwania klientów można przywrócić pliki i foldery z maszyn wirtualnych IaaS bez przywracania całą maszynę Wirtualną, umożliwiające szybsze przywracania.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja
[Operations Management Suite (OMS)](https://docs.microsoft.com/en-us/azure/operations-management-suite/operations-management-suite-overview) zapewnia szczegółowe rejestrowanie działania systemu i użytkownika, a także kondycji systemu. OMS [analizy dzienników](https://azure.microsoft.com/services/log-analytics/) rozwiązanie zbiera i analizuje dane generowane przez zasobami na platformie Azure i lokalnego środowiska.
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacji wykonywanych na zasobów w ramach subskrypcji. Dzienniki aktywności mogą ułatwić określenie inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdego zasobu. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows i magazynu obiektów Blob platformy Azure, tabele i kolejki dzienników.
- **Zaloguj się Archiwizacja**: zapisu wszystkich dzienników diagnostycznych na konto magazynu Azure scentralizowane i zaszyfrowanego dla archiwizacji. Przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, aby spełnić wymagania organizacji dotyczące przechowywania. Te dzienniki nawiązać Analiza dzienników Azure do przetwarzania, przechowywania i raportowania pulpitu nawigacyjnego.

Ponadto następujące rozwiązania OMS są dołączone jako część tej architektury:
-   [Ocena AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i udostępnia listę zalecenia dotyczące infrastruktury serwera wdrożonym z określonymi priorytetami.
-   [Ocena ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): rozwiązanie chroniące przed złośliwym kodem raportów dotyczących stanu złośliwego oprogramowania, zagrożeń i ochrony.
-   [Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): rozwiązanie do automatyzacji Azure przechowuje, uruchamia i zarządza elementami runbook.
-   [Bezpieczeństwo i inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpieczenia i inspekcji pulpit nawigacyjny zapewnia wysokiego poziomu wgląd w stan zabezpieczeń zasobów podając metryki na domen zabezpieczeń, godne uwagi problemy wykryć, analizy zagrożeń i typowych kwerend zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożonym opracowuje dla klientów.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): rozwiązanie do zarządzania aktualizacji umożliwia zarządzanie klienta aktualizacje zabezpieczeń systemu operacyjnego, w tym stanu o dostępnych aktualizacjach i proces instalacji wymaganych aktualizacji.
-   [Agent kondycji](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): kondycja agenta rozwiązania zgłasza liczbę agentów są wdrażane i ich rozmieszczenie geograficzne, a także liczbę agentów, które odpowiadają i liczby agentów, które są przesyłanie danych operacyjnych.
-   [Azure Dzienniki aktywności](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązania analizy dziennika aktywności obsługuje Analiza dzienników Azure działania we wszystkich subskrypcji platformy Azure dla danego klienta.
-   [Śledzenie zmian](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): śledzenie zmian rozwiązania pozwala łatwo zidentyfikować zmiany w środowisku.

## <a name="threat-model"></a>Modelu zagrożeń

Diagram przepływu danych dla tej architektury dokumentacja jest dostępna dla [Pobierz](https://aka.ms/gdprDWdfd) lub znajduje się poniżej. Ten model może ułatwić klientom punkty potencjalne ryzyko w infrastrukturze systemu, podczas wprowadzania zmian.

![Magazyn danych dla modelu zagrożeń GDPR](images/gdpr-datawarehouse-threat-model.png?raw=true "magazynu danych dla modelu zagrożeń GDPR")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Azure zabezpieczeń i zgodności planu — macierzy odpowiedzialność klienta GDPR](https://aka.ms/gdprCRM) wymieniono obowiązki kontrolera i procesora dla wszystkich artykułów GDPR. Należy pamiętać, że dla usług Azure, klient zazwyczaj jest to kontroler i Microsoft zachowuje się jak procesor.

[Zabezpieczeń platformy Azure i plan zgodności - macierzy wdrożenia magazynu danych GDPR](https://aka.ms/gdprDW) zawiera informacje, na które GDPR artykuły dotyczą architektury magazynu danych, w tym szczegółowe opisy sposób Implementacja spełnia wymagania dotyczące każdego wymienionego artykułu.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieć VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowana do bezpiecznego nawiązać połączenie z zasoby wdrożone w ramach tej architektury odwołanie do magazynu danych. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Implementując bezpieczny tunel sieci VPN platformy Azure, można utworzyć wirtualnego prywatnej połączenie między siecią lokalną i sieci wirtualnej platformy Azure. To połączenie odbywa się przez Internet i pozwala bezpiecznie "tunnel" informacji wewnątrz zaszyfrowanych łącza między siecią a Azure klienta. Sieci VPN typu lokacja-lokacja jest bezpieczne, dojrzała technologia, która jest wdrażany w przedsiębiorstwach wszystkich rozmiarów dekad. [Trybu tunelowania IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizm szyfrowania.

Ponieważ ruchu w tunelu VPN przechodzą przez Internet, korzystając z sieci VPN lokacja lokacja, firma Microsoft oferuje opcji inny, jeszcze bardziej bezpieczne połączenia. Usługa Azure ExpressRoute jest dedykowanych sieci WAN łącza między Azure i lokalizacji lokalnej lub dostawcy usług hosta programu Exchange. Jako połączeń ExpressRoute nie przechodzą w Internecie, te połączenia oferują więcej niezawodności, szybkości szybsze niższe opóźnienia i lepsze zabezpieczenia niż typowe połączenia za pośrednictwem Internetu. Ponadto jest bezpośrednie połączenie klienta telekomunikacyjnych dostawcy, dlatego dane nie są przesyłane w Internecie i w związku z tym nie jest uwidaczniana do niego.

Najlepsze rozwiązania dotyczące wdrażania sieci hybrydowe bezpiecznego, która rozszerza sieci lokalnej na platformie Azure są [dostępne](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Proces wyodrębniania przekształcenia ładowania (ETL)
[Program PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) można załadować danych do usługi Azure SQL Data Warehouse bez konieczności oddzielnego ETL lub zaimportować narzędzia. Aparat PolyBase zezwala na dostęp do danych za pomocą zapytania T-SQL. Przy użyciu programu PolyBase może służyć firmy Microsoft analizy biznesowej i stosu analizy, a także narzędzia innych firm zgodne z programem SQL Server.

### <a name="azure-active-directory-setup"></a>Instalacja usługi Azure Active Directory
[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest niezbędne do zarządzania wdrażania i inicjowania obsługi dostępu do personelu interakcji ze środowiskiem. Istniejący Windows Server Active Directory można zintegrować z usługi AAD w [cztery kliknięć](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Klientów można również powiązanie wdrożonej infrastruktura usługi Active Directory (kontrolery domeny) do istniejącej usługi AAD dokonując wdrożonej infrastruktura usługi Active Directory jest poddomeną las usługi AAD.

### <a name="optional-services"></a>Usługi opcjonalne
Azure oferuje wiele usług z magazynu i przemieszczania sformatowany i niesformatowanych danych. Następujące usługi mogą być dodawane do tej architektury odwołania, w zależności od wymagań klientów:
-   [Fabryka danych Azure](https://docs.microsoft.com/azure/data-factory/introduction) to usługa w chmurze zarządzanych przeznaczonego do złożonych hybrydowego extract — przekształcenia ładowania (ETL) projektów integracji wyodrębniania obciążenia transformacji (ELT) i danych. Fabryka danych Azure ma funkcje pomocy śledzenia i Znajdź dane osobowe, w tym wizualizacji i narzędzi do identyfikacji podczas odebrania danych i gdy pochodzi z monitorowania. Przy użyciu fabryki danych Azure, klientów można tworzyć i zaplanować opartych na danych o nazwie potoki, które pozyskiwania danych z baz danych różnych przepływów pracy. Potoki te umożliwiają klientom pozyskiwania danych ze źródeł wewnętrznych i zewnętrznych. Klientów można następnie przetwarzania i przekształcania danych wyjściowych do magazynów danych, takie jak magazyn danych SQL Azure.
- Klientów można umieścić dane bez struktury w [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), które umożliwia przechwytywanie danych o dowolnej wielkości, typu, oraz przyspieszyć wprowadzanie w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.  Usługa Azure Data Lake ma funkcje pozwalające wyodrębniania i konwersji danych. Azure Data Lake Store jest zgodna z najbardziej otwartych składników źródła w ekosystemie Hadoop i bardzo dobrze integruje się z innymi usługami Azure, takich jak magazyn danych SQL Azure.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.
 - Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produktem firmy Microsoft lub rozwiązania.
 - Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym.
 - Ten dokument został utworzony jako odwołanie i nie należy używać do definiowania oznacza, że wszystkie za pomocą których klient może spełniać określone wymagania i przepisami. Klienci powinien poszukuje prawne z organizacji w implementacjach klienta zatwierdzone.
