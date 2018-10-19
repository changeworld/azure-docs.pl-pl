---
title: Zabezpieczenia platformy Azure i zgodności planu — magazynu danych w ramach RODO
description: Zabezpieczenia platformy Azure i zgodności planu — magazynu danych w ramach RODO
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 84e26212b1102d693b84e5b66fbd606da2673934
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405943"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Zabezpieczenia platformy Azure i zgodności planu: Magazyn danych dla rozporządzenia RODO

## <a name="overview"></a>Przegląd
Ogólne rozporządzenie o ochronie danych (RODO) zawiera wiele wymagań dotyczących zbierania, przechowywania i przy użyciu informacji osobistych, w tym jak organizacjom identyfikowanie i zabezpieczanie danych osobowych, uwzględnić wymagania dotyczące przezroczystości, wykrywania i raportu naruszenia danych osobowych i szkolenie zachowania personel i innych pracowników. RODO zapewnia większą kontrolę nad ich danych osobowych użytkowników indywidualnych i nakłada wiele nowych zobowiązań w organizacji, które zbierania, obsługiwać lub analizować dane osobowe. Rozporządzenie GDPR nakłada nowe zasady dotyczące organizacji oferujących towary i usługi osobom w Unii Europejskiej (UE) lub że zbierać i analizować dane związane z osobami mieszkającymi w UE. Niezależnie od tego, w którym znajduje się organizacja stosuje się GDPR.

Firma Microsoft zaprojektowała platformy Azure za pomocą wiodących w branży środki bezpieczeństwa i zasady zachowania poufności informacji w celu ochrony danych w chmurze, w tym kategorie danych osobowych identyfikowane przez rozporządzenie GDPR. Firmy Microsoft [warunków umowy](http://aka.ms/Online-Services-Terms) zatwierdzić Microsoft wymagania procesorów.

Tych samych zabezpieczeń platformy Azure i zgodności plan zawiera wskazówki dotyczące wdrażania architektura hurtowni danych, na platformie Azure, która ułatwia spełnianie wymagań rozporządzenia rodo. To rozwiązanie przedstawia sposób, w którym klienci mogą sprostać określonych wymagań dotyczących zabezpieczeń i zgodności i służy jako podstawa do tworzenia i konfigurowanie własnych rozwiązań magazynu danych na platformie Azure. Klienci mogą korzystać z tej architektury referencyjnej i postępuj zgodnie z firmy Microsoft [czterech kroków procesu](https://aka.ms/gdprebook) w podróży do zgodności z rozporządzeniem GDPR:
1. Odnajdywanie: Zidentyfikować dane osobowe, które istnieje, oraz jej lokalizację.
2. Zarządzanie: Określają jak osobistych danych jest używane i dostępne.
3. Ochrona: Ustanowić kontrole bezpieczeństwa, aby zapobiec, wykrywanie ich i odpowiadanie na luki w zabezpieczeniach i naruszeń danych.
4. Raport: Przechowywania dokumentacji wymagane i zarządzanie żądaniami danych i naruszenia powiadomienia.

Ta architektura referencyjna skojarzonego wdrożenia przewodnik i model zagrożeń są przeznaczone do służyć jako podstawa dla klientów dostosować je do ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym. Pamiętaj o następujących kwestiach:
- Architektura zapewnia plan bazowy, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z RODO.
- Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
To rozwiązanie zapewnia architekturę referencyjną, która implementuje magazyn danych w chmurze o wysokiej wydajności i bezpieczne. Istnieją dwie warstwy danych w ramach tej architektury: jeden w którym dane są importowane, przechowywane i umieszczane w środowisku klastrowanym SQL, a druga dla usługi Azure SQL Data Warehouse w przypadku, gdy dane są ładowane za pomocą narzędzia ETL (np. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Zapytań T-SQL) do przetworzenia. Gdy dane są przechowywane w usłudze Azure SQL Data Warehouse, analizy można uruchomić na dużą skalę.

Platforma Azure oferuje szereg usług raportowania i analizy dla klienta. To rozwiązanie oferuje program SQL Server Reporting Services (SSRS) do szybkiego tworzenia raportów z usługi Azure SQL Data Warehouse. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL przez dołączenie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem jest Azure zaleca się korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.

Dane w usłudze Azure SQL Data Warehouse są przechowywane w tabelach relacyjnych z magazynem kolumnowym formatu, który znacznie zmniejsza koszty przechowywania danych przy jednoczesnym zwiększeniu wydajności zapytań.  W zależności od wymagań użycia usługi Azure SQL Data Warehouse zasoby obliczeniowe można skalować w górę i w dół lub wyłączony całkowicie przypadku Brak aktywnych procesów wymagających zasoby obliczeniowe.

Moduł równoważenia obciążenia SQL zarządza ruch SQL zapewniające wysoką wydajność. Wszystkie maszyny wirtualne w tej architekturze referencyjnej w wdrożyć zestaw dostępności z wystąpienia programu SQL Server skonfigurowanych w zawsze włączonej grupy dostępności funkcji wysokiej dostępności i odzyskiwania po awarii.

Ta architektura referencyjna magazynu danych obejmuje również warstwą Active Directory (AD) do zarządzania zasobami w ramach architektury. Podsieć usługi Active Directory umożliwia łatwe przyjęcie w ramach większe struktury lasu usługi AD, co do pracy ciągłej, środowiska, nawet wtedy, gdy dostęp do większej lasu jest niedostępny. Wszystkie maszyny wirtualne są przyłączone do domeny do warstwy usługi Active Directory i wymuszanie konfiguracji zabezpieczeń i zgodności na poziomie systemu operacyjnego za pomocą zasad grupy usługi Active Directory.

Maszyna wirtualna służy jako zarządzania hostem bastionu, zapewnienia bezpiecznego połączenia dla administratorów, które uzyskują dostęp do wdrożonych zasobów. Załadowaniu danych do obszaru tymczasowego za pośrednictwem tego zarządzania hostem bastionu. **Azure zaleca się skonfigurowanie połączenia sieci VPN lub usługi Azure ExpressRoute do importowania danych i zarządzania do podsieci architektury odwołanie.**

![Magazyn danych dla rozporządzenia RODO referencyjny diagram architektury](images/gdpr-datawarehouse-architecture.png?raw=true "magazynu danych na potrzeby RODO referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Szczegółowe informacje na temat architektury wdrożenia [architektura wdrożenia](#deployment-architecture) sekcji.

-   Azure Virtual Machines
    - (1) Host bastionu
    -   (2) kontrolera domeny active Directory
    -   (2) węzeł klastra serwera SQL
    -   (1) Monitor serwera SQL
-   Zestawy dostępności
    - Kontrolery domeny usługi Active Directory
    - Węzły klastra SQL i monitora
-   Virtual Network
    - (4) podsieci
    -   (4) grupy zabezpieczeń sieci
- SQL Data Warehouse
- SQL Server Reporting Services
- Moduł równoważenia obciążenia usługi Azure SQL
- Usługa Azure Active Directory
- Magazyn usługi Recovery Services
- W usłudze Azure Key Vault
- Log Analytics
- Azure Data Catalog
- Azure Security Center

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Usługa SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jest Enterprise danych magazynu (EDW), który wykorzystuje wysoce równoległe przetwarzanie (MPP) do szybkiego uruchamiania złożonych zapytań obejmujących petabajty danych, pozwalając użytkownikom na efektywne zidentyfikować dane osobowe. Użytkownicy mogą używać prostych zapytań PolyBase T-SQL, aby zaimportować dane big data do usługi SQL Data Warehouse i korzystanie z zaawansowanych funkcji architektury MPP do uruchomienia analizy o wysokiej wydajności.

**SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) umożliwia szybkie tworzenie raportów przy użyciu tabele, wykresy, mapy, mierniki, macierzach i bardziej dla usługi Azure SQL Data Warehouse.

**Wykaz danych**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) sprawia, że źródła danych łatwe do zrozumienia i prostsze do odnalezienia przez użytkowników, którzy zarządzają danymi. Wspólnych źródeł danych można zarejestrować, oznaczone i wyszukiwane dane osobowe. Dane pozostają w istniejącej lokalizacji, ale kopia ich metadanych jest dodawany do usługi Data Catalog wraz z odwołaniem do lokalizacji źródła danych. Te metadane są również indeksowane, aby można było je łatwo odnaleźć za pomocą wyszukiwania oraz uczynić je zrozumiałymi dla użytkowników, którzy je odnajdą.

**Host bastionu**: hostem bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów poprzez zezwolenie wyłącznie na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Zezwalaj ruchu (RDP) pulpitu zdalnego, źródło ruchu musi być zdefiniowany w grupie zabezpieczeń sieci (NSG).

To rozwiązanie tworzy maszynę wirtualną jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozszerzenie analizy dzienników](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) włączone tak, aby poświadczeń i innych wpisów tajnych są uruchamiane w środowisku chronionego, która jest odizolowana od system operacyjny

### <a name="virtual-network"></a>Sieć wirtualna
Ta architektura referencyjna definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresów 10.0.0.0/16.

**Sieciowe grupy zabezpieczeń**: [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają list kontroli dostępu (ACL), które blokują lub zezwalają na ruch sieciowy w ramach sieci wirtualnej. Sieciowe grupy zabezpieczeń może służyć do zabezpieczenia ruchu na poziomie podsieci lub poszczególnych maszyn wirtualnych. Istnieją następujące sieciowe grupy zabezpieczeń:
  - Sieciowa grupa zabezpieczeń dla warstwy danych (klastry serwera SQL, SQL Server monitora i modułu równoważenia obciążenia programu SQL)
  - Sieciowa grupa zabezpieczeń do zarządzania hostem bastionu
  - Sieciowa grupa zabezpieczeń usługi Active Directory
  - Sieciowa grupa zabezpieczeń dla programu SQL Server Reporting Services

Sieciowe grupy zabezpieczeń mają określonych portów i protokołów, otwórz rozwiązanie może pracować bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej sieciowej grupy zabezpieczeń:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
  - Usługa log Analytics jest połączony z [diagnostyki sieciowej grupy zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: każda podsieć jest skojarzona z jego odpowiedniego sieciowej grupy zabezpieczeń.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane za pomocą wielu środków, takimi jak szyfrowanie i inspekcja bazy danych.

**Usługa Azure Storage**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i ochrony danych osobowych w odniesieniu do bezpieczeństwa organizacji i zdefiniowane przez rozporządzenie GDPR wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**: [usługi Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. To rozwiązanie integruje się z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Usługa Azure SQL Database**: wystąpienie usługi Azure SQL Database używa następujące środki bezpieczeństwa bazy danych:
-   [Usługi AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
-   Baza danych SQL jest skonfigurowana do używania [przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i rest usługi pliki dziennika transakcji, aby chronić informacje o. Funkcja TDE zapewnia pewność, że przechowywane dane osobowe nie została poddana przed nieautoryzowanym dostępem.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i dostęp do bazy danych nietypowe wzorce.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że wrażliwe dane osobowe nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych, tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą dostęp do danych w postaci zwykłego tekstu.
- [Właściwości rozszerzone](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funkcja może służyć do zaprzestać przetwarzania, których dane dotyczą, ponieważ pozwala ono użytkownikom dodawanie właściwości niestandardowych do obiektów bazy danych i oznaczania tagami danych jako "Wycofany" na potrzeby obsługi aplikacji logiki, aby zapobiec Przetwarzanie skojarzone dane osobowe.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pozwala użytkownikom na definiowanie zasad, aby ograniczyć dostęp do danych, aby zaprzestać przetwarzania.
- [SQL Database dynamicznych danych maskowania (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych osobowych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. DDM może automatycznie odnajdywać potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. Dzięki temu identyfikację danych osobowych kwalifikujących się GDPR ochrony oraz do zmniejszenia dostępu w taki sposób, że nie istnieje w bazie danych za pośrednictwem przed nieautoryzowanym dostępem. **Uwaga: Klienci będą musieli dostosować ustawienia DDM stosować się do ich schemat bazy danych.**

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych osobowych w środowisku platformy Azure:
-   [Usługa Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) to usługa firmy Microsoft wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami zarządzania. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze AAD, w tym użytkowników uzyskujących dostęp do bazy danych SQL.
-   Do aplikacji uwierzytelniania przy użyciu usługi AAD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi AAD do uwierzytelniania aplikacji w usłudze Azure SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Usługa Azure opartej na rolach kontrola dostępu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom zdefiniować uprawnienia dostępu w zakresie, aby udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast co nieograniczone uprawnienia dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych osobowych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługi AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do pewnych informacji, takich jak dane osobowe.  Administratorzy mogą używać usługi AAD Privileged Identity Management, aby odkryć, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Ta funkcja może również wymuszać na żądanie, just-in-time dostęp administracyjny w razie.
- [Ochrona tożsamości usługi AAD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki mające wpływ na tożsamości w organizacji, umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości organizacji i bada podejrzanych zdarzenia, które można podjąć odpowiednie działania, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomóc klientom chronić dane osobowe i dostęp do tych danych:
- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń (HSM). Typ klucza jest HSM chronione 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień, korzystając z modelu RBAC.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.

**Zarządzanie poprawkami**: Windows wdrożone maszyny wirtualne w ramach tej architektury referencyjnej są domyślnie skonfigurowane, automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To rozwiązanie obejmuje również [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których zaktualizowano wdrożenia mogą być tworzone do poprawki maszyny wirtualne w razie.

**Ochrona przed szkodliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, ułatwiający identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania za pomocą konfigurowalnych alertów gdy znany złośliwego lub niechcianego oprogramowania podejmuje próbę zainstalowania lub uruchomienia na chronionych maszynach wirtualnych.

**Alerty zabezpieczeń**: [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) służy do monitorowania ruchu, zbieranie dzienników i analizowanie źródeł danych pod kątem zagrożeń. Ponadto usługa Azure Security Center uzyskuje dostęp do istniejącej konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych osobowych. Usługa Azure Security Center obejmuje [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) dla każdej wykrytej przed zagrożeniami, aby pomóc zespołom reagowania na zdarzenia badaniu i usuwaniu zagrożeń.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej
**Wysoka dostępność**: obciążenia serwera są zgrupowane w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) do pomagają zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Co najmniej jednej maszyny wirtualnej są dostępne podczas planowanych lub nieplanowanych zdarzeń związanych z konserwacją spotkania 99,95% umowy SLA platformy Azure.

**Magazyn usługi Recovery Services**: [magazyn usługi Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. Przy użyciu magazynu usługi Recovery Services klienci mogą przywracać pliki i foldery z maszyny Wirtualnej IaaS bez przywracania całej maszyny Wirtualnej, umożliwiając skraca czas ich przywracania.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji
[Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) zapewnia szczegółowe rejestrowanie aktywności systemu i użytkownika, a także kondycji systemu. [Usługi Log Analytics](https://azure.microsoft.com/services/log-analytics/) rozwiązanie zbiera i analizuje dane generowane przez zasoby na platformie Azure i środowiskach lokalnych.
- **Dzienniki aktywności**: [dzienników aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows i magazynu Azure Blob, tabel i Dzienniki kolejek.
- **Zaloguj się archiwizowania**: wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Okres przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, spełniają wymagania specyficzne dla organizacji przechowywania. Te dzienniki połączyć z usługą Azure Log Analytics do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym.

Oprócz następujących rozwiązań usługi Log Analytics są uwzględniane w ramach tej architektury:
-   [Ocena usługi AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory Health Check rozwiązanie ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Ocena ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): rozwiązanie chroniące przed złośliwym kodem raporty dotyczące złośliwego oprogramowania, zagrożeń i ochronę stanu.
-   [Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): rozwiązanie usługi Azure Automation przechowuje, uruchamia i zarządza elementami runbook.
-   [Zabezpieczenia i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpieczenia i inspekcja pulpit nawigacyjny zawiera ogólne informacje o stanie zabezpieczeń zasobów, zapewniając metryki w przypadku domen zabezpieczeń, problemy godne uwagi, wykrywania, analizy zagrożeń i typowe zapytania dotyczące zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): rozwiązania SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): rozwiązanie do zarządzania aktualizacjami umożliwia zarządzanie klientami z aktualizacjami zabezpieczeń systemu operacyjnego, w tym stan dostępnych aktualizacji i procesu instalacji wymaganych aktualizacji.
-   [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.
-   [Śledzenie zmian](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązanie do śledzenia zmian umożliwia klientom łatwo identyfikować zmiany w środowisku.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/gdprDWdfd) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![Data Warehouse, model zagrożeń RODO](images/gdpr-datawarehouse-threat-model.png?raw=true "magazynu danych na potrzeby model zagrożeń RODO")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Zabezpieczeń platformy Azure i zgodności planu — RODO klienta odpowiedzialność macierzy](https://aka.ms/gdprCRM) Wyświetla obowiązki kontrolera i procesora dla wszystkich artykułów RODO. Należy pamiętać, usług systemu Azure, klient jest zazwyczaj kontrolera i Microsoft działa jako procesor.

[Zabezpieczeń platformy Azure i zgodności planu — macierz wdrożenia magazynu danych RODO](https://aka.ms/gdprDW) zawiera informacje, na które RODO artykuły są rozwiązywane przez architektury magazynu danych, w tym szczegółowe opisy sposób, w jaki Implementacja spełnia wymagania części każdego artykułu objętego usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi zostać skonfigurowany nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej magazynu danych. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można utworzyć wirtualnego prywatne połączenie między siecią lokalną i usługą Azure Virtual Network. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne "tuneli" informacji wewnątrz zaszyfrowane połączenia między siecią i platformą Azure przez klienta. Sieci VPN typu lokacja-lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu VPN przechodzą przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje opcję inny, jeszcze bardziej bezpieczne połączenia. Usługa ExpressRoute systemu Azure jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto ponieważ jest to bezpośrednie połączenie klienta, dostawca usług telekomunikacyjnych, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest uwidaczniana, aby go.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Proces wyodrębniania, przekształcania, ładowania (ETL)
[Program PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) można załadować dane do usługi Azure SQL Data Warehouse bez konieczności stosowania osobnych ETL lub zaimportować narzędzia. Program PolyBase umożliwia dostęp do danych za pomocą zapytań T-SQL. Przy użyciu technologii PolyBase może służyć przez firmę Microsoft analizy biznesowej i stos analizy, a także narzędzia innych producentów są zgodne z programem SQL Server.

### <a name="azure-active-directory-setup"></a>Konfiguracja usługi Azure Active Directory
[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest Zarządzanie wdrożeniem i inicjowania obsługi dostępu do personelu wchodzenie w interakcje ze środowiskiem. Istniejącego systemu Windows Server Active Directory można zintegrować z usługą AAD w [cztery kliknięcia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Klienci również powiązać wdrożonej infrastrukturę usługi Active Directory (kontrolery domeny) do istniejącej usługi AAD, wprowadzając wdrożonej infrastrukturę usługi Active Directory poddomeny las usługi AAD.

### <a name="optional-services"></a>Usługi opcjonalne
Platforma Azure oferuje szereg usług na potrzeby magazynu i przemieszczania danych sformatowanego i niesformatowane. Następujące usługi mogą być dodawane do tej architektury referencyjnej, w zależności od wymagań klientów:
-   [Usługa Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) to usługa zarządzana usługa w chmurze, która została skonstruowana na potrzeby kompleksowych, hybrydowych extract-transform-load (ETL), wyodrębnianie ładowanie transformacja (ELT) i danych projekty integracji. Usługa Azure Data Factory ma możliwości śledzenia i Znajdź dane osobowe, łącznie z wizualizacją i monitorowania narzędzi do identyfikacji, gdy źródła otrzymanych danych, a, z którego pochodzi. Za pomocą usługi Azure Data Factory, klientów można tworzyć i planować opartych na danych przepływy pracy o nazwie potoki, które pozyskiwać dane z różnych magazynów danych. Te potoki Zezwalaj klientom na odbieranie danych z wewnętrznych i zewnętrznych źródeł. Klienci, a następnie jest przetwarzanie i przekształcanie danych dla danych wyjściowych w magazynach danych, takich jak Azure SQL Data Warehouse.
- Klientów można umieścić dane bez struktury w [usługi Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), które umożliwia przechwytywanie danych o dowolnym rozmiarze, typie, i pozyskiwania szybkości w jednym miejscu na potrzeby analizy operacyjnej i rozpoznawczej.  Usługa Azure Data Lake ma funkcje, które umożliwiają wyodrębnianie i konwersja danych. Azure Data Lake Store jest zgodna z najbardziej otwarta składnikami źródła w ekosystemie usługi Hadoop i bardzo dobrze integruje się z innymi usługami Azure, takich jak Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
 - W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
 - Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
 - Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
