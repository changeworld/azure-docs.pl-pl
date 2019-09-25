---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — magazyn danych dla automatyzacji FedRAMP
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — magazyn danych dla automatyzacji FedRAMP
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 7fe5b45d7719b34fff8c09d08f510dc465f50104
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257403"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure: Magazyn danych dla automatyzacji FedRAMP

## <a name="overview"></a>Przegląd

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) to Stany Zjednoczone program dla instytucji rządowych, który zapewnia ustandaryzowane podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania produktów i usług w chmurze. Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera wskazówki dotyczące sposobu dostarczania Microsoft Azure architekturę magazynu danych, która pomaga zaimplementować podzestaw FedRAMP o wysokiej kontroli. To rozwiązanie zapewnia wskazówki dotyczące wdrażania i konfigurowania zasobów platformy Azure na potrzeby wspólnej architektury referencyjnej, pokazując sposoby, w których klienci mogą spełniać określone wymagania w zakresie zabezpieczeń i zgodności, a także służy jako podstawa dla klientów Twórz i Konfiguruj własne rozwiązania magazynu danych na platformie Azure.

Ta architektura referencyjna, przewodniki związane z implementacją kontroli i modele zagrożeń mają służyć jako podstawa dla klientów, którzy dostosowują się do określonych wymagań i nie powinny być używane w środowisku produkcyjnym. Wdrożenie aplikacji w tym środowisku bez modyfikacji nie jest wystarczające, aby całkowicie spełnić wymagania FedRAMP wysokiego planu bazowego. Pamiętaj o następujących kwestiach:
- Architektura udostępnia linię bazową, która ułatwia klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z FedRAMP.
- Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki

To rozwiązanie zapewnia architekturę referencyjną magazynu danych implementującą wysoką wydajność i bezpieczny magazyn danych w chmurze. W tej architekturze są dwie osobne warstwy danych: jedna, w której dane są importowane, przechowywane i przemieszczane w ramach klastrowanego środowiska SQL, a drugi dla Azure SQL Data Warehouse, w którym dane są ładowane przy użyciu narzędzia ETL ( [np. zapytania](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL) do przetwarzania. Gdy dane są przechowywane w Azure SQL Data Warehouse, analiza może być uruchamiana na dużą skalę.

Microsoft Azure oferuje różne usługi raportowania i analiz dla klienta. To rozwiązanie obejmuje SQL Server Reporting Services (SSRS) umożliwiające szybkie tworzenie raportów z Azure SQL Data Warehouse. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL w ramach dołączania certyfikatów z podpisem własnym. Najlepszym rozwiązaniem jest to, że platforma Azure zaleca korzystanie z zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.

Dane w Azure SQL Data Warehouse są przechowywane w tabelach relacyjnych z magazynem kolumnowym, co znacznie zmniejsza koszty magazynowania danych przy jednoczesnym zwiększeniu wydajności zapytań.  W zależności od wymagań dotyczących użycia Azure SQL Data Warehouse zasoby obliczeniowe mogą być skalowane w górę lub w dół lub wyłączane całkowicie, jeśli nie ma aktywnych procesów wymagających zasobów obliczeniowych.

Moduł równoważenia obciążenia SQL zarządza ruchem SQL, zapewniając wysoką wydajność. Wszystkie maszyny wirtualne w tej architekturze referencyjnej wdrażają się w zestawie dostępności z wystąpieniami SQL Server skonfigurowanymi w grupie dostępności AlwaysOn na potrzeby funkcji wysokiej dostępności i odzyskiwania po awarii.

Ta architektura referencyjna magazynu danych obejmuje również warstwę Active Directory (AD) do zarządzania zasobami w ramach architektury. Podsieć Active Directory umożliwia łatwe wdrażanie w ramach większej struktury lasów usługi AD, co pozwala na ciągłą eksploatację środowiska, nawet gdy dostęp do większego lasu jest niedostępny. Wszystkie maszyny wirtualne są przyłączone do domeny Active Directory i używają zasad grupy Active Directory, aby wymusić konfiguracje zabezpieczeń i zgodności na poziomie systemu operacyjnego.

Maszyna wirtualna służy jako host zarządzania bastionu, co zapewnia bezpieczne połączenie dla administratorów w celu uzyskania dostępu do wdrożonych zasobów. Dane są ładowane do obszaru przejściowego za pomocą tego hosta zarządzania bastionu. **Platforma Azure zaleca skonfigurowanie połączenia sieci VPN lub usługi Azure ExpressRoute na potrzeby zarządzania i importowania danych do podsieci architektury referencyjnej.**

![Magazyn danych dla diagramu architektury referencyjnej FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "Magazyn danych dla diagramu architektury referencyjnej FedRAMP")

To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

Usługa Azure Virtual Machines
-   (1) Host bastionu
-   (2) Active Directory kontroler domeny
-   (2) SQL Server węzeł klastra
-   (1) monitor SQL Server

Zestawy dostępności
-   (1) Active Directory kontrolery domeny
-   (1) węzły klastra SQL i monitor

Sieć wirtualna
-   (4) podsieci
-   (4) sieciowe grupy zabezpieczeń

SQL Data Warehouse

SQL Server Reporting Services

Load Balancer Azure SQL

Usługa Azure Active Directory

Magazyn usługi Recovery Services

W usłudze Azure Key Vault

Dzienniki usługi Azure Monitor

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów deweloperskich i implementacji.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) to magazyn danych przedsiębiorstwa (rozszerzenia), który wykorzystuje wysoce równoległe przetwarzanie (MPP), aby szybko uruchamiać złożone zapytania w petabajtów danych. Zaimportuj dane Big Data do SQL Data Warehouse przy użyciu prostych zapytań T-SQL i korzystaj z możliwości MPP, aby uruchomić analizę o wysokiej wydajności.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) umożliwia szybkie tworzenie raportów za pomocą tabel, wykresów, map, mierników, macierzy i innych elementów Azure SQL Data Warehouse.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do wdrożonych zasobów w tym środowisku. Host bastionu zapewnia bezpieczne połączenie z wdrożonymi zasobami przez umożliwienie zdalnego ruchu z publicznych adresów IP na bezpiecznej liście. Aby zezwolić na ruch pulpitu zdalnego (RDP), źródło ruchu musi być zdefiniowane w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń).

Utworzono maszynę wirtualną jako przyłączony do domeny Host bastionu z następującymi konfiguracjami:
-   [Rozszerzenie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Rozszerzenie dzienników Azure Monitor](../../virtual-machines/windows/extensions-oms.md)
-   [Diagnostyka Azure rozszerzenie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) przy użyciu Azure Key Vault (przestrzega Azure Government, PCI DSS, HIPAA i innych wymagań)
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) w celu ograniczenia użycia zasobów maszyny wirtualnej, gdy nie są używane
-   Włączono funkcję [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) , dzięki czemu poświadczenia i inne wpisy tajne są uruchamiane w chronionym środowisku, które jest odizolowane od działającego systemu operacyjnego

### <a name="virtual-network"></a>Sieć wirtualna
Ta architektura referencyjna definiuje prywatną sieć wirtualną z przestrzenią adresową 10.0.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) zawierają listy Access Control (listy ACL), które zezwalają na ruch w sieci wirtualnej lub go odmawiają. Sieciowych grup zabezpieczeń może służyć do zabezpieczania ruchu w podsieci lub na poziomie poszczególnych maszyn wirtualnych. Następująca sieciowych grup zabezpieczeń istnieje:
  - SIECIOWEJ grupy zabezpieczeń dla warstwy danych (klastrów SQL Server, monitora SQL Server i Load Balancer SQL)
  - SIECIOWEJ grupy zabezpieczeń dla hosta zarządzania bastionu
  - SIECIOWEJ grupy zabezpieczeń dla Active Directory
  - SIECIOWEJ grupy zabezpieczeń dla SQL Server Reporting Services

Każdy sieciowych grup zabezpieczeń ma otwarte określone porty i protokoły, dzięki czemu rozwiązanie może być bezpieczne i poprawne. Ponadto dla każdego sieciowej grupy ZABEZPIECZEŃu są włączone następujące konfiguracje:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
  - Dzienniki Azure Monitor są połączone z [diagnostyką sieciowej grupy zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: Każda podsieć jest skojarzona z odpowiadającą jej sieciowej grupy zabezpieczeń.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane przechowywane przez szyfrowanie, inspekcję bazy danych i inne miary.

**Usługa Azure Storage** Aby zaspokoić zaszyfrowane dane w wymaganiach dotyczących REST, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używają [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md).

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

### <a name="business-continuity"></a>Ciągłość działalności biznesowej
**Wysoka dostępność**: Obciążenia serwera są pogrupowane w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , aby zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Co najmniej jedna maszyna wirtualna jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, które spełnia warunki umowy SLA na 99,95%.

**Magazyn Recovery Services**: [Magazyn Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowych i chroni wszystkie konfiguracje Virtual Machines platformy Azure w tej architekturze. W przypadku magazynu Recovery Services klienci mogą przywracać pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej, co umożliwia szybsze przywracanie.

### <a name="logging-and-audit"></a>Rejestrowanie i inspekcja
[Dzienniki Azure monitor](../azure-security-disk-encryption-overview.md) zapewniają obszerne rejestrowanie aktywności systemu i użytkownika, a także kondycję systemu. [Azure monitor rejestruje](https://azure.microsoft.com/services/log-analytics/) rozwiązanie zbiera i analizuje dane wygenerowane przez zasoby na platformie Azure i w środowiskach lokalnych.
- **Dzienniki aktywności**: [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/resource-logs-overview.md) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Te dzienniki obejmują Dzienniki systemu Windows i magazyn obiektów blob platformy Azure, tabele i dzienniki kolejki.
- **Dzienniki zapory**: Application Gateway zapewnia pełną dzienników diagnostycznych i dostępu. Dzienniki zapory są dostępne dla zasobów Application Gateway z włączoną obsługą usługi WAF.
- **Archiwizowanie dzienników**: Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage na potrzeby archiwizacji ze zdefiniowanym okresem przechowywania wynoszącym 2 dni. Te dzienniki łączą się z dziennikami Azure Monitor na potrzeby przetwarzania, przechowywania i raportowania pulpitów nawigacyjnych.

Ponadto w ramach tej architektury są dołączone następujące rozwiązania do monitorowania:
-   [AD Assessment](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie Active Directory Sprawdzanie kondycji ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md): Rozwiązanie chroniące przed złośliwym kodem zgłasza złośliwe oprogramowanie, zagrożenia i stan ochrony.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation rozwiązanie przechowuje elementy Runbook, uruchamia je i zarządza nimi.
-   [Security and Audit](../../security-center/security-center-intro.md): Pulpit nawigacyjny Security and Audit zawiera szczegółowe informacje o stanie zabezpieczeń zasobów dzięki zapewnieniu metryk w domenach zabezpieczeń, istotnych problemach, wykryciach, analizie zagrożeń i typowych zapytaniach zabezpieczeń.
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie Sprawdzanie kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu oraz zapewnia klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
-   [Update Management](../../automation/automation-update-management.md): Rozwiązanie Update Management umożliwia klientom zarządzanie aktualizacjami zabezpieczeń systemu operacyjnego, w tym stanem dostępnych aktualizacji i procesem instalowania wymaganych aktualizacji.
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, oraz liczby agentów przesyłających dane operacyjne.
-   [Dzienniki aktywności platformy Azure](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.
-   [Change Tracking](../../azure-monitor/platform/collect-activity-logs.md): Rozwiązanie Change Tracking pozwala klientom łatwo identyfikować zmiany w środowisku.

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają funkcje zarządzania tożsamościami w środowisku platformy Azure:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) może należeć do usługi zarządzania tożsamościami w chmurze z wieloma dzierżawami firmy Microsoft. Wszyscy użytkownicy rozwiązania zostały utworzeni w Azure Active Directory, w tym użytkownicy uzyskujący dostęp do SQL Database.
-   Uwierzytelnianie w aplikacji odbywa się przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ponadto szyfrowanie kolumn bazy danych używa usługi Azure AD do uwierzytelniania aplikacji do Azure SQL Database. Aby uzyskać więcej informacji, zobacz jak [chronić poufne dane w SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami organizacji i bada podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.
-   [Access Control oparte na rolach (RBAC) na platformie Azure](../../role-based-access-control/role-assignments-portal.md) umożliwia zarządzanie dostępem ukierunkowanym na platformę Azure. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.

Aby dowiedzieć się więcej o korzystaniu z funkcji zabezpieczeń Azure SQL Database, zobacz przykład [aplikacji demonstracyjnej firmy Contoso](https://github.com/Microsoft/azure-sql-security-sample) .

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze.

**Ochrona przed złośliwym oprogramowaniem**: [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware) Virtual Machines zapewnia ochronę w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie, z konfigurowalnymi alertami, gdy znane złośliwe lub niepożądane oprogramowanie próbuje Zainstaluj lub Uruchom na chronionych maszynach wirtualnych.

**Zarządzanie poprawkami**: Maszyny wirtualne z systemem Windows wdrożone w ramach tej architektury referencyjnej są domyślnie skonfigurowane tak, aby otrzymywać aktualizacje automatyczne z usługi Windows Update. To rozwiązanie obejmuje również usługę [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , za pomocą której można tworzyć zaktualizowane wdrożenia w celu zastosowania poprawek do maszyn wirtualnych w razie potrzeby.


## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="expressroute-and-vpn"></a>ExpressRoute i VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub bezpieczny tunel VPN musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej magazynu danych. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Przez odpowiednie skonfigurowanie usługi ExpressRoute lub sieci VPN klienci mogą dodać warstwę ochrony danych podczas przesyłania.

### <a name="extract-transform-load-etl-process"></a>Proces wyodrębniania-transformacji-Load (ETL)
[Baza](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) danych może ładować dane do Azure SQL Data Warehouse bez potrzeby oddzielnego narzędzia ETL lub importu. Baza danych wielobase umożliwia dostęp do nich za poorednictwem zapytań T-SQL. Stos analizy biznesowej i analizy firmy Microsoft oraz narzędzia innych firm zgodne z SQL Server mogą być używane z bazą danych.

### <a name="azure-active-directory-setup"></a>Konfiguracja Azure Active Directory
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) ma zasadnicze znaczenie dla zarządzania wdrożeniem i zapewnianiem dostępu do pracowników, którzy współpracują ze środowiskiem. Istniejące Active Directory systemu Windows Server można zintegrować z usługą AAD za pomocą [czterech kliknięć](../../active-directory/hybrid/how-to-connect-install-express.md). Klienci mogą również powiązać wdrożoną infrastrukturę Active Directory (kontrolery domeny) z istniejącą usługą AAD, wykonując wdrożoną infrastrukturę Active Directory poddomeną lasu usługi AAD.

### <a name="additional-services"></a>Usługi dodatkowe
Chociaż ta architektura magazynu danych nie jest przeznaczona do wdrożenia w środowisku [komercyjnym platformy Azure](https://azure.microsoft.com/overview/what-is-azure/) , podobne cele można osiągnąć za pomocą usług opisanych w tej architekturze referencyjnej, a także dodatkowych dostępnych usług tylko w środowisku komercyjnym platformy Azure. Należy pamiętać, że komercyjne usługi platformy Azure utrzymują FedRAMP JAB P-ATO na poziomie umiarkowanego wpływu, umożliwiając instytucjom rządowym i partnerom wdrażanie średnio poufnych informacji w chmurze wykorzystujących środowisko komercyjne platformy Azure.

Komercyjne środowisko platformy Azure oferuje szeroką gamę usług, które obsługują sformatowane i niesformatowane magazyny danych oraz przemieszczanie w celu użycia w magazynach danych, w tym:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) to zarządzana usługa w chmurze, która jest oparta na złożonych hybrydowych projektach typu wyodrębnianie-transformacja-ładowanie (ETL), wyodrębnianie-ładowanie-transformacja (ELT) i integracji danych. Korzystając z Azure Data Factory, klienci mogą tworzyć i planować przepływy pracy oparte na danych nazywane potokami, które pozyskiwanie danych z różnych magazynów danych. Klienci mogą następnie przetwarzać i przekształcać dane wyjściowe w magazyny danych, takie jak Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) umożliwia przechwytywanie danych dowolnego rozmiaru, typu i szybkości pozyskiwania w jednym miejscu na potrzeby analiz operacyjnych i poznawczych. Azure Data Lake Store jest zgodny z większością składników typu open source w ekosystemie usługi Hadoop i integruje dobrze z innymi usługami platformy Azure, takimi jak Azure SQL Data Warehouse.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych (DPD) dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/blueprintdwthreatmodel) lub można go znaleźć poniżej:

![Magazyn danych dla modelu zagrożeń FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "Magazyn danych dla modelu zagrożeń FedRAMP")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności
W [macierzy strategia zabezpieczeń i zgodności z przepisami platformy Azure — FedRAMP wysoka odpowiedzialność klienta](https://aka.ms/blueprinthighcrm) znajduje się lista wszystkich kontroli zabezpieczeń wymaganych przez FedRAMP High Baseline. Podobnie w przypadku [macierzy strategia zabezpieczeń i zgodności z przepisami platformy Azure — FedRAMP umiarkowanej odpowiedzialności klienta](https://aka.ms/blueprintcrmmod) wyświetlana jest lista wszystkich kontroli zabezpieczeń wymaganych przez podstawową linię bazową FedRAMP. Oba dokumenty zawierają szczegółowe informacje o tym, czy implementacja poszczególnych kontroli jest odpowiedzialna za firmę Microsoft, klienta, czy współdzielona między nimi.

Macierz [implementacji strategia zabezpieczeń i zgodności z przepisami platformy Azure FedRAMP o wysokiej kontroli](https://aka.ms/blueprintdwcimhigh) i [strategia zabezpieczeń i zgodności z przepisami platformy Azure-FedRAMP umiarkowanej implementacji kontroli](https://aka.ms/blueprintdwcimmod) zawierają informacje o tym, które kontrolki są objęte Architektura magazynu danych dla każdej linii bazowej FedRAMP, łącznie z szczegółowymi opisami, jak implementacja spełnia wymagania poszczególnych formantów objętych usługą.

## <a name="disclaimer"></a>Zastrzeżenie

 - Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
 - Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
 - Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
 - Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
 - Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
 - Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
