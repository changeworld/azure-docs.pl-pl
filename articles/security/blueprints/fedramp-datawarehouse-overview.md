---
title: Zabezpieczeń platformy Azure i plan zgodności - magazynu danych na potrzeby FedRAMP automatyzacji
description: Zabezpieczeń platformy Azure i plan zgodności - magazynu danych na potrzeby FedRAMP automatyzacji
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 110ce131286f437e051dd859eb4d2baa29f106f6
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Zabezpieczeń platformy Azure i plan zgodności: Magazyn danych FedRAMP automatyzacji

## <a name="overview"></a>Przegląd

[Federalne ryzyka i Program do zarządzania autoryzacji (FedRAMP)](https://www.fedramp.gov/) to program całej dla instytucji rządowych Stanów Zjednoczonych, który zawiera standardowe podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania dla chmury produkty i usługi. Ten Azure zabezpieczeń i zgodności plan zawiera wskazówki dotyczące jak dostarczać architektura hurtowni danych Microsoft Azure, ułatwiająca implementuje podzbiór wysokiej FedRAMP formantów. To rozwiązanie zawiera wskazówki dotyczące wdrażania i konfigurowania zasobów Azure dla typowych architektura referencyjna prezentacja sposoby, w którym klienci mogą spełniać określone wymagania zabezpieczeń i zgodności i podstawą dla klientów Tworzenie i konfigurowanie własnych rozwiązań magazynu danych na platformie Azure.

To architektura referencyjna, przewodniki implementacji skojarzonym formancie i modele zagrożeń mają służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym. Wdrażanie aplikacji w tym środowisku bez żadnych modyfikacji jest niewystarczający w celu całkowicie spełniają wymagania wysokiej FedRAMP linii bazowej. Pamiętaj o następujących kwestiach:
- Architektura zapewnia linii bazowej, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z FedRAMP.
- Klienci są zobowiązani do przeprowadzania odpowiednich ustawień zabezpieczeń i zgodności oceny dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat wdrażania poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników

To rozwiązanie zapewnia architektura odwołanie hurtowni danych, która implementuje chmury wysokiej wydajności i bezpiecznego magazynu danych. Istnieją dwie warstwy danych w ramach tej architektury: co gdzie dane są importowane, przechowywane i umieszczane w środowisku klastrowanym SQL, a druga dla usługi Azure SQL Data Warehouse w przypadku, gdy dane są ładowane przy użyciu narzędzia ETL (np. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Zapytania T-SQL) do przetwarzania. Gdy dane są przechowywane w usłudze Azure SQL Data Warehouse, analytics można uruchomić w bardzo dużej skali.

Microsoft Azure oferuje wiele usług raportowania i analiz dla klienta. To rozwiązanie obejmuje usługi SQL Server Reporting Services (SSRS) do szybkiego tworzenia raportów z magazynu danych SQL Azure. Cały ruch SQL są szyfrowane przy użyciu protokołu SSL przez dodanie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem Azure zalecane jest stosowanie zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.

Dane w magazynie danych SQL Azure są przechowywane w tabel relacyjnych z magazynem kolumnowy, format, który znacznie zmniejsza koszty przechowywania danych podczas poprawę wydajności zapytań.  W zależności od wymagań użycia zasobów obliczeniowych Azure SQL Data Warehouse można skalować w górę lub w dół lub wyłączone całkowicie Jeśli brak aktywnych procesów wymagających zasobów obliczeniowych.

Moduł równoważenia obciążenia SQL zarządza SQL ruchu, zapewniających wysoką wydajność. Wszystkie maszyny wirtualne w tej architekturze referencyjnej wdrożyć w zestawie wystąpieniami programu SQL Server skonfigurowanych w zawsze włączonej grupy dostępności funkcji wysokiej dostępności i odzyskiwania po awarii dostępności.

Informacje o architekturze wzorcowej magazynu danych obejmuje również warstwą Active Directory (AD) do zarządzania zasobami w architekturze. Podsieć usługi Active Directory umożliwia łatwy przyjęcie w ramach większej struktury lasu usługi AD, co pozwala na działanie ciągłe środowiska, nawet wtedy, gdy jest niedostępny do większych lasu. Wszystkie maszyny wirtualne są przyłączone do domeny do warstwy usługi Active Directory i wymuszanie konfiguracji zgodności i zabezpieczeń na poziomie systemu operacyjnego za pomocą zasad grupy usługi Active Directory.

Maszyna wirtualna służy jako zarządzania hosta bastionu, zapewnienia bezpiecznego połączenia dla administratorów, aby wdrożyć dostęp do zasobów. Ładuje dane do obszaru przemieszczania za pomocą tego hosta bastionu zarządzania. **Azure zaleca się Konfigurowanie połączenia sieci VPN lub rozwiązania Azure ExpressRoute do importowania danych i zarządzania w podsieci architektura odwołania.**

![Magazynu danych na potrzeby diagram architektury odwołanie FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "magazynu danych na potrzeby diagram architektury FedRAMP odwołania")

To rozwiązanie wymaga następujących usług platformy Azure. Szczegółowe informacje o architekturze wdrożenia są w [wdrożenia architecture](#deployment-architecture) sekcji.

Azure Virtual Machines
-   (1) Host bastionu
-   (2) active Directory kontrolera domeny
-   (2) węzeł klastra programu SQL Server
-   (1) Monitor serwera SQL

Zestawy dostępności
-   (1) kontrolery domeny active Directory
-   (1) węzły klastra SQL i Monitor

Virtual Network
-   (4) podsieci
-   (4) grupy zabezpieczeń sieci

SQL Data Warehouse

SQL Server Reporting Services

Moduł równoważenia obciążenia Azure SQL

Usługa Azure Active Directory

Magazyn usług odzyskiwania

W usłudze Azure Key Vault

Operations Management Suite (OMS)

## <a name="deployment-architecture"></a>Architektura wdrażania

Poniższa sekcja zawiera szczegóły dotyczące projektowania i wdrażania elementów.

**Usługa SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jest Enterprise danych magazynu (EDW) wykorzystującego Massively Parallel przetwarzania (MPP) do szybkiego uruchamiania złożonych zapytań między petabajtów danych. Zaimportuj dane big data do magazynu danych SQL za pomocą prostego zapytania PolyBase T-SQL i użyj zasilania MPP, aby uruchomić analytics wysokiej wydajności.

**SQL Server Reporting Services**: [programu SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) umożliwia szybkie tworzenie raportów z tabel, wykresy, mapy, mierników, macierze i więcej przez Magazyn danych SQL Azure.

**Host bastionu**: host bastionu jest pojedynczy punkt wejścia, który umożliwia użytkownikom dostęp do wdrożonych zasobów, w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów, zezwalając tylko ruchu zdalnego z publicznych adresów IP na liście bezpiecznych. Aby zezwolić na zdalny pulpitu ruch (RDP), źródło ruchu musi być zdefiniowana w grupy zabezpieczeń sieci (NSG).

Maszyna wirtualna została utworzona jako hosta domeny bastionu z następujących konfiguracji:
-   [Rozszerzenie ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozszerzenie pakietu OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozszerzenie Diagnostyka Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Szyfrowanie dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault (szanuje Azure dla instytucji rządowych, PCI DSS, HIPAA i inne wymagania)
-   [Zasad automatyczne zamykanie](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane
-   [Windows Defender poświadczeń Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) włączane poświadczeń i innych informacji poufnych uruchamiane w chronionym środowisku, która jest odizolowana od systemem operacyjnym

### <a name="virtual-network"></a>Sieć wirtualna
Informacje o architekturze wzorcowej definiuje prywatnej sieci wirtualnej z 10.0.0.0/16 przestrzeni adresowej.

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
Architektura chroni dane przechowywane przez szyfrowanie, bazy danych inspekcji i stosowania innych środków.

**Magazyn Azure** do spełnienia zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Magazyn](https://docs.microsoft.com/azure/storage/storage-service-encryption).

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

### <a name="business-continuity"></a>Ciągłość działalności biznesowej
**Wysoka dostępność**: obciążenia serwera są pogrupowane w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pomaga zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Co najmniej jednej maszyny wirtualnej jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, spotkania 99,95% umowy SLA platformy Azure.

**Magazyn usług odzyskiwania**: [magazyn usług odzyskiwania](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. W magazynie usług odzyskiwania klientów można przywrócić pliki i foldery z maszyn wirtualnych IaaS bez przywracania całą maszynę Wirtualną, umożliwiające szybsze przywracania.

### <a name="logging-and-audit"></a>Rejestrowanie i inspekcja
[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) zapewnia szczegółowe rejestrowanie działania systemu i użytkownika, a także kondycji systemu. OMS [analizy dzienników](https://azure.microsoft.com/services/log-analytics/) rozwiązanie zbiera i analizuje dane generowane przez zasobami na platformie Azure i lokalnego środowiska.
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacji wykonywanych na zasobów w ramach subskrypcji.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdego zasobu. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows i magazynu obiektów Blob platformy Azure, tabele i kolejki dzienników.
- **Zapory Dzienniki**: bramy aplikacji udostępnia pełnej diagnostyki i dostępu do dzienników. Dzienniki zapory są dostępne dla zasobów włączone zapory aplikacji sieci Web Application Gateway.
- **Zaloguj się Archiwizacja**: wszystkie dzienniki diagnostyczne zapisu na konto magazynu Azure scentralizowane i zaszyfrowanego dla archiwizacji okres przechowywania określonych 2 dni. Te dzienniki nawiązać Analiza dzienników Azure do przetwarzania, przechowywania i raportowania pulpitu nawigacyjnego.

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

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają tożsamość możliwości zarządzania w środowisku platformy Azure:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) może być wielodostępne oparte na chmurze katalogami i tożsamościami zarządzania usługi Microsoft. Wszyscy użytkownicy dla rozwiązania zostały utworzone w usłudze Azure Active Directory, w tym użytkownikom dostęp do bazy danych SQL.
-   Aby aplikacja uwierzytelniania przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi Azure AD do uwierzytelniania aplikacji z bazą danych SQL Azure. Aby uzyskać więcej informacji, zobacz temat jak [chronią poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki tożsamości organizacji, konfiguruje automatyczne odpowiedzi wykryte podejrzane działania związane z tożsamości organizacji i sprawdza podejrzane zdarzenia podjęcie odpowiednich działań, aby je rozwiązać.
-   [Azure opartej na rolach kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia fokus zarządzania dostępem dla platformy Azure. Subskrypcja dostęp jest ograniczony do administratora subskrypcji.

Aby dowiedzieć się więcej o korzystaniu z funkcji zabezpieczeń bazy danych SQL Azure, zobacz [aplikacja demonstracyjna Clinic Contoso](https://github.com/Microsoft/azure-sql-security-sample) próbki.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie kluczy tajnych**: w tym rozwiązaniu zastosowano [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) zarządzania kluczy i kluczy tajnych. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze.

**Ochrona przed złośliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, że pomaga zidentyfikować i usunąć wirusy, programy szpiegujące lub inne złośliwe oprogramowanie, można skonfigurować alerty Jeśli znane złośliwego lub niechcianego oprogramowania próbuje zainstalować lub uruchomić na chronionych maszyn wirtualnych.

**Zarządzanie poprawkami**: maszyny wirtualne systemu Windows wdrożone w ramach tej architektury odwołania są domyślnie skonfigurowane, odbierania automatycznych aktualizacji z usługi Windows Update. To rozwiązanie obejmuje również OMS [usługi Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których można utworzyć wdrożenia zaktualizowane maszynom wirtualnym poprawki w razie potrzeby.


## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="expressroute-and-vpn"></a>ExpressRoute i sieci VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub bezpieczny tunel sieci VPN musi być skonfigurowana do bezpiecznego nawiązać połączenie z zasoby wdrożone w ramach tej architektury odwołanie do magazynu danych. Jako połączeń ExpressRoute nie przechodzą w Internecie, te połączenia oferują więcej niezawodności, szybkości szybsze niższe opóźnienia i lepsze zabezpieczenia niż typowe połączenia za pośrednictwem Internetu. Konfigurując odpowiednie ExpressRoute i sieci VPN, klienci można dodać warstwę ochrony danych podczas przesyłania.

### <a name="extract-transform-load-etl-process"></a>Proces wyodrębniania przekształcenia ładowania (ETL)
[Program PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) można załadować danych do usługi Azure SQL Data Warehouse bez konieczności oddzielnego ETL lub zaimportować narzędzia. Aparat PolyBase zezwala na dostęp do danych za pomocą zapytania T-SQL. Przy użyciu programu PolyBase może służyć firmy Microsoft analizy biznesowej i stosu analizy, a także narzędzia innych firm zgodne z programem SQL Server.

### <a name="azure-active-directory-setup"></a>Instalacja usługi Azure Active Directory
[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest niezbędne do zarządzania wdrażania i inicjowania obsługi dostępu do personelu interakcji ze środowiskiem. Istniejący Windows Server Active Directory można zintegrować z usługi AAD w [cztery kliknięć](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Klientów można również powiązanie wdrożonej infrastruktura usługi Active Directory (kontrolery domeny) do istniejącej usługi AAD dokonując wdrożonej infrastruktura usługi Active Directory jest poddomeną las usługi AAD.

### <a name="additional-services"></a>Usługi dodatkowe
Mimo że taka Architektura magazynu danych nie jest przeznaczony do wdrożenia na [Azure komercyjnych](https://azure.microsoft.com/overview/what-is-azure/) środowiska, podobne cele można osiągnąć za pomocą usługi opisane w tej architekturze referencyjnej oraz dodatkowe usługi dostępne tylko w środowiskach komercyjnych Azure. Należy pamiętać, komercyjnych Azure obsługuje FedRAMP JAB P-ATO na umiarkowany poziom wpływu, dzięki czemu agencji rządowych i partnerami, aby wdrażać w chmurze, wykorzystując środowiska Azure handlowego średnio poufne informacje.

Azure oferuje komercyjnych szerokiej gamy usługi dojścia sformatowany i niesformatowanych danych magazynu i przemieszczania do użycia w magazynowanie danych, w tym:
-   [Fabryka danych Azure](https://docs.microsoft.com/azure/data-factory/introduction) to usługa w chmurze zarządzanych przeznaczonego do złożonych hybrydowego extract — przekształcenia ładowania (ETL) projektów integracji wyodrębniania obciążenia transformacji (ELT) i danych. Przy użyciu fabryki danych Azure, klientów można tworzyć i zaplanować opartych na danych o nazwie potoki, które pozyskiwania danych z baz danych różnych przepływów pracy. Klientów można następnie przetwarzania i przekształcania danych wyjściowych do magazynów danych, takie jak magazyn danych SQL Azure.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) umożliwia przechwytywanie danych dowolnego rozmiar, typ i wprowadzanie szybkości w jednym miejscu na potrzeby analiz operacyjnych i poznawczych. Azure Data Lake Store jest zgodna z najbardziej otwartych składników źródła w ekosystemie Hadoop i bardzo dobrze integruje się z innymi usługami Azure, takich jak magazyn danych SQL Azure.

## <a name="threat-model"></a>Modelu zagrożeń

Diagram przepływu danych (DPD) dla tej architektury dokumentacja jest dostępna dla [Pobierz](https://aka.ms/blueprintdwthreatmodel) lub znajduje się poniżej:

![Magazyn danych dla modelu zagrożeń FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "magazynu danych dla modelu zagrożeń FedRAMP")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Azure zabezpieczeń i zgodności planu — FedRAMP wysokiej klienta odpowiedzialność macierzy](https://aka.ms/blueprinthighcrm) zawiera wszystkie opcje zabezpieczeń wymagane przez wysokiej FedRAMP linii bazowej. Podobnie [Azure zabezpieczeń i zgodności planu — umiarkowane macierzy odpowiedzialność klienta FedRAMP](https://aka.ms/blueprintcrmmod) zawiera wszystkie opcje zabezpieczeń wymagane przez umiarkowany FedRAMP linii bazowej. Zarówno dokumentów można ustalić, czy implementacja każdej kontrolki jest odpowiedzialność firmy Microsoft, odbiorcy, lub udostępniane między nimi.

[Zabezpieczeń platformy Azure i plan zgodności - FedRAMP wysokiej macierzy implementacji kontroli](https://aka.ms/blueprintdwcimhigh) i [Azure zabezpieczeń i zgodności planu — umiarkowane macierzy implementacji kontroli FedRAMP](https://aka.ms/blueprintdwcimmod) Podaj informacje, na którym formanty są objęte architektura hurtowni danych dla każdej linii bazowej FedRAMP, w tym szczegółowy opis sposobu implementacji spełnia wymagania dotyczące każdego objęte kontroli.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.
 - Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produktem firmy Microsoft lub rozwiązania.
 - Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym.
 - Ten dokument został utworzony jako odwołanie i nie należy używać do definiowania oznacza, że wszystkie za pomocą których klient może spełniać określone wymagania i przepisami. Klienci powinien poszukuje prawne z organizacji w implementacjach klienta zatwierdzone.
