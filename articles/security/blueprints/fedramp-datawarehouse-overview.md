---
title: Zabezpieczenia platformy Azure i planu zgodność — FedRAMP automatyzacji w magazynie danych
description: Zabezpieczenia platformy Azure i planu zgodność — FedRAMP automatyzacji w magazynie danych
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 3c78aed2f30ea85f5bc16a8c0fb270bb1c761be8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586036"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Zabezpieczenia platformy Azure i zgodności planu: Magazyn danych w przypadku usługi Automation FedRAMP

## <a name="overview"></a>Omówienie

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) to program obowiązkowo USA, zapewniający ustandaryzowane podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania dla chmury produkty i usługi. Tych samych zabezpieczeń platformy Azure i zgodności plan zawiera wskazówki dotyczące dostarczania architektura hurtowni danych Microsoft Azure, ułatwiające implementuje podzbiór programowi FedRAMP High kontrolki. To rozwiązanie znajdują się wskazówki dotyczące wdrażania i konfiguracji zasobów platformy Azure dla typowych architektury referencyjnej, demonstrując sposób, w którym klienci mogą sprostać określonych wymagań dotyczących zabezpieczeń i zgodności i służy jako podstawa dla klientów Tworzenie i konfigurowanie własnych rozwiązań magazynu danych na platformie Azure.

Ta architektura referencyjna przewodniki po wdrażaniu skojarzonego formantu i modele zagrożeń są przeznaczone do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym. Wdrażanie aplikacji w tym środowisku, bez żadnych modyfikacji jest za mało, aby całkowicie wymagań linii bazowej programowi FedRAMP High. Pamiętaj o następujących kwestiach:
- Architektura zapewnia plan bazowy, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z FedRAMP.
- Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników

To rozwiązanie zapewnia architektury referencyjnej magazynu danych, która implementuje magazyn danych w chmurze o wysokiej wydajności i bezpieczne. Istnieją dwie warstwy danych w ramach tej architektury: jeden w którym dane są importowane, przechowywane i umieszczane w środowisku klastrowanym SQL, a druga dla usługi Azure SQL Data Warehouse w przypadku, gdy dane są ładowane za pomocą narzędzia ETL (np. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Zapytań T-SQL) do przetworzenia. Gdy dane są przechowywane w usłudze Azure SQL Data Warehouse, analizy można uruchomić na dużą skalę.

Platforma Microsoft Azure oferuje szereg usług raportowania i analizy dla klienta. To rozwiązanie oferuje program SQL Server Reporting Services (SSRS) do szybkiego tworzenia raportów z usługi Azure SQL Data Warehouse. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL przez dołączenie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem jest Azure zaleca się korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.

Dane w usłudze Azure SQL Data Warehouse są przechowywane w tabelach relacyjnych z magazynem kolumnowym formatu, który znacznie zmniejsza koszty przechowywania danych przy jednoczesnym zwiększeniu wydajności zapytań.  W zależności od wymagań użycia usługi Azure SQL Data Warehouse zasoby obliczeniowe można skalować w górę i w dół lub wyłączony całkowicie przypadku Brak aktywnych procesów wymagających zasoby obliczeniowe.

Moduł równoważenia obciążenia SQL zarządza ruchem SQL, zapewnienie wysokiej wydajności. Wszystkie maszyny wirtualne w tej architekturze referencyjnej w wdrożyć zestaw dostępności z wystąpienia programu SQL Server skonfigurowanych w zawsze włączonej grupy dostępności funkcji wysokiej dostępności i odzyskiwania po awarii.

Ta architektura referencyjna magazynu danych obejmuje również warstwą Active Directory (AD) do zarządzania zasobami w ramach architektury. Podsieć usługi Active Directory umożliwia łatwe przyjęcie w ramach większe struktury lasu usługi AD, co do pracy ciągłej, środowiska, nawet wtedy, gdy dostęp do większej lasu jest niedostępny. Wszystkie maszyny wirtualne są przyłączone do domeny do warstwy usługi Active Directory i wymuszanie konfiguracji zabezpieczeń i zgodności na poziomie systemu operacyjnego za pomocą zasad grupy usługi Active Directory.

Maszyna wirtualna służy jako zarządzania hostem bastionu, zapewnienia bezpiecznego połączenia dla administratorów, które uzyskują dostęp do wdrożonych zasobów. Załadowaniu danych do obszaru tymczasowego za pośrednictwem tego zarządzania hostem bastionu. **Azure zaleca się skonfigurowanie połączenia sieci VPN lub usługi Azure ExpressRoute do importowania danych i zarządzania do podsieci architektury odwołanie.**

![Data Warehouse, FedRAMP referencyjny diagram architektury](images/fedramp-datawarehouse-architecture.png?raw=true "magazynu danych na potrzeby FedRAMP referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Szczegółowe informacje na temat architektury wdrożenia [architektura wdrożenia](#deployment-architecture) sekcji.

Azure Virtual Machines
-   (1) Host bastionu
-   (2) kontrolera domeny active Directory
-   (2) węzeł klastra serwera SQL
-   (1) Monitor serwera SQL

Zestawy dostępności
-   (1) kontrolery domeny w usłudze active Directory
-   (1) węzły klastra SQL i Monitor

Virtual Network
-   (4) podsieci
-   (4) grupy zabezpieczeń sieci

SQL Data Warehouse

SQL Server Reporting Services

Moduł równoważenia obciążenia usługi Azure SQL

Usługa Azure Active Directory

Magazyn usługi Recovery Services

W usłudze Azure Key Vault

Dzienniki usługi Azure Monitor

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji przedstawiono elementy projektowania i implementacji.

**Usługa SQL Data Warehouse**: [Usługa SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jest Enterprise danych magazynu (EDW), który wykorzystuje wysoce równoległe przetwarzanie (MPP) do szybkiego uruchamiania złożonych zapytań obejmujących petabajty danych. Zaimportuj dane big data do usługi SQL Data Warehouse za pomocą prostych zapytań PolyBase T-SQL, a następnie użyj zaawansowanych funkcji architektury MPP do uruchomienia analizy o wysokiej wydajności.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) umożliwia szybkie tworzenie raportów przy użyciu tabele, wykresy, mapy, mierniki, macierzach i bardziej dla usługi Azure SQL Data Warehouse.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów poprzez zezwolenie wyłącznie na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Zezwalaj ruchu (RDP) pulpitu zdalnego, źródło ruchu musi być zdefiniowany w grupie zabezpieczeń sieci (NSG).

Maszyna wirtualna została utworzona jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Usługa Azure Monitor dzienników rozszerzenia](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault (stosuje się do platformy Azure Government, PCI DSS, HIPAA i innych wymagań)
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) włączone tak, aby poświadczeń i innych wpisów tajnych są uruchamiane w środowisku chronionego, która jest odizolowana od system operacyjny

### <a name="virtual-network"></a>Sieć wirtualna
Ta architektura referencyjna definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresów 10.0.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają list kontroli dostępu (ACL), które blokują lub zezwalają na ruch sieciowy w ramach sieci wirtualnej. Sieciowe grupy zabezpieczeń może służyć do zabezpieczenia ruchu na poziomie podsieci lub poszczególnych maszyn wirtualnych. Istnieją następujące sieciowe grupy zabezpieczeń:
  - Sieciowa grupa zabezpieczeń dla warstwy danych (klastry serwera SQL, SQL Server monitora i modułu równoważenia obciążenia programu SQL)
  - Sieciowa grupa zabezpieczeń do zarządzania hostem bastionu
  - Sieciowa grupa zabezpieczeń usługi Active Directory
  - Sieciowa grupa zabezpieczeń dla programu SQL Server Reporting Services

Sieciowe grupy zabezpieczeń mają określonych portów i protokołów, otwórz rozwiązanie może pracować bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej sieciowej grupy zabezpieczeń:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
  - Dzienniki platformy Azure Monitor jest podłączony do [diagnostyki sieciowej grupy zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: Każda podsieć jest skojarzone z jego odpowiedniego sieciowej grupy zabezpieczeń.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane za pomocą funkcji szyfrowania, inspekcja bazy danych i innych miar.

**Usługa Azure Storage** do spełnienia zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption).

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

### <a name="business-continuity"></a>Ciągłość działalności biznesowej
**Wysoka dostępność**: Obciążenia serwera są zgrupowane w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) do pomagają zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Co najmniej jednej maszyny wirtualnej są dostępne podczas planowanych lub nieplanowanych zdarzeń związanych z konserwacją spotkania 99,95% umowy SLA platformy Azure.

**Magazyn usług Recovery Services**: [Magazyn usługi Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. Przy użyciu magazynu usługi Recovery Services klienci mogą przywracać pliki i foldery z maszyny Wirtualnej IaaS bez przywracania całej maszyny Wirtualnej, umożliwiając skraca czas ich przywracania.

### <a name="logging-and-audit"></a>Rejestrowanie i inspekcja
[Dzienniki platformy Azure Monitor](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) zapewnia szczegółowe rejestrowanie aktywności systemu i użytkownika, a także kondycji systemu. [Dzienniki usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) rozwiązanie zbiera i analizuje dane generowane przez zasoby na platformie Azure i środowiskach lokalnych.
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows i magazynu Azure Blob, tabel i Dzienniki kolejek.
- **Dzienniki zapory**: Application Gateway zapewnia pełną diagnostycznych i dostęp do dzienników. Dzienniki zapory są dostępne dla zasobów z obsługą zapory aplikacji sieci Web bramy aplikacji.
- **Archiwizowanie dziennika**: Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji okres przechowywania zdefiniowanych 2 dni. Te dzienniki nawiązać dzienniki usługi Azure Monitor do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym.

Ponadto następujące rozwiązania do monitorowania, są uwzględnione w ramach tej architektury:
-   [Ocena usługi AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Ocena ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Rozwiązanie chroniące przed złośliwym kodem raporty dotyczące złośliwego oprogramowania, zagrożeń i ochronę stanu.
-   [Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Rozwiązania usługi Azure Automation przechowuje, uruchamia i zarządza elementami runbook.
-   [Zabezpieczenia i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Pulpit nawigacyjny zabezpieczenia i inspekcja zawiera ogólne informacje o stanie zabezpieczeń zasobów, zapewniając metryki w przypadku domen zabezpieczeń, problemy godne uwagi, wykrywania, analizy zagrożeń i typowe zapytania dotyczące zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Rozwiązanie Update Management umożliwia zarządzanie klientami z aktualizacjami zabezpieczeń systemu operacyjnego, w tym stan dostępnych aktualizacji i procesu instalacji wymaganych aktualizacji.
-   [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.
-   [Śledzenie zmian](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Change Tracking umożliwia klientom łatwo identyfikować zmiany w środowisku.

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają tożsamość możliwości zarządzania w środowisku platformy Azure:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) może być usługa firmy Microsoft wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami zarządzania. Wszyscy użytkownicy dotyczące rozwiązania zostały utworzone w usługi Azure Active Directory, w tym użytkowników uzyskujących dostęp do bazy danych SQL.
-   Do aplikacji uwierzytelniania przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi Azure AD można uwierzytelnić aplikację do usługi Azure SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki mające wpływ na tożsamości w organizacji, umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości organizacji i wsparcia podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.
-   [Usługa Azure opartej na rolach kontrola dostępu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia skupia się zarządzanie dostępem dla platformy Azure. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.

Aby dowiedzieć się więcej o korzystaniu z funkcji zabezpieczeń usługi Azure SQL Database, zobacz [aplikacji pokazowej Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) próbki.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze.

**Ochrona przed szkodliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania, można skonfigurować alerty, gdy znany złośliwego lub niechcianego oprogramowania próbuje zainstalowania lub uruchomienia na chronionych maszynach wirtualnych.

**Zarządzanie poprawkami**: Windows wdrożone maszyny wirtualne w ramach tej architektury referencyjnej są domyślnie skonfigurowane, automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To rozwiązanie obejmuje również [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których zaktualizowano wdrożenia mogą być tworzone do poprawki maszyny wirtualne w razie.


## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="expressroute-and-vpn"></a>Usługi ExpressRoute i sieci VPN
[Usługa ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub bezpieczny tunel sieci VPN musi zostać skonfigurowany nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej magazynu danych. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Konfigurując odpowiednio ExpressRoute lub sieci VPN, klientów można dodać warstwę ochrony danych podczas przesyłania.

### <a name="extract-transform-load-etl-process"></a>Proces wyodrębniania, przekształcania, ładowania (ETL)
[Program PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) można załadować dane do usługi Azure SQL Data Warehouse bez konieczności stosowania osobnych ETL lub zaimportować narzędzia. Program PolyBase umożliwia dostęp do danych za pomocą zapytań T-SQL. Przy użyciu technologii PolyBase może służyć przez firmę Microsoft analizy biznesowej i stos analizy, a także narzędzia innych producentów są zgodne z programem SQL Server.

### <a name="azure-active-directory-setup"></a>Konfiguracja usługi Azure Active Directory
[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest Zarządzanie wdrożeniem i inicjowania obsługi dostępu do personelu wchodzenie w interakcje ze środowiskiem. Istniejącego systemu Windows Server Active Directory można zintegrować z usługą AAD w [cztery kliknięcia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Klienci również powiązać wdrożonej infrastrukturę usługi Active Directory (kontrolery domeny) do istniejącej usługi AAD, wprowadzając wdrożonej infrastrukturę usługi Active Directory poddomeny las usługi AAD.

### <a name="additional-services"></a>Usługi dodatkowe
Mimo że ta Architektura magazynu danych nie jest przeznaczony do wdrożenia [wersji Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) środowiska, podobnie cele można osiągnąć za pomocą usług opisanych w tej architekturze referencyjnej także dodatkowe usługi tylko w środowiskach komercyjnych platformy Azure. Należy pamiętać, że wersji Azure Commercial utrzymuje FedRAMP JAB P-ATO na poziomie umiarkowany wpływ, dzięki niemu agencje rządowe i partnerami w celu wdrażania umiarkowanie poufnych informacji w chmurze, wykorzystując wersji Azure Commercial środowiska.

Platforma Azure oferuje komercyjnych szerokiej gamy usługi uchwyt sformatowany i niesformatowanych danych magazynu i przemieszczania, które zostaną użyte w magazynowaniu danych, w tym:
-   [Usługa Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) to usługa zarządzana usługa w chmurze, która została skonstruowana na potrzeby kompleksowych, hybrydowych extract-transform-load (ETL), wyodrębnianie ładowanie transformacja (ELT) i danych projekty integracji. Za pomocą usługi Azure Data Factory, klientów można tworzyć i planować opartych na danych przepływy pracy o nazwie potoki, które pozyskiwać dane z różnych magazynów danych. Klienci, a następnie jest przetwarzanie i przekształcanie danych dla danych wyjściowych w magazynach danych, takich jak Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) umożliwia przechwytywanie danych dowolnego rozmiaru, typu i szybkości wprowadzania w jednym miejscu na potrzeby analizy operacyjnej i rozpoznawczej. Azure Data Lake Store jest zgodna z najbardziej otwarta składnikami źródła w ekosystemie usługi Hadoop i bardzo dobrze integruje się z innymi usługami Azure, takich jak Azure SQL Data Warehouse.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych (DPD) dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/blueprintdwthreatmodel) lub znajdują się poniżej:

![Data Warehouse, model zagrożeń FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "magazynu danych na potrzeby FedRAMP model zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Zabezpieczeń platformy Azure i plan zgodność — FedRAMP wysokiej klienta odpowiedzialność macierzy](https://aka.ms/blueprinthighcrm) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez programowi FedRAMP High linii bazowej. Podobnie [zabezpieczeń platformy Azure i plan zgodność — FedRAMP umiarkowane klienta odpowiedzialność macierzy](https://aka.ms/blueprintcrmmod) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez plan bazowy średni FedRAMP. Zarówno dokumentów szczegółów czy wykonania każdego formantu jest odpowiedzialność firmy Microsoft, klient lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i plan zgodność — FedRAMP wysokiej macierzy implementacji kontroli](https://aka.ms/blueprintdwcimhigh) i [zabezpieczeń platformy Azure i plan zgodność — FedRAMP macierzy kontroli umiarkowane w implementacji](https://aka.ms/blueprintdwcimmod) zapewniają informacje, na którym formanty są objęte Architektura magazynu danych dla każdej podstawy FedRAMP, w tym szczegółowy opis sposobu wdrożenia spełnia wymagania każdego formantu objętego usługą.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
 - W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
 - Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
 - Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
