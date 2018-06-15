---
title: Zabezpieczeń platformy Azure i plan zgodności - analiza FedRAMP
description: Zabezpieczeń platformy Azure i plan zgodności - analiza FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: db9e49cc4dc02b6864bee2dc4b73ff3c085f5380
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206970"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Zabezpieczeń platformy Azure i plan zgodności: analiza FedRAMP

## <a name="overview"></a>Przegląd

[Federalne ryzyka i Program do zarządzania autoryzacji (FedRAMP)](https://www.fedramp.gov/) to program całej dla instytucji rządowych Stanów Zjednoczonych, który zawiera standardowe podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania dla chmury produkty i usługi. Ten Azure zabezpieczeń i zgodności plan zawiera wskazówki dotyczące jak dostarczać architekturę analytics Microsoft Azure, która pomaga implementuje podzbiór wysokiej FedRAMP formantów. To rozwiązanie zawiera wskazówki dotyczące wdrażania i konfigurowania zasobów Azure dla typowych architektura referencyjna prezentacja sposoby, w którym klienci mogą spełniać określone wymagania zabezpieczeń i zgodności i podstawą dla klientów Tworzenie i konfigurowanie ich własnych rozwiązań analitycznych na platformie Azure.

To architektura referencyjna, przewodniki implementacji skojarzonym formancie i modele zagrożeń mają służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym. Wdrażanie aplikacji w tym środowisku bez żadnych modyfikacji jest niewystarczający w celu całkowicie spełniają wymagania wysokiej FedRAMP linii bazowej. Pamiętaj o następujących kwestiach:
- Architektura zapewnia linii bazowej, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z FedRAMP.
- Klienci są zobowiązani do przeprowadzania odpowiednich ustawień zabezpieczeń i zgodności oceny dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat wdrażania poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników

To rozwiązanie zapewnia analytics platformy, na której klienci mogą tworzyć własne narzędzia do analizy. Architektura referencyjna przedstawiono przypadek użycia ogólny, gdzie klienci wprowadzanie danych za pośrednictwem Importy danych zbiorczego SQL/dane administratora lub aktualizacje danych operacyjnych za pomocą operacyjne użytkownika. Działać strumieni wprowadzają [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) do importowania danych do bazy danych SQL. Środowisko Azure Functions muszą być skonfigurowane przez klienta za pośrednictwem portalu Azure, aby obsłużyć zadania importu unikatowe dla każdego klienta własnych wymagań analytics.

Microsoft Azure oferuje wiele usług raportowania i analiz dla klienta; Jednak to rozwiązanie zawiera usług Azure Analysis Services w połączeniu z bazą danych SQL Azure szybko przeglądanie danych i szybsze dostarczenie wyników za pomocą modelowania inteligentny danych klienta. Usługi analizy Azure jest formą uczenia maszynowego przeznaczone do zwiększenia szybkości zapytania odnajdywania nowe relacje między zestawów danych. Po danych po zapoznaniu za pośrednictwem kilku funkcji statystycznych, maksymalnie 7 zapytania dodatkowe pule (całkowity, łącznie z serwera klient 8) mogą być synchronizowane z tej samej modeli tabelarycznych rozkładu obciążenia zapytania i zmniejsza czas odpowiedzi.

Ulepszone analizy i raportowania baz danych można skonfigurować dla indeksów magazynu kolumn. Zarówno usługi analizy Azure, jak i bazy danych SQL można skalować w górę lub w dół lub całkowicie zamknąć w odpowiedzi do użycia przez klientów. Cały ruch SQL są szyfrowane przy użyciu protokołu SSL przez dodanie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem Azure zalecane jest stosowanie zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.

Gdy dane są przekazywane do bazy danych SQL Azure i uczony Azure Analysis Services, jest rozłożona operacyjne użytkownika i administratora SQL/danych z usługi Power BI. Power BI intuicyjnie wyświetla dane i pobiera razem informacji między wiele zestawów danych, aby narysować większego wglądu. Jego wysoki stopień zdolności adaptacyjnych i Łatwa integracja z bazy danych SQL Azure zapewnia klientów można skonfigurować go do obsługi szerokiej gamy scenariuszy zgodnie z ich potrzebami.

Całe rozwiązanie oparto na magazyn Azure, której klienci konta skonfigurować w portalu Azure. Magazyn Azure szyfruje wszystkie dane przy użyciu szyfrowania usługi magazynu do zachowania poufności danych w stanie spoczynku.  Geograficzne magazyn geograficznie nadmiarowy (GRS) zapewnia czy niekorzystny zdarzeń w centrum danych podstawowych klienta nie spowoduje utratę danych jako drugiej kopii będą przechowywane w innej lokalizacji setki mil optymalizacji.

Aby zwiększyć bezpieczeństwo tej architektury zarządza zasobami za pomocą usługi Azure Active Directory i usługi Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem Operations Management Suite (OMS) i Azure Monitor. Klienci skonfigurować obie te usługi monitorowania do przechwytywania dzienniki i wyświetlania kondycji systemu w jednej, łatwo nawigacji pulpitu nawigacyjnego.

Baza danych SQL Azure zwykle odbywa się za pośrednictwem SQL Server Management Studio (SSMS), w którym jest uruchomiona z komputera lokalnego skonfigurowane do korzystania z bazy danych SQL Azure za pośrednictwem bezpiecznego połączenia sieci VPN lub usługi ExpressRoute. **Azure zaleca się Konfigurowanie połączenia sieci VPN lub rozwiązania Azure ExpressRoute do importowania danych i zarządzania w grupie zasobów architektura odwołania.**

![Analiza diagram architektury odwołanie FedRAMP](images/fedramp-analytics-reference-architecture.png?raw=true "analiza diagram architektury FedRAMP odwołania")

### <a name="roles"></a>Role
Plan analytics opisano scenariusz z trzech typów ogólnych użytkownika: operacyjne użytkownika, administratora SQL danych/i inżynier systemów. Azure opartej na rolach kontroli dostępu (RBAC) umożliwia wdrożenie zarządzania dokładne dostępu za pomocą wbudowanych ról niestandardowych. Zasoby są dostępne do konfigurowania [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) i tworzenie konspektu i wdrażanie [wstępnie zdefiniowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Inżynier systemów
Inżynier systemów jest właścicielem subskrypcji klienta dla platformy Azure i konfiguruje wdrożenie rozwiązania za pośrednictwem portalu Azure.

#### <a name="sqldata-administrator"></a>Administrator SQL/danych
Administrator SQL danych/ustanawia funkcja importowania zbiorczego danych i funkcji aktualizacji danych operacyjnych do przekazywania do bazy danych Azure SQL. Administrator SQL/danych nie odpowiada za jakiekolwiek aktualizacje danych operacyjnych w bazie danych, ale będą mogli wyświetlać dane za pomocą usługi Power BI.

#### <a name="operational-user"></a>Operacyjne użytkownika
Użytkownik operacyjne regularnie aktualizuje dane i właścicielem Generowanie codziennych danych. Operacyjne użytkownika również interpretują wyniki za pomocą usługi Power BI.

### <a name="azure-services"></a>Usługi platformy Azure

To rozwiązanie wymaga następujących usług platformy Azure. Szczegółowe informacje o architekturze wdrożenia są w [wdrożenia Architecture](#deployment-architecture) sekcji.
- Azure Functions
- Azure SQL Database
- Azure Analysis Services
- Usługa Azure Active Directory
- W usłudze Azure Key Vault
- OMS
- Azure Monitor
- Azure Storage
- Brama usługi ExpressRoute lub sieć VPN
- Pulpit nawigacyjny usługi Power BI

## <a name="deployment-architecture"></a>Architektura wdrażania
Poniższa sekcja zawiera szczegóły dotyczące projektowania i wdrażania elementów.

![tekst alternatywny](images/fedramp-analytics-components.png?raw=true "Analytics FedRAMP diagramu składników")

**Środowisko Azure Functions**: [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) przedstawiono rozwiązania umożliwiające uruchamianie małych fragmentów kodu w chmurze za pośrednictwem większość języków programowania. Funkcje, w tym rozwiązaniu integracji z usługą Azure Storage na automatyczne ściągnięcie danych klienta w chmurze, ułatwianie integracji z innymi usługami Azure. Funkcje są łatwe skalowanie i tylko pociągnąć za sobą koszt działające.

**Usługa Azure Analysis**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) zapewnia integrację z usługami platformy Azure danych i modelowania danych przedsiębiorstwa. Usługa Azure Analysis przyspiesza przeglądanie olbrzymich ilości danych przez połączenie danych z wielu źródeł w modelu danych.

**Power BI**: [usługi Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) zapewnia analizy i raportowania funkcje dla klientów w trakcie pobierania większego wglądu poza ich wysiłków przetwarzania danych.

### <a name="networking"></a>Networking
**Sieciowe grupy zabezpieczeń**: [grup NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) są skonfigurowane do zarządzania ruchem skierowany wdrożonych zasobów i usług. Sieciowe grupy zabezpieczeń są ustawione na Odmów przez domyślny schemat i tylko na ruch, który jest zawarty w wstępnie skonfigurowane listy kontroli dostępu (ACL).

Każdy z grup NSG mieć określone porty i protokoły umożliwiający rozwiązanie może działać bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej grupy NSG:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i są przechowywane na koncie magazynu
  - OMS [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) jest podłączony do dzienników diagnostycznych NSG.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane przechowywane przez szyfrowanie, bazy danych inspekcji i stosowania innych środków.

**Replikacja danych** Azure dla instytucji rządowych ma dwie opcje do [replikacji danych](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Domyślnym ustawieniem jest replikacja danych **magazynu geograficznie nadmiarowego (GRS)**, które asynchronicznie przechowuje dane klienta w centrum danych poza regionu podstawowego. Dzięki temu odzyskiwania danych w zdarzeniu całkowitej utraty dla centrum danych podstawowych.
 - **Lokalnie nadmiarowego magazynu (LRS)** można również skonfigurować za pomocą konta magazynu Azure. Magazyn LRS replikuje dane w ramach jednostki skalowania magazynu, który jest obsługiwany w tym samym regionie, w którym klient tworzy swojego konta. Wszystkie dane są replikowane jednocześnie, zapewniając, że żadne dane kopii zapasowej zostaną utracone w błąd jednostki skali podstawowego magazynu.

**Magazynu Azure** aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie usługi wdrożone w wykorzystać architektura to odwołanie [usługi Azure Storage](https://azure.microsoft.com/services/storage/), która przechowuje dane o [szyfrowanie usługi Magazyn](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Szyfrowanie dysków Azure**
[szyfrowania dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi. Rozwiązanie integruje się z usługą Azure Key Vault, aby kontrolować i zarządzać kluczami szyfrowania dysków.

**Baza danych SQL Azure** wystąpienie bazy danych SQL Azure używa następujących środków zabezpieczeń bazy danych:
-   [AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania konta magazynu platformy Azure.
-   Baza danych SQL jest skonfigurowana do używania [funkcji przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowania i odszyfrowywania danych i plików dziennika do ochrony informacji w stanie spoczynku. Funkcji TDE zawiera gwarantują, że przechowywanych danych nie zostało poddane nieautoryzowanego dostępu.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwić wszystkie dostęp do serwerów baz danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, podając alertów zabezpieczeń bazy danych podejrzanych działań, potencjalnych luk w zabezpieczeniach, ataki i nietypowych bazy danych programu access wzorce.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych, tylko aplikacje klienckie lub serwery aplikacji dostęp do kluczy można uzyskać dostępu do danych w postaci zwykłego tekstu.
-   [Baza danych SQL dane dynamiczne maskowanie](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) może odbywać się po wdraża architektura referencyjna struktury. Klienci muszą dostosować dane dynamiczne maskowanie ustawienia, aby stosować się do schematu bazy danych.

### <a name="logging-and-audit"></a>Rejestrowanie i inspekcja
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) generuje wyświetlania all-up monitorowania danych w tym działania dzienniki, metryki i dane diagnostyczne, umożliwiając klientom utworzyć pełny obraz kondycji systemu.  
[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) zapewnia szczegółowe rejestrowanie działania systemu i użytkownika, a także kondycji systemu. OMS [analizy dzienników](https://azure.microsoft.com/services/log-analytics/) rozwiązanie zbiera i analizuje dane generowane przez zasobami na platformie Azure i lokalnego środowiska.
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacji wykonywanych na zasobów w ramach subskrypcji.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdego zasobu. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows i magazynu obiektów Blob platformy Azure, tabele i kolejki dzienników.
- **Zapory Dzienniki**: bramy aplikacji udostępnia pełnej diagnostyki i dostępu do dzienników. Dzienniki zapory są dostępne dla zasobów włączone zapory aplikacji sieci Web Application Gateway.
- **Zaloguj się Archiwizacja**: wszystkie dzienniki diagnostyczne zapisu na konto magazynu Azure scentralizowane i zaszyfrowanego dla archiwizacji okres przechowywania określonych 2 dni. Te dzienniki nawiązać Analiza dzienników Azure do przetwarzania, przechowywania i raportowania pulpitu nawigacyjnego.

Ponadto następujące rozwiązania OMS są dołączone jako część tej architektury:
-   [Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): rozwiązanie do automatyzacji Azure przechowuje, uruchamia i zarządza elementami runbook.
-   [Bezpieczeństwo i inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpieczenia i inspekcji pulpit nawigacyjny zapewnia wysokiego poziomu wgląd w stan zabezpieczeń zasobów podając metryki na domen zabezpieczeń, godne uwagi problemy wykryć, analizy zagrożeń i typowych kwerend zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożonym opracowuje dla klientów.
-   [Azure Dzienniki aktywności](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązania analizy dziennika aktywności obsługuje Analiza dzienników Azure działania we wszystkich subskrypcji platformy Azure dla danego klienta.

### <a name="identity-management"></a>Zarządzanie tożsamościami
-   Aby aplikacja uwierzytelniania przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi Azure AD do uwierzytelniania aplikacji z bazą danych SQL Azure. Aby uzyskać więcej informacji, zobacz temat jak [chronią poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki tożsamości organizacji, konfiguruje automatyczne odpowiedzi wykryte podejrzane działania związane z tożsamości organizacji i sprawdza podejrzane zdarzenia podjęcie odpowiednich działań, aby je rozwiązać.
-   [Azure opartej na rolach kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia fokus zarządzania dostępem dla platformy Azure. Subskrypcja dostęp jest ograniczony do administratora subskrypcji.

Aby dowiedzieć się więcej o korzystaniu z funkcji zabezpieczeń bazy danych SQL Azure, zobacz [aplikacja demonstracyjna Clinic Contoso](https://github.com/Microsoft/azure-sql-security-sample) próbki.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie kluczy tajnych**: w tym rozwiązaniu zastosowano [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) zarządzania kluczy i kluczy tajnych. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="expressroute-and-vpn"></a>ExpressRoute i sieci VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub bezpieczny tunel sieci VPN musi być skonfigurowana bezpiecznie nawiązać połączenia z zasobami wdrożenia w ramach tej architektury odwołanie do analizy danych. Jako połączeń ExpressRoute nie przechodzą w Internecie, te połączenia oferują więcej niezawodności, szybkości szybsze niższe opóźnienia i lepsze zabezpieczenia niż typowe połączenia za pośrednictwem Internetu. Konfigurując odpowiednie ExpressRoute i sieci VPN, klienci można dodać warstwę ochrony danych podczas przesyłania.

### <a name="azure-active-directory-setup"></a>Instalacja usługi Azure Active Directory
[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest niezbędne do zarządzania wdrażania i inicjowania obsługi dostępu do personelu interakcji ze środowiskiem. Istniejący Windows Server Active Directory można zintegrować z usługi AAD w [cztery kliknięć](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Klientów można również powiązanie wdrożonej infrastruktura usługi Active Directory (kontrolery domeny) do istniejącej usługi AAD dokonując wdrożonej infrastruktura usługi Active Directory jest poddomeną las usługi AAD.

### <a name="additional-services"></a>Usługi dodatkowe
#### <a name="iaas---vm-vonsiderations"></a>IaaS - vonsiderations maszyny Wirtualnej
To rozwiązanie typu PaaS nie obejmuje żadnych maszyn wirtualnych IaaS platformy Azure. Klienta można utworzyć maszyny Wirtualnej platformy Azure, aby uruchomić wiele z tych usług PaaS. W takim przypadku określonych funkcji i usług ciągłość prowadzenia działalności biznesowej i OMS można wykorzystać:

##### <a name="business-continuity"></a>Ciągłość działalności biznesowej
- **Wysoka dostępność**: obciążenia serwera są pogrupowane w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pomaga zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Co najmniej jednej maszyny wirtualnej jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, spotkania 99,95% umowy SLA platformy Azure.

- **Magazyn usług odzyskiwania**: [magazyn usług odzyskiwania](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. W magazynie usług odzyskiwania klientów można przywrócić pliki i foldery z maszyn wirtualnych IaaS bez przywracania całą maszynę Wirtualną, umożliwiające szybsze przywracania.

##### <a name="oms"></a>OMS
-   [Ocena AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i udostępnia listę zalecenia dotyczące infrastruktury serwera wdrożonym z określonymi priorytetami.
-   [Ocena ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): rozwiązanie chroniące przed złośliwym kodem raportów dotyczących stanu złośliwego oprogramowania, zagrożeń i ochrony.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): rozwiązanie do zarządzania aktualizacji umożliwia zarządzanie klienta aktualizacje zabezpieczeń systemu operacyjnego, w tym stanu o dostępnych aktualizacjach i proces instalacji wymaganych aktualizacji.
-   [Agent kondycji](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): kondycja agenta rozwiązania zgłasza liczbę agentów są wdrażane i ich rozmieszczenie geograficzne, a także liczbę agentów, które odpowiadają i liczby agentów, które są przesyłanie danych operacyjnych.
-   [Śledzenie zmian](https://docs.microsoft.com/azure/automation/automation-change-tracking): śledzenie zmian rozwiązania pozwala łatwo zidentyfikować zmiany w środowisku.

##### <a name="security"></a>Bezpieczeństwo
- **Ochrona przed złośliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, że pomaga zidentyfikować i usunąć wirusy, programy szpiegujące lub inne złośliwe oprogramowanie, można skonfigurować alerty Jeśli znane złośliwego lub niechcianego oprogramowania próbuje zainstalować lub uruchomić na chronionych maszyn wirtualnych.
- **Zarządzanie poprawkami**: maszyny wirtualne systemu Windows wdrożone w ramach tej architektury odwołania są domyślnie skonfigurowane, odbierania automatycznych aktualizacji z usługi Windows Update. To rozwiązanie obejmuje również OMS [usługi Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których można utworzyć wdrożenia zaktualizowane maszynom wirtualnym poprawki w razie potrzeby.

#### <a name="azure-commercial"></a>Komercyjnej Azure
Mimo że tej architektury analizy danych nie jest przeznaczony do wdrożenia na [Azure komercyjnych](https://azure.microsoft.com/overview/what-is-azure/) środowiska, podobne cele można osiągnąć za pomocą usługi opisane w tej architekturze referencyjnej oraz dodatkowe usługi dostępne tylko w środowiskach komercyjnych Azure. Należy pamiętać, komercyjnych Azure obsługuje FedRAMP JAB P-ATO na umiarkowany poziom wpływu, dzięki czemu agencji rządowych i partnerami, aby wdrażać w chmurze, wykorzystując środowiska Azure handlowego średnio poufne informacje.

Komercyjnej Azure oferuje szerokiej gamy usług analizy dla ściąganie insights poza dużych ilości danych:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), składnika [pakietu Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), rozwija modelu analizy predykcyjnej z jednego lub więcej źródeł danych. Funkcje statystyczne są używane przez wiele iteracji do wygenerowania efektywną modelu, który będą mogły używać aplikacji, takich jak usługi Power BI.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) umożliwia przechwytywanie danych dowolnego rozmiar, typ i wprowadzanie szybkości w jednym miejscu na potrzeby analiz operacyjnych i poznawczych. Azure Data Lake Store jest zgodna z najbardziej otwartych składników źródła w ekosystemie Hadoop i bardzo dobrze integruje się z innymi usługami Azure.

## <a name="threat-model"></a>Modelu zagrożeń

Diagram przepływu danych (DPD) dla tej architektury dokumentacja jest dostępna dla [Pobierz](https://aka.ms/blueprintanalyticsthreatmodel) lub znajduje się poniżej:

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Azure zabezpieczeń i zgodności planu — FedRAMP wysokiej klienta odpowiedzialność macierzy](https://aka.ms/blueprinthighcrm) zawiera wszystkie opcje zabezpieczeń wymagane przez wysokiej FedRAMP linii bazowej. Podobnie [Azure zabezpieczeń i zgodności planu — umiarkowane macierzy odpowiedzialność klienta FedRAMP](https://aka.ms/blueprintanalyticscimmod) zawiera wszystkie opcje zabezpieczeń wymagane przez umiarkowany FedRAMP linii bazowej. Zarówno dokumentów można ustalić, czy implementacja każdej kontrolki jest odpowiedzialność firmy Microsoft, odbiorcy, lub udostępniane między nimi.

[Zabezpieczeń platformy Azure i plan zgodności - FedRAMP wysokiej macierzy implementacji kontroli](https://aka.ms/blueprintanalyticscimhigh) i [Azure zabezpieczeń i zgodności planu — umiarkowane macierzy implementacji kontroli FedRAMP](https://aka.ms/blueprintanalyticscimmod) Podaj informacje, na którym formanty są objęte architektura analytics dla każdej linii bazowej FedRAMP, w tym szczegółowy opis sposobu implementacji spełnia wymagania dotyczące każdego objęte formantu.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.
 - Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produktem firmy Microsoft lub rozwiązania.
 - Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym.
 - Ten dokument został utworzony jako odwołanie i nie należy używać do definiowania oznacza, że wszystkie za pomocą których klient może spełniać określone wymagania i przepisami. Klienci powinien poszukuje prawne z organizacji w implementacjach klienta zatwierdzone.
