---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — analiza dla FedRAMP
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — analiza dla FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 7b07fee46bce4c7b80346eb0b4c0fccd5245d87f
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946871"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure: Analiza dla FedRAMP

## <a name="overview"></a>Omówienie

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) to Stany Zjednoczone program dla instytucji rządowych, który zapewnia ustandaryzowane podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania produktów i usług w chmurze. Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera wskazówki dotyczące sposobu dostarczania architektury analizy Microsoft Azure, która pomaga zaimplementować podzestaw FedRAMP o wysokiej kontroli. To rozwiązanie zapewnia wskazówki dotyczące wdrażania i konfigurowania zasobów platformy Azure na potrzeby wspólnej architektury referencyjnej, pokazując sposoby, w których klienci mogą spełniać określone wymagania w zakresie zabezpieczeń i zgodności, a także służy jako podstawa dla klientów Twórz i Konfiguruj własne rozwiązania analityczne na platformie Azure.

Ta architektura referencyjna, przewodniki związane z implementacją kontroli i modele zagrożeń mają służyć jako podstawa dla klientów, którzy dostosowują się do określonych wymagań i nie powinny być używane w środowisku produkcyjnym. Wdrożenie aplikacji w tym środowisku bez modyfikacji nie jest wystarczające, aby całkowicie spełnić wymagania FedRAMP wysokiego planu bazowego. Pamiętaj o następujących kwestiach:
- Architektura udostępnia linię bazową, która ułatwia klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z FedRAMP.
- Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki

To rozwiązanie zapewnia platformę analityczną, na której klienci mogą tworzyć własne narzędzia analityczne. Architektura referencyjna przedstawia ogólny przypadek użycia, w którym klienci wprowadzają dane przy użyciu zbiorczych danych importu przez administratora SQL/danych lub za pośrednictwem aktualizacji danych operacyjnych za pośrednictwem użytkownika operacyjnego. Oba strumienie służbowe zawierają [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) do importowania danych do SQL Database. Azure Functions musi być skonfigurowany przez klienta za pomocą witryny Azure Portal w celu obsługi zadań importu unikatowych dla potrzeb analizy poszczególnych klientów.

Microsoft Azure oferuje różne usługi raportowania i analiz dla klienta. to rozwiązanie obejmuje jednak Azure Analysis Services w połączeniu z Azure SQL Database, aby szybko przeglądać dane i dostarczać szybsze wyniki dzięki inteligentniejszemu modelowaniu danych klientów. Usługi Azure Analytics to forma uczenia maszynowego, która pozwala zwiększyć szybkość zapytań, odkrywając nowe relacje między zestawami danych. Po przeszkoleniu danych za pomocą kilku funkcji statystycznych, do 7 dodatkowych pul zapytań (łącznie z serwerem klienta) można synchronizować z tymi samymi modelami tabelarycznymi, aby rozłożyć obciążenie zapytań i skrócić czasy odpowiedzi.

W celu uzyskania rozszerzonej analizy i raportowania bazy danych SQL można skonfigurować przy użyciu indeksów magazynu kolumn. Zarówno usługi Azure Analytics, jak i bazy danych SQL można skalować w górę lub w dół lub całkowicie wyłączać w odpowiedzi na użycie klientów. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL w ramach dołączania certyfikatów z podpisem własnym. Najlepszym rozwiązaniem jest to, że platforma Azure zaleca korzystanie z zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.

Gdy dane zostaną przekazane do Azure SQL Database i przeszkolone przez Azure Analysis Services, zostanie ono rozszyfrowane przez zarówno użytkownika operacyjnego, jak i administratora SQL/danych z Power BI. Power BI wyświetla dane intuicyjnie i pobiera razem informacje w wielu zestawach danych w celu uzyskania większego wglądu. Wysoki stopień adaptacji i łatwej integracji z Azure SQL Database zapewnia klientom możliwość skonfigurowania jej do obsługi szerokiej gamy scenariuszy, zgodnie z potrzebami biznesowymi.

Całe rozwiązanie jest oparte na usłudze Azure Storage, którą klienci konfigurują w witrynie Azure Portal. Usługa Azure Storage szyfruje wszystkie dane za pomocą szyfrowanie usługi Storage, aby zachować poufność danych przechowywanych w spoczynku.  Magazyn Geograficznie nadmiarowy (GRS) gwarantuje, że niekorzystne zdarzenie w podstawowym centrum danych klienta nie spowoduje utraty danych, ponieważ druga kopia zostanie zapisana w oddzielnej lokalizacji setki kilometrów.

W celu zwiększenia bezpieczeństwa ta architektura zarządza zasobami przy użyciu Azure Active Directory i Azure Key Vault. Kondycja systemu jest monitorowana za Azure Monitor. Klienci konfigurują obie usługi monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwym do nawigacji Pulpitie nawigacyjnym.

Azure SQL Database jest często zarządzany za pośrednictwem SQL Server Management Studio (SSMS), który działa z komputera lokalnego skonfigurowanego do uzyskiwania dostępu do Azure SQL Database za pośrednictwem bezpiecznego połączenia VPN lub ExpressRoute. **Platforma Azure zaleca skonfigurowanie połączenia sieci VPN lub usługi Azure ExpressRoute na potrzeby zarządzania i importowania danych do grupy zasobów architektura referencyjna.**

![Analiza diagramu architektury referencyjnej FedRAMP](images/fedramp-analytics-reference-architecture.png?raw=true "Analiza diagramu architektury referencyjnej FedRAMP")

### <a name="roles"></a>Role
Plan analizy przedstawia scenariusz z trzema ogólnymi typami użytkowników: użytkownikiem operacyjnym, administratorem SQL/danych i inżynierem systemów. Access Control oparte na rolach (RBAC) na platformie Azure umożliwia wdrożenie precyzyjnego zarządzania dostępem za pośrednictwem wbudowanych ról niestandardowych. Zasoby są dostępne do konfigurowania [Access Control opartej na rolach](../../role-based-access-control/role-assignments-portal.md) i tworzenia konspektów oraz implementowania [wstępnie zdefiniowanych ról](../../role-based-access-control/built-in-roles.md).

#### <a name="systems-engineer"></a>Inżynier systemów
Inżynier systemów posiada subskrypcję klienta platformy Azure i konfiguruje wdrożenie rozwiązania za pomocą witryny Azure Portal.

#### <a name="sqldata-administrator"></a>Administrator SQL/dane
Administrator SQL/Data tworzy funkcję importu danych zbiorczych i funkcję aktualizacji danych operacyjnych na potrzeby przekazywania do usługi Azure SQL Database. Administrator SQL/Data nie odpowiada za żadne aktualizacje danych operacyjnych w bazie danych, ale będzie mogła wyświetlać dane za pomocą Power BI.

#### <a name="operational-user"></a>Użytkownik operacyjny
Użytkownik operacyjny regularnie aktualizuje dane i jest własnością codziennej generacji danych. Użytkownik operacyjny interpretuje także wyniki przez Power BI.

### <a name="azure-services"></a>Usługi Azure

To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .
- Azure Functions
- Azure SQL Database
- Usługa Azure Analysis Service
- Usługa Azure Active Directory
- W usłudze Azure Key Vault
- Azure Monitor (dzienniki)
- Azure Storage
- ExpressRoute/VPN Gateway
- Pulpit nawigacyjny usługi Power BI

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów deweloperskich i implementacji.

![tekst alternatywny](images/fedramp-analytics-components.png?raw=true "Analiza dla diagramu składników FedRAMP")

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) są rozwiązaniami do uruchamiania małych fragmentów kodu w chmurze za pośrednictwem większości języków programowania. Funkcje w tym rozwiązaniu integrują się z usługą Azure Storage, aby automatycznie ściągać dane klientów do chmury, ułatwiając integrację z innymi usługami platformy Azure. Funkcje są łatwo skalowalne i ponoszą koszt tylko wtedy, gdy są uruchomione.

**Usługa Azure Analysis Service**: [Usługa Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) zapewnia modelowanie i integrację danych w przedsiębiorstwie z usługami Azure Data Platform. Usługa Azure Analysis Service przyspiesza przeglądanie przez duże ilości danych przez połączenie danych z wielu źródeł w jednym modelu danych.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) udostępnia funkcje analizy i raportowania dla klientów próbujących uzyskać więcej informacji na temat wysiłków związanych z przetwarzaniem danych.

### <a name="networking"></a>Networking
**Sieciowe grupy zabezpieczeń**: [Sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) są skonfigurowane do zarządzania ruchem skierowanym do wdrożonych zasobów i usług. Sieciowe grupy zabezpieczeń są ustawiane na schemat odmowy przez domyślny i zezwalają na ruch zawarty na liście wstępnie skonfigurowanych Access Control (ACL).

Każdy sieciowych grup zabezpieczeń ma otwarte określone porty i protokoły, dzięki czemu rozwiązanie może być bezpieczne i poprawne. Ponadto dla każdego sieciowej grupy ZABEZPIECZEŃu są włączone następujące konfiguracje:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
  - [Dzienniki Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md) są połączone z dziennikami diagnostycznymi sieciowej grupy zabezpieczeń.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane przechowywane przez szyfrowanie, inspekcję bazy danych i inne miary.

**Replikacja danych** Azure Government ma dwie opcje [replikacji danych](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Domyślne ustawienie replikacji danych to **Magazyn Geograficznie nadmiarowy (GRS)** , który asynchronicznie przechowuje dane klientów w osobnym centrum danych poza regionem podstawowym. Zapewnia to odzyskiwanie danych w łącznym zdarzeniu utraty dla podstawowego centrum danych.
 - **Magazyn lokalnie nadmiarowy (LRS)** można skonfigurować Alternatywnie za pomocą konta usługi Azure Storage. LRS replikuje dane w jednostce skalowania magazynu, która jest hostowana w tym samym regionie, w którym klient tworzy swoje konto. Wszystkie dane są replikowane współbieżnie, co gwarantuje, że żadne dane kopii zapasowej nie zostaną utracone w przypadku awarii podstawowej jednostki skalowania magazynu.

**Usługa Azure Storage** Aby zaspokoić zaszyfrowane dane w wymaganiach dotyczących REST, wszystkie usługi wdrożone w tej architekturze referencyjnej wykorzystują [usługę Azure Storage](https://azure.microsoft.com/services/storage/), która przechowuje dane w [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md).

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) wykorzystuje funkcję BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla systemu operacyjnego i dysków danych. Rozwiązanie integruje się z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**Azure SQL Database** W wystąpieniu Azure SQL Database są stosowane następujące miary zabezpieczeń bazy danych:
-   [Uwierzytelnianie i autoryzacja w usłudze AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
-   SQL Database jest skonfigurowany do używania [transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje szyfrowanie w czasie rzeczywistym i odszyfrowywanie danych i plików dziennika w celu ochrony informacji przechowywanych w pamięci podręcznej. TDE zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](../../sql-database/sql-database-threat-detection.md) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalne luki w zabezpieczeniach, ataki z iniekcją SQL i nietypowe wzorce dostępu do bazy danych.
-   [Always Encrypted kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
-   [SQL Database dynamiczną maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) można wykonać po wdrożeniu architektury referencyjnej. Klienci będą musieli dostosować ustawienia dynamicznego maskowania danych, aby były zgodne ze schematem bazy danych.

### <a name="logging-and-audit"></a>Rejestrowanie i inspekcja
[Azure monitor](../../azure-monitor/overview.md) generuje cały ekran danych monitorowania, w tym dzienniki aktywności, metryki i dane diagnostyczne, umożliwiając klientom tworzenie kompletnego obrazu kondycji systemu.  
[Dzienniki Azure monitor](../azure-security-disk-encryption-overview.md) zapewniają obszerne rejestrowanie aktywności systemu i użytkownika, a także kondycję systemu. Zbiera i analizuje dane generowane przez zasoby na platformie Azure i w środowiskach lokalnych.
- **Dzienniki aktywności**: [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/diagnostic-logs-overview.md) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Te dzienniki obejmują Dzienniki systemu Windows i magazyn obiektów blob platformy Azure, tabele i dzienniki kolejki.
- **Dzienniki zapory**: Application Gateway zapewnia pełną dzienników diagnostycznych i dostępu. Dzienniki zapory są dostępne dla zasobów Application Gateway z włączoną obsługą usługi WAF.
- **Archiwizowanie dzienników**: Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage na potrzeby archiwizacji ze zdefiniowanym okresem przechowywania wynoszącym 2 dni. Te dzienniki łączą się z dziennikami Azure Monitor na potrzeby przetwarzania, przechowywania i raportowania pulpitów nawigacyjnych.

Ponadto w ramach tej architektury są dołączone następujące rozwiązania do monitorowania:
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation rozwiązanie przechowuje elementy Runbook, uruchamia je i zarządza nimi.
-   [Security and Audit](../../security-center/security-center-intro.md): Pulpit nawigacyjny Security and Audit zawiera szczegółowe informacje o stanie zabezpieczeń zasobów dzięki zapewnieniu metryk w domenach zabezpieczeń, istotnych problemach, wykryciach, analizie zagrożeń i typowych zapytaniach zabezpieczeń.
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie Sprawdzanie kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu oraz zapewnia klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
-   [Dzienniki aktywności platformy Azure](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

### <a name="identity-management"></a>Zarządzanie tożsamościami
-   Uwierzytelnianie w aplikacji odbywa się przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ponadto szyfrowanie kolumn bazy danych używa usługi Azure AD do uwierzytelniania aplikacji do Azure SQL Database. Aby uzyskać więcej informacji, zobacz jak [chronić poufne dane w SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami organizacji i bada podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.
-   [Access Control oparte na rolach (RBAC) na platformie Azure](../../role-based-access-control/role-assignments-portal.md) umożliwia zarządzanie dostępem ukierunkowanym na platformę Azure. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.

Aby dowiedzieć się więcej o korzystaniu z funkcji zabezpieczeń Azure SQL Database, zobacz przykład [aplikacji demonstracyjnej firmy Contoso](https://github.com/Microsoft/azure-sql-security-sample) .

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie**wpisami tajnymi: Rozwiązanie używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="expressroute-and-vpn"></a>ExpressRoute i VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub bezpieczny tunel VPN musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej analizy danych. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Przez odpowiednie skonfigurowanie usługi ExpressRoute lub sieci VPN klienci mogą dodać warstwę ochrony danych podczas przesyłania.

### <a name="azure-active-directory-setup"></a>Konfiguracja Azure Active Directory
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) ma zasadnicze znaczenie dla zarządzania wdrożeniem i zapewnianiem dostępu do pracowników, którzy współpracują ze środowiskiem. Istniejące Active Directory systemu Windows Server można zintegrować z usługą AAD za pomocą [czterech kliknięć](../../active-directory/hybrid/how-to-connect-install-express.md). Klienci mogą również powiązać wdrożoną infrastrukturę Active Directory (kontrolery domeny) z istniejącą usługą AAD, wykonując wdrożoną infrastrukturę Active Directory poddomeną lasu usługi AAD.

### <a name="additional-services"></a>Usługi dodatkowe
#### <a name="iaas---vm-considerations"></a>IaaS — uwagi dotyczące maszyn wirtualnych
To rozwiązanie PaaS nie obejmuje maszyn wirtualnych IaaS platformy Azure. Klient może utworzyć maszynę wirtualną platformy Azure do uruchamiania wielu z tych usług PaaS. W takim przypadku można korzystać z określonych funkcji i usług w celu zapewnienia ciągłości działania i dzienników Azure Monitor:

##### <a name="business-continuity"></a>Ciągłość działalności biznesowej
- **Wysoka dostępność**: Obciążenia serwera są pogrupowane w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , aby zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Co najmniej jedna maszyna wirtualna jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, które spełnia warunki umowy SLA na 99,95%.

- **Magazyn Recovery Services**: [Magazyn Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowych i chroni wszystkie konfiguracje Virtual Machines platformy Azure w tej architekturze. W przypadku magazynu Recovery Services klienci mogą przywracać pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej, co umożliwia szybsze przywracanie.

##### <a name="monitoring-solutions"></a>Rozwiązania do monitorowania
-   [AD Assessment](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie Active Directory Sprawdzanie kondycji ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md): Rozwiązanie chroniące przed złośliwym kodem zgłasza złośliwe oprogramowanie, zagrożenia i stan ochrony.
-   [Update Management](../../automation/automation-update-management.md): Rozwiązanie Update Management umożliwia klientom zarządzanie aktualizacjami zabezpieczeń systemu operacyjnego, w tym stanem dostępnych aktualizacji i procesem instalowania wymaganych aktualizacji.
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, oraz liczby agentów przesyłających dane operacyjne.
-   [Change Tracking](../../automation/change-tracking.md): Rozwiązanie Change Tracking pozwala klientom łatwo identyfikować zmiany w środowisku.

##### <a name="security"></a>Bezpieczeństwo
- **Ochrona przed złośliwym oprogramowaniem**: [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware) Virtual Machines zapewnia ochronę w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie, z konfigurowalnymi alertami, gdy znane złośliwe lub niepożądane oprogramowanie próbuje Zainstaluj lub Uruchom na chronionych maszynach wirtualnych.
- **Zarządzanie poprawkami**: Maszyny wirtualne z systemem Windows wdrożone w ramach tej architektury referencyjnej są domyślnie skonfigurowane tak, aby otrzymywać aktualizacje automatyczne z usługi Windows Update. To rozwiązanie obejmuje również usługę [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , za pomocą której można tworzyć zaktualizowane wdrożenia w celu zastosowania poprawek do maszyn wirtualnych w razie potrzeby.

#### <a name="azure-commercial"></a>Komercyjne platformy Azure
Mimo że ta architektura analizy danych nie jest przeznaczona do wdrożenia w środowisku [komercyjnym platformy Azure](https://azure.microsoft.com/overview/what-is-azure/) , podobne cele można osiągnąć za pomocą usług opisanych w tej architekturze referencyjnej, a także dodatkowych dostępnych usług tylko w środowisku komercyjnym platformy Azure. Należy pamiętać, że komercyjne usługi platformy Azure utrzymują FedRAMP JAB P-ATO na poziomie umiarkowanego wpływu, umożliwiając instytucjom rządowym i partnerom wdrażanie średnio poufnych informacji w chmurze wykorzystujących środowisko komercyjne platformy Azure.

Komercyjna usługa Azure oferuje szeroką gamę usług analitycznych umożliwiających uzyskiwanie szczegółowych informacji z dużych ilości danych:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), składnik [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), opracowuje model analizy predykcyjnej na podstawie jednego lub większej liczby źródeł danych. Funkcje statystyczne są używane dla kilku iteracji w celu wygenerowania skutecznego modelu, który następnie może wykorzystać aplikacje, takie jak Power BI.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) umożliwia przechwytywanie danych dowolnego rozmiaru, typu i szybkości pozyskiwania w jednym miejscu na potrzeby analiz operacyjnych i poznawczych. Azure Data Lake Store jest zgodny z większością składników Open Source w ekosystemie usługi Hadoop i integruje dobrze z innymi usługami platformy Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych (DPD) dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/blueprintanalyticsthreatmodel) lub można go znaleźć poniżej:

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności
W [macierzy strategia zabezpieczeń i zgodności z przepisami platformy Azure — FedRAMP wysoka odpowiedzialność klienta](https://aka.ms/blueprinthighcrm) znajduje się lista wszystkich kontroli zabezpieczeń wymaganych przez FedRAMP High Baseline. Podobnie w przypadku [macierzy strategia zabezpieczeń i zgodności z przepisami platformy Azure — FedRAMP umiarkowanej odpowiedzialności klienta](https://aka.ms/blueprintanalyticscimmod) wyświetlana jest lista wszystkich kontroli zabezpieczeń wymaganych przez podstawową linię bazową FedRAMP. Oba dokumenty zawierają szczegółowe informacje o tym, czy implementacja poszczególnych kontroli jest odpowiedzialna za firmę Microsoft, klienta, czy współdzielona między nimi.

Macierz [implementacji strategia zabezpieczeń i zgodności z przepisami platformy Azure FedRAMP o wysokiej kontroli](https://aka.ms/blueprintanalyticscimhigh) i [strategia zabezpieczeń i zgodności z przepisami platformy Azure-FedRAMP umiarkowanej implementacji kontroli](https://aka.ms/blueprintanalyticscimmod) zawierają informacje o tym, które kontrolki są objęte Architektura analizy dla każdej linii bazowej FedRAMP, łącznie z szczegółowymi opisami tego, jak implementacja spełnia wymagania poszczególnych formantów objętych usługą.

## <a name="disclaimer"></a>Zastrzeżenie

 - Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
 - Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
 - Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
 - Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
 - Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
 - Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
