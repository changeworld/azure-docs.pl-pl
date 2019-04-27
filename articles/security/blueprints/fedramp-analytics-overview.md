---
title: Zabezpieczenia platformy Azure i zgodności planu - Analytics dla programu FedRAMP
description: Zabezpieczenia platformy Azure i zgodności planu - Analytics dla programu FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: fa10ff14bf893c268d6b6b1a0d181d11a3f27dc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586291"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Zabezpieczenia platformy Azure i zgodności planu: Analytics for FedRAMP

## <a name="overview"></a>Omówienie

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) to program obowiązkowo USA, zapewniający ustandaryzowane podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania dla chmury produkty i usługi. Tych samych zabezpieczeń platformy Azure i zgodności plan zawiera wskazówki dotyczące dostarczania architektury analizy Microsoft Azure, która pomaga implementuje podzbiór programowi FedRAMP High kontrolki. To rozwiązanie znajdują się wskazówki dotyczące wdrażania i konfiguracji zasobów platformy Azure dla typowych architektury referencyjnej, demonstrując sposób, w którym klienci mogą sprostać określonych wymagań dotyczących zabezpieczeń i zgodności i służy jako podstawa dla klientów Tworzenie i konfigurowanie rozwiązania do analizy na platformie Azure.

Ta architektura referencyjna przewodniki po wdrażaniu skojarzonego formantu i modele zagrożeń są przeznaczone do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym. Wdrażanie aplikacji w tym środowisku, bez żadnych modyfikacji jest za mało, aby całkowicie wymagań linii bazowej programowi FedRAMP High. Pamiętaj o następujących kwestiach:
- Architektura zapewnia plan bazowy, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z FedRAMP.
- Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników

To rozwiązanie zapewnia platforma analityczna, na którym klienci mogą tworzyć swoje własne narzędzia do analizy. Architektura referencyjna przedstawia przypadek użycia ogólny, gdzie klienci wejściowych danych za pośrednictwem importuje dane zbiorcze przez administratora SQL/danych lub aktualizacji danych operacyjnych za pomocą użytkownika usługi Operational. Obie działają strumieni zawierały [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) do importowania danych do bazy danych SQL. Usługa Azure Functions musi zostać skonfigurowane przez klienta za pomocą witryny Azure Portal do obsługi zadań importu unikatowe dla każdego klienta własne wymagania dotyczące Analityki.

Platforma Microsoft Azure oferuje szereg usług raportowania i analizy dla klienta; Jednak to rozwiązanie łączy w sobie usług Azure Analysis Services w połączeniu z usługi Azure SQL Database, aby szybko przeglądać dane i że dostarczymy wyniki szybciej za pośrednictwem inteligentniejsze modelowania danych klienta. Usługi analiz platformy Azure jest formą uczenia maszynowego przeznaczone do zwiększenia szybkości zapytań o nowe relacje między zestawami danych odnajdywania. Po danych ma przeszkoleni za pośrednictwem kilku funkcji statystycznych, maksymalnie 7 zapytania dodatkowych pul (8 całkowity, łącznie z serwerem klienta) mogą być synchronizowane z tych samych modeli tabelarycznych rozłożyć obciążenie zapytań i zmniejszyć czas reakcji.

Rozszerzone analiza i raportowanie baz danych SQL można skonfigurować na indeksach magazynu kolumn. Usługi analizy platformy Azure i bazy danych SQL można skalować w górę i w dół lub całkowicie wyłączony w odpowiedzi do użycia przez klientów. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL przez dołączenie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem jest Azure zaleca się korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.

Gdy dane są przekazywane do usługi Azure SQL Database i skonfigurowanych pod kątem usługi Azure Analysis Services, jest rozłożona się przez użytkownika operacyjną i administratora SQL/danych dzięki usłudze Power BI. Usługa Power BI wyświetla dane intuicyjnie i zbiera informacje o różnych wiele zestawów danych, aby narysować bardziej szczegółowe analizy dotyczące. Jego wysoki stopień zdolności adaptacyjnych i Łatwa integracja z usługą Azure SQL Database gwarantuje, czy klienci mogą skonfigurować go do obsługi szerokiej gamy scenariuszy zgodnie z wymogami swoich potrzeb biznesowych.

Całe rozwiązanie jest utworzonych na podstawie usługi Azure Storage, którego klienci konta skonfigurować w witrynie Azure Portal. Usługa Azure Storage szyfruje wszystkie dane przy użyciu szyfrowania usługi Storage, aby zachować poufność danych magazynowanych.  Geograficzne magazyn nadmiarowy (GRS) gwarantuje, że zdarzenie niepożądane, u klienta podstawowe centrum danych nie spowoduje utraty danych jako drugą kopię będą przechowywane w oddzielnej lokalizacji setki natychmiast mil.

Aby zwiększyć bezpieczeństwo Ta architektura zarządza zasobów za pomocą usługi Azure Active Directory i usługi Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Monitor. Klienci, skonfigurować zarówno usług monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwo można nawigować pulpitu nawigacyjnego.

Usługa Azure SQL Database zwykle odbywa się za pośrednictwem SQL Server Management Studio (SSMS), który jest uruchamiany z komputera lokalnego, która jest skonfigurowana do dostępu do bazy danych SQL Azure za pośrednictwem bezpiecznego połączenia sieci VPN lub usługi ExpressRoute. **Azure zaleca się skonfigurowanie połączenia sieci VPN lub usługi Azure ExpressRoute do importowania danych i zarządzania w grupie zasobów architektury odwołanie.**

![Analiza FedRAMP referencyjny diagram architektury](images/fedramp-analytics-reference-architecture.png?raw=true "analizy FedRAMP referencyjny diagram architektury")

### <a name="roles"></a>Role
Plan analytics opisano scenariusz z trzema typami ogólnymi użytkowników: użytkownika operacyjnej, administratora SQL/Data i inżynier systemów. Usługa Azure opartej na rolach kontrola dostępu (RBAC) umożliwia wdrożenie precyzyjnego dostępu do zarządzania za pomocą wbudowanych ról niestandardowych. Zasoby są dostępne do konfigurowania [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) konspekt i implementowanie [wstępnie zdefiniowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Inżynier systemów
Inżynier systemów jest właścicielem subskrypcji klienta na platformie Azure i umożliwia skonfigurowanie wdrażania rozwiązania w portalu Azure.

#### <a name="sqldata-administrator"></a>Administrator SQL/danych
Administratora SQL/danych określa funkcja importowania zbiorczego danych i funkcji aktualizacji danych operacyjnych do przekazywania do usługi Azure SQL database. Administrator SQL/danych nie jest odpowiedzialny za wszelkie aktualizacje danych operacyjnych w bazie danych, ale będą mogli wyświetlać dane w usłudze Power BI.

#### <a name="operational-user"></a>Operacyjne użytkownika
Użytkownik operacyjnej regularnie aktualizuje dane i której zostaje właścicielem generowania codziennych danych. Operacyjne użytkownika będzie również interpretować wyniki za pośrednictwem usługi Power BI.

### <a name="azure-services"></a>Usługi platformy Azure

To rozwiązanie korzysta z poniższych usług platformy Azure. Szczegółowe informacje na temat architektury wdrożenia [architektura wdrożenia](#deployment-architecture) sekcji.
- Azure Functions
- Azure SQL Database
- Azure Analysis Service
- Usługa Azure Active Directory
- W usłudze Azure Key Vault
- Usługa Azure Monitor (Dzienniki)
- Azure Storage
- Brama usługi ExpressRoute/VPN
- Pulpit nawigacyjny usługi Power BI

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy projektowania i implementacji.

![tekst alternatywny](images/fedramp-analytics-components.png?raw=true "Analytics dla diagramu składników FedRAMP")

**Usługa Azure Functions**: [Usługa Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) przedstawiono rozwiązania umożliwiające uruchamianie małych fragmentów kodu w chmurze, za pośrednictwem większość języków programowania. Funkcje, w tym rozwiązaniu integracji z usługą Azure Storage na automatyczne ściągnięcie danych klienta do chmury w ułatwienia integracji z innymi usługami platformy Azure. Funkcje są łatwo skalowalne i generują kosztów, gdy są uruchomione.

**Usługa Azure Analysis**: [Usługa analizy](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) zapewnia modelowanie danych przedsiębiorstwa i integrację z usługami platformy danych Azure. Usługa Azure Analysis Service przyspiesza przeglądanie dużych ilości danych dzięki połączeniu danych z wielu źródeł do pojedynczego modelu danych.

**Power BI**: [Usługa Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) zapewnia analizy i raportowania funkcje dla klientów próby ściągania bardziej szczegółowe analizy dotyczące poza ich nakłady na przetwarzanie danych.

### <a name="networking"></a>Networking
**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) są skonfigurowane do zarządzania ruchem, skierowanych do wdrożonych zasobów i usług. Sieciowe grupy zabezpieczeń są ustawione na Odmów przez domyślny schemat i tylko zezwalać na ruch, który jest zawarty w wstępnie skonfigurowanej listy kontroli dostępu (ACL).

Sieciowe grupy zabezpieczeń mają określonych portów i protokołów, otwórz rozwiązanie może pracować bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej sieciowej grupy zabezpieczeń:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
  - [Dzienniki platformy Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) jest podłączony do dzienników diagnostycznych do sieciowych grup zabezpieczeń.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane za pomocą funkcji szyfrowania, inspekcja bazy danych i innych miar.

**Replikacja danych** Azure dla instytucji rządowych ma dwie opcje do [replikacji danych](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Domyślnym ustawieniem jest replikacja danych **magazyn geograficznie nadmiarowy (GRS)**, które asynchronicznie przechowuje dane klienta w centrum danych poza regionem podstawowym. Zapewnia to odzyskiwanie danych w zdarzeniu całkowitej utraty podstawowe centrum danych.
 - **Magazyn lokalnie nadmiarowy (LRS)** można również skonfigurować za pomocą konta usługi Azure Storage. LRS replikuje dane w jednostce skalowania magazynu, który znajduje się w tym samym regionie, w której klient tworzy swojego konta. Wszystkie dane są replikowane w współbieżnie, zapewniając, że nie dane kopii zapasowej zostaną utracone błąd jednostki skali magazynu głównego.

**Usługa Azure Storage** aby sprostać zaszyfrowanych danych na pozostałe wymagania, wszystkich usług wdrożonych w tej używają architektury odwołanie [usługi Azure Storage](https://azure.microsoft.com/services/storage/), która przechowuje dane za pomocą [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Usługa Azure Disk Encryption**
[usługi Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. To rozwiązanie integruje się z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Usługa Azure SQL Database** wystąpienia usługi Azure SQL Database wykorzystuje następujące środki bezpieczeństwa bazy danych:
-   [Usługi AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
-   Baza danych SQL jest skonfigurowana do używania [przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie danych i plików dziennika do ochrony informacji w stanie spoczynku. Funkcja TDE zapewnia pewność, że przechowywanych danych nie została poddana przed nieautoryzowanym dostępem.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i dostęp do bazy danych nietypowe wzorce.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych, tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą dostęp do danych w postaci zwykłego tekstu.
-   [Baza danych SQL dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) może odbywać się po architektura referencyjna wdraża. Klienci będą musieli dostosować dane dynamiczne maskowanie ustawienia, aby stosować się do ich schemat bazy danych.

### <a name="logging-and-audit"></a>Rejestrowanie i inspekcja
[Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) generuje wyświetlanie całej monitorowania danych, w tym Dzienniki aktywności, metryki i dane diagnostyczne, dzięki czemu klienci mogą tworzyć pełny obraz kondycji systemu.  
[Dzienniki platformy Azure Monitor](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) zapewnia szczegółowe rejestrowanie aktywności systemu i użytkownika, a także kondycji systemu. Zbiera i analizuje dane generowane przez zasoby na platformie Azure i środowiskach lokalnych.
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows i magazynu Azure Blob, tabel i Dzienniki kolejek.
- **Dzienniki zapory**: Application Gateway zapewnia pełną diagnostycznych i dostęp do dzienników. Dzienniki zapory są dostępne dla zasobów z obsługą zapory aplikacji sieci Web bramy aplikacji.
- **Archiwizowanie dziennika**: Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji okres przechowywania zdefiniowanych 2 dni. Te dzienniki nawiązać dzienniki usługi Azure Monitor do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym.

Ponadto następujące rozwiązania do monitorowania, są uwzględnione w ramach tej architektury:
-   [Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Rozwiązania usługi Azure Automation przechowuje, uruchamia i zarządza elementami runbook.
-   [Zabezpieczenia i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Pulpit nawigacyjny zabezpieczenia i inspekcja zawiera ogólne informacje o stanie zabezpieczeń zasobów, zapewniając metryki w przypadku domen zabezpieczeń, problemy godne uwagi, wykrywania, analizy zagrożeń i typowe zapytania dotyczące zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

### <a name="identity-management"></a>Zarządzanie tożsamościami
-   Do aplikacji uwierzytelniania przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi Azure AD można uwierzytelnić aplikację do usługi Azure SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki mające wpływ na tożsamości w organizacji, umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości organizacji i wsparcia podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.
-   [Usługa Azure opartej na rolach kontrola dostępu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia skupia się zarządzanie dostępem dla platformy Azure. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.

Aby dowiedzieć się więcej o korzystaniu z funkcji zabezpieczeń usługi Azure SQL Database, zobacz [aplikacji pokazowej Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) próbki.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="expressroute-and-vpn"></a>Usługi ExpressRoute i sieci VPN
[Usługa ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub bezpieczny tunel sieci VPN musi zostać skonfigurowany nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej analizy danych. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Konfigurując odpowiednio ExpressRoute lub sieci VPN, klientów można dodać warstwę ochrony danych podczas przesyłania.

### <a name="azure-active-directory-setup"></a>Konfiguracja usługi Azure Active Directory
[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest Zarządzanie wdrożeniem i inicjowania obsługi dostępu do personelu wchodzenie w interakcje ze środowiskiem. Istniejącego systemu Windows Server Active Directory można zintegrować z usługą AAD w [cztery kliknięcia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Klienci również powiązać wdrożonej infrastrukturę usługi Active Directory (kontrolery domeny) do istniejącej usługi AAD, wprowadzając wdrożonej infrastrukturę usługi Active Directory poddomeny las usługi AAD.

### <a name="additional-services"></a>Usługi dodatkowe
#### <a name="iaas---vm-considerations"></a>IaaS — zagadnienia dotyczące maszyn wirtualnych
To rozwiązanie PaaS nie zawierać żadnych maszyn wirtualnych IaaS platformy Azure. Klient może utworzyć Maszynę wirtualną platformy Azure do uruchomienia wielu z tych usług PaaS. W tym przypadku określonych funkcji i usług dla ciągłości działania i dzienniki usługi Azure Monitor może wykorzystać:

##### <a name="business-continuity"></a>Ciągłość działalności biznesowej
- **Wysoka dostępność**: Obciążenia serwera są zgrupowane w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) do pomagają zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Co najmniej jednej maszyny wirtualnej są dostępne podczas planowanych lub nieplanowanych zdarzeń związanych z konserwacją spotkania 99,95% umowy SLA platformy Azure.

- **Magazyn usług Recovery Services**: [Magazyn usługi Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. Przy użyciu magazynu usługi Recovery Services klienci mogą przywracać pliki i foldery z maszyny Wirtualnej IaaS bez przywracania całej maszyny Wirtualnej, umożliwiając skraca czas ich przywracania.

##### <a name="monitoring-solutions"></a>Rozwiązania do monitorowania
-   [Ocena usługi AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Ocena ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Rozwiązanie chroniące przed złośliwym kodem raporty dotyczące złośliwego oprogramowania, zagrożeń i ochronę stanu.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Rozwiązanie Update Management umożliwia zarządzanie klientami z aktualizacjami zabezpieczeń systemu operacyjnego, w tym stan dostępnych aktualizacji i procesu instalacji wymaganych aktualizacji.
-   [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Śledzenie zmian](https://docs.microsoft.com/azure/automation/automation-change-tracking): Rozwiązanie Change Tracking umożliwia klientom łatwo identyfikować zmiany w środowisku.

##### <a name="security"></a>Bezpieczeństwo
- **Ochrona przed szkodliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania, można skonfigurować alerty, gdy znany złośliwego lub niechcianego oprogramowania próbuje zainstalowania lub uruchomienia na chronionych maszynach wirtualnych.
- **Zarządzanie poprawkami**: Windows wdrożone maszyny wirtualne w ramach tej architektury referencyjnej są domyślnie skonfigurowane, automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To rozwiązanie obejmuje również [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których zaktualizowano wdrożenia mogą być tworzone do poprawki maszyny wirtualne w razie.

#### <a name="azure-commercial"></a>Azure Commercial
Mimo że ta architektura analizy danych nie jest przeznaczony do wdrożenia [wersji Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) środowiska, podobnie cele można osiągnąć za pomocą usług opisanych w tej architekturze referencyjnej także dodatkowe usługi tylko w środowiskach komercyjnych platformy Azure. Należy pamiętać, że wersji Azure Commercial utrzymuje FedRAMP JAB P-ATO na poziomie umiarkowany wpływ, dzięki niemu agencje rządowe i partnerami w celu wdrażania umiarkowanie poufnych informacji w chmurze, wykorzystując wersji Azure Commercial środowiska.

Komercyjne platformy Azure oferuje szeroką gamę usług analizy dla ściąganie szczegółowych informacji z dużych ilości danych:
-   [Usługa Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), składnik pakietu [pakietu Cortana Intelligence](https://azure.microsoft.com/overview/ai-platform/), opracowuje modelu analizy predykcyjnej z co najmniej jedno źródło danych. Funkcje statystyczne są używane przez kilka iteracji do generowania skutecznego modelu, które następnie mogą używać aplikacje, takie jak Power BI.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) umożliwia przechwytywanie danych dowolnego rozmiaru, typu i szybkości wprowadzania w jednym miejscu na potrzeby analizy operacyjnej i rozpoznawczej. Azure Data Lake Store jest zgodna z najbardziej otwarta składniki źródła w ekosystemie usługi Hadoop i bardzo dobrze integruje się z innymi usługami Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych (DPD) dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/blueprintanalyticsthreatmodel) lub znajdują się poniżej:

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Zabezpieczeń platformy Azure i plan zgodność — FedRAMP wysokiej klienta odpowiedzialność macierzy](https://aka.ms/blueprinthighcrm) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez programowi FedRAMP High linii bazowej. Podobnie [zabezpieczeń platformy Azure i plan zgodność — FedRAMP umiarkowane klienta odpowiedzialność macierzy](https://aka.ms/blueprintanalyticscimmod) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez plan bazowy średni FedRAMP. Zarówno dokumentów szczegółów czy wykonania każdego formantu jest odpowiedzialność firmy Microsoft, klient lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i plan zgodność — FedRAMP wysokiej macierzy implementacji kontroli](https://aka.ms/blueprintanalyticscimhigh) i [zabezpieczeń platformy Azure i plan zgodność — FedRAMP macierzy kontroli umiarkowane w implementacji](https://aka.ms/blueprintanalyticscimmod) zapewniają informacje, na którym formanty są objęte architektura analizy dla każdej podstawy FedRAMP, w tym szczegółowy opis sposobu wdrożenia spełnia wymagania każdego formantu objętego usługą.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
 - W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
 - Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
 - Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
