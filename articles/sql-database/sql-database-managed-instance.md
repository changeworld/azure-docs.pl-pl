---
title: Baza danych Azure SQL zarządzane wystąpienia omówienie | Dokumentacja firmy Microsoft
description: W tym temacie opisano wystąpienia zarządzane bazy danych SQL Azure oraz wyjaśniono, jak działa i jak różni się od pojedynczej bazy danych w bazie danych SQL Azure.
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/10/2018
ms.author: bonova
ms.openlocfilehash: eeb6b74fb7dfbf25e27963dd7a2f7f431feebcc8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="what-is-a-managed-instance-preview"></a>Co to jest wystąpienie zarządzane (wersja zapoznawcza)?

Azure wystąpienia bazy danych SQL zarządzane (wersja zapoznawcza) jest nową funkcją bazy danych SQL Azure, zapewniając niemal zgodności 100% z programu SQL Server lokalne (Enterprise Edition), zapewniając natywny [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md) wdrożenia, którego dotyczy typowe problemy z zabezpieczeniami i [modelu biznesowego](https://azure.microsoft.com/pricing/details/sql-database/) korzystna dla lokalnego programu SQL Server klientów. Zarządzane wystąpienia umożliwia istniejących klientów programu SQL Server do podnoszenia i przesunięcia ich aplikacji lokalnych do chmury przy minimalnych zmianach aplikacji i baz danych. W tym samym czasie wystąpienia zarządzane zachowuje wszystkie funkcje PaaS (Aktualizacje automatyczne stosowanie poprawek i wersja, kopia zapasowa, wysokiej dostępności), które znacząco zmniejsza nakład pracy i całkowitego kosztu posiadania.

> [!IMPORTANT]
> Listę regionów, w których jest obecnie dostępne wystąpienie zarządzane, można znaleźć w temacie [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance (Migrowanie baz danych do w pełni zarządzanej usługi za pomocą wystąpienia zarządzanego usługi Azure SQL Database)](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/).
 
Poniższy diagram przedstawia główne funkcje wystąpienia zarządzane:

![Najważniejsze funkcje](./media/sql-database-managed-instance/key-features.png)

Zarządzane wystąpienia jest Zaplanowaliśmy jako preferowaną platformę w następujących scenariuszach: 

- Lokalnej instalacji programu SQL Server / IaaS klientów, którzy migrację aplikacji i w pełni zarządzaną usługę z minimalnym projektowania zmiany.
- Niezależnym dostawcom oprogramowania polegania na bazy danych SQL, którzy chcą włączyć użytkownikom na migrację do chmury i w związku z tym osiągnąć znaczne konkurencyjnej lub osiągnięcia globalnego rynku. 

Zarządzane wystąpienia przez ogólnej dostępności ma na celu dostarczenia blisko powierzchni 100% zgodności z najnowszą wersją programu SQL Server lokalnej za pośrednictwem planu przemieszczanego wersji. 

Wytyczne tabeli klucza różnice i Zaplanowaliśmy scenariusze użycia między SQL IaaS, baza danych SQL Azure i zarządzane wystąpienia bazy danych SQL:

| | Scenariusz użycia | 
| --- | --- | 
|Wystąpienie zarządzane usługi SQL Database |Dla klientów chcących migracji duża liczba aplikacji z lokalnymi lub IaaS własnym utworzony lub niezależnego dostawcy oprogramowania, pod warunkiem z jako niskie migracji nakładu pracy, jak to możliwe, zaproponować zarządzane wystąpienia. Przy użyciu w pełni zautomatyzowanego [usługi migracji danych (DMS)](/sql/dma/dma-overview) na platformie Azure, klientom przyrostu i przesunięcia ich lokalnego programu SQL Server do wystąpienia zarządzane, który zapewnia zgodność z lokalnej instalacji programu SQL Server i pełne izolacji wystąpienia klienta macierzystą obsługę sieci Wirtualnej.  Pakiet Software Assurance należy wymienić ich istniejących licencji dla rabaty na zarządzane wystąpienia bazy danych SQL za pomocą [korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Zarządzane wystąpienia bazy danych SQL jest najlepsze miejsce docelowe migracji w chmurze dla wystąpień programu SQL Server, które wymagają wysokiego poziomu zabezpieczeń i powierzchni programowalności sformatowanego. |
|Baza danych SQL Azure (pojedyncza lub puli) |**Pule elastyczne**: dla klientów, tworzenie nowych aplikacji z wieloma dzierżawcami SaaS lub celowo Przekształcanie istniejącej lokalnej aplikacji do wielodostępnych aplikacji SaaS, zaproponować elastyczne pule. Zalety tego modelu to: <br><ul><li>Konwersja modelu biznesowego, ze sprzedaży licencji sprzedaży subskrypcji usługi (dla ISV)</li></ul><ul><li>Łatwe i dowód punktor dzierżawy izolacji</li></ul><ul><li>Uproszczony model programowania skoncentrowane bazy danych</li></ul><ul><li>Możliwość skalowania bez naciśnięcie twardych limitu</li></ul>**Pojedyncze bazy danych**: dla klientów, tworzenie nowych aplikacji innych niż wielodostępne SaaS, której obciążenie jest stabilne i przewidywalne, zaproponować pojedynczych baz danych. Zalety tego modelu to:<ul><li>Uproszczony model programowania skoncentrowane bazy danych</li></ul>  <ul><li>Przewidywalna wydajność dla każdej bazy danych</li></ul>|
|Maszyny wirtualne SQL IaaS|W przypadku konieczności dostosowywania systemu operacyjnego lub serwer bazy danych, jak również klientów mających określone wymagania w zakresie uruchamianie aplikacji innych firm siebie z programem SQL Server (w tej samej maszyny Wirtualnej), klienci zaproponować maszyn wirtualnych SQL / IaaS jako najlepszego rozwiązania|
|||

## <a name="how-to-programmatically-identify-a-managed-instance"></a>Jak zidentyfikować programowo wystąpienie usługi zarządzania

W poniższej tabeli przedstawiono kilka właściwości dostępne za pośrednictwem Transact SQL, można użyć do wykrywania, czy aplikacja działa z wystąpieniem zarządzane i pobrać ważne właściwości.

|Właściwość|Wartość|Komentarz|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 firmy Microsoft Corporation.|Ta wartość jest taka sama jak baza danych SQL.|
|`SERVERPROPERTY ('Edition')`|Usługi SQL Azure|Ta wartość jest taka sama jak baza danych SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Ta wartość jednoznacznie identyfikuje wystąpienie zarządzane.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nazwa DNS pełnego wystąpienia w następującym formacie:<instanceName>.<dnsPrefix>. Database.Windows.NET, gdzie <instanceName> jest nazwa podana przez klienta, podczas gdy <dnsPrefix> jest generowane automatycznie część nazwy gwarantujących globalne unikatowość nazwy DNS ("wcus17662feb9ce98", na przykład)|Example: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>Najważniejsze funkcje i możliwości wystąpienia zarządzane 

> [!IMPORTANT]
> Wystąpienie usługi zarządzania jest uruchamiany z wszystkich funkcji najnowszej wersji programu SQL Server, w tym operacje online, automatyczne plan korekty i inne rozszerzenia wydajności enterprise. 

| **Korzyści PaaS** | **Ciągłość działalności biznesowej:** |
| --- | --- |
|Zakup sprzętu i zarządzania <br>Nie zarządzania nakładów pracy związanych z zarządzaniem podstawowej infrastruktury <br>Szybkie inicjowania i skalowania usługi <br>Automatyczne stosowanie poprawek i wersja uaktualnienia <br>Integracja z innymi usługami danych PaaS |czas działania 99,99% umowy SLA  <br>Wbudowane wysokiej dostępności <br>Dane chronione przy użyciu automatycznego tworzenia kopii zapasowych <br>Okres przechowywania kopii zapasowych można skonfigurować klienta (ustalona na 7 dni w publicznej wersji zapoznawczej) <br>Tworzenie kopii zapasowych zainicjowane przez użytkownika <br>Możliwość przywrócenia punktu w czasie w bazie danych |
|**Zabezpieczeń i zgodności** | **Zarządzanie**|
|Środowiska izolowane (integracji sieci wirtualnej, Usługa pojedynczej dzierżawy, dedykowane obliczeniowej i magazynu <br>Szyfrowanie danych podczas przesyłania <br>Azure AD authentication, obsługę rejestracji jednokrotnej <br>Zgodnego ze standardami zgodności takie same jak baza danych Azure SQL <br>Inspekcja SQL <br>Wykrywanie zagrożeń |Interfejs API Menedżera zasobów Azure do automatyzacji usługi inicjowania i skalowania <br>Azure funkcje portalu usługi ręcznego inicjowania i skalowania <br>Usługi danych migracji 

![Logowanie jednokrotne](./media/sql-database-managed-instance/sso.png) 

## <a name="vcore-based-purchasing-model"></a>na podstawie vCore model kupna

Model kupna vCore zapewnia elastyczność, przejrzystości, formantowi i łatwe do tłumaczenia lokalne wymagania obciążenia w chmurze. Ten model umożliwia skalowanie możliwości obliczeniowych, pamięci oraz Magazyn ustalane na podstawie jego potrzeb obciążenia. VCore model również jest uprawniona do się do 30 procent oszczędności za pomocą [korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Wirtualne podstawowe reprezentuje Procesora logicznego oferowany z opcją wybór między generacje sprzętu.
- Logiczne procesory CPU 4. generacji wykorzystują procesory Intel E5-2673 v3 (Haswell) z zegarem 2,4 GHz.
- Gen 5 procesorów logicznych są oparte na Intel E5-2673 w wersji 4 (Broadwell) 2.3 GHz procesorów.

Poniższa tabela ułatwia zrozumienie sposobu wybierania optymalną konfigurację obliczeniowych, pamięci, magazynu i zasobów we/wy.

||4. generacja|5. generacja|
|----|------|-----|
|Sprzęt|Intel E5-2673 v3 (Haswell) procesorów 2,4 GHz dołączonych dysków SSD vCore = 1 PZ (fizyczne rdzenie)|Intel E5-2673 w wersji 4 (Broadwell) 2.3 GHz procesorów, szybkie eNVM dysków SSD, vCore = LP 1 (hyper wątek)|
|Poziomy wydajności|8, 16, 24 vCores|8, 16, 24, 32, 40 vCores|
|Memory (Pamięć)|7GB na vCore|5.5GB na vCore|
||||

## <a name="managed-instance-service-tier"></a>Zarządzane wystąpienia warstwy usług

Wystąpienie zarządzanych jest początkowo dostępne w warstwie usług pojedynczego - ogólnego przeznaczenia — przeznaczony dla aplikacji z typowych dostępności i wymogach opóźnienia we/wy.

Poniższa lista zawiera opis klucza charakterystyczne dla warstwy usług ogólnego przeznaczenia: 

- Projektowanie dla większości aplikacji biznesowych z typowych wydajności i wymagania dotyczące wysokiej dostępności 
- Wysokiej wydajności magazynu Azure Premium (8 TB) 
- 100 bazy danych / wystąpienia 

W tej warstwie niezależnie można wybrać magazyn i moc obliczeniową. 

Na poniższym diagramie przedstawiono active obliczeniowych i nadmiarowych węzłów w tej warstwie usług.
 
![Warstwy usług celu ogólne](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

Poniżej opisano najważniejsze funkcje ogólnego przeznaczenia warstwy usług:

|Cecha | Opis|
|---|---|
| Liczba vCores * | 8, 16, 24 (gł 4)<br>8, 16, 24, 32, 40 (Gen5)|
| Wersja programu SQL Server / kompilacji | SQL Server najnowszych (dostępne) |
| Minimalny rozmiar magazynu | 32 GB |
| Maksymalny rozmiar magazynu | 8 TB |
| Maksymalna liczba magazyn na bazę danych | 8 TB |
| Oczekiwano magazynu IOPS | 500-7500 IOPS dla pliku danych (w zależności od danych plików). Zobacz [magazyn w warstwie Premium](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| Liczba plików danych (wiersze) na bazie danych | Wiele | 
| Liczba plików dziennika (dziennik) na bazę danych | 1 | 
| Zarządzane automatycznych kopii zapasowych | Yes |
| HA | Oparte na magazyn zdalny i [sieć szkieletowa usług Azure](../service-fabric/service-fabric-overview.md) |
| Wbudowane wystąpienia i bazy danych monitorowania i metryki | Yes |
| Oprogramowanie automatyczne stosowanie poprawek | Yes |
| Sieć wirtualna — wdrożenie usługi Azure Resource Manager | Yes |
| Sieć wirtualna — klasycznego modelu wdrożenia | Nie |
| Portalu pomocy technicznej | Yes|
|||

\* Wirtualne podstawowe reprezentuje Procesora logicznego oferowany z opcją wybór między generacje sprzętu. Gen 4 procesory logiczne są oparte na Intel E5-2673 v3 (Haswell) procesorów 2,4 GHz i procesorów logicznych Gen 5 są oparte na Intel E5-2673 w wersji 4 (Broadwell) 2.3 GHz procesorów. 

## <a name="advanced-security-and-compliance"></a>Zaawansowane zabezpieczenia i zgodność 

### <a name="managed-instance-security-isolation"></a>Zarządzane wystąpienia zabezpieczeń izolacji 

Zarządzane wystąpienia zapewniają izolację dodatkowe zabezpieczenia od pozostałych dzierżawców w chmurze Azure. Izolacji zabezpieczeń obejmuje: 

- [Implementacja natywnego sieci wirtualnej](sql-database-managed-instance-vnet-configuration.md) i łączności do środowiska lokalnego przy użyciu Azure Express Route lub brama sieci VPN 
- Punkt końcowy programu SQL jest widoczne tylko za pośrednictwem prywatnego adresu IP, umożliwiając bezpieczne połączenie z prywatnej Azure lub hybrydowej sieci
- Pojedynczego dzierżawcy z dedykowanym podstawowej infrastruktury (obliczeniowych, pamięci masowej)

Poniższy diagram przedstawia izolacji projektu: 

![Wysoka dostępność](./media/sql-database-managed-instance/application-deployment-topologies.png)  

### <a name="auditing-for-compliance-and-security"></a>Inspekcja zgodności i zabezpieczeń 

[Zarządzane wystąpienia inspekcji](sql-database-managed-instance-auditing.md) śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania na koncie magazynu Azure. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń. 

### <a name="data-encryption-in-motion"></a>Szyfrowanie danych w ruchu 

Zarządzane wystąpienia zabezpiecza dane przez zapewnienie szyfrowanie danych w ruchu przy użyciu Transport Layer Security.

Oprócz zabezpieczeń warstwy transportu, zarządzane wystąpienia bazy danych SQL oferuje ochrony danych poufnych w locie, podczas przechowywania i podczas przetwarzania z zapytań [zawsze zaszyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Funkcja Always Encrypted to pierwsze w branży rozwiązanie, które oferuje bezkonkurencyjne zabezpieczenie danych przed naruszeniami, w tym przed kradzieżą danych o kluczowym znaczeniu. Na przykład z zawsze zaszyfrowane, numery kart kredytowych są przechowywane w bazie danych zawsze, nawet w zaszyfrowanej zapytania przetwarzania, umożliwiającą odszyfrowywania w punkcie użycia przez autoryzowanego personelu lub aplikacji wymagających przetworzenia danych. 

### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych 

Baza danych SQL [maskowania danych dynamicznych](/sql/relational-databases/security/dynamic-data-masking) ogranicza ujawnienie danych poufnych przez maskowania go do nieuprzywilejowanym użytkownikom. Maskowanie danych dynamicznych pomaga zapobiec nieautoryzowanemu dostępowi do danych poufnych, umożliwiając wyznaczyć ilość poufnych danych w celu odkrycia przy minimalnym wpływie na warstwie aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian. 

### <a name="row-level-security"></a>Zabezpieczenia na poziomie wiersza 

[Wiersz poziomu zabezpieczeń](/sql/relational-databases/security/row-level-security) umożliwia kontrolę dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonywanie zapytania (takich jak przez kontekst grupy członkostwa lub wykonywania). Zabezpieczenia na poziomie wiersza (RLS, Row-Level Security) upraszczają projektowanie i kodowanie zabezpieczeń w aplikacji. Zabezpieczenia na poziomie wiersza umożliwiają zaimplementowanie ograniczeń w dostępie do wiersza danych. Na przykład zapewniając, że pracownicy mają dostęp tylko wiersze danych, które są odpowiednie do ich działu lub ograniczanie dostępu do danych do odpowiednich danych. 

### <a name="threat-detection"></a>Wykrywanie zagrożeń 

[Wykrywanie zagrożeń wystąpienia zarządzane](sql-database-managed-instance-threat-detection.md) uzupełnia [inspekcji zarządzane wystąpienia](sql-database-managed-instance-auditing.md) zapewniając dodatkową warstwę zabezpieczeń analizy wbudowanych w usługę, która wykrywa nietypowe i potencjalnie szkodliwe próby dostęp i wykorzystać baz danych. Alerty o podejrzanych działań, potencjalnych luk i ataków iniekcja kodu SQL, a także bazy danych nietypowe wzorce dostępu. Można wyświetlić alertów wykrywania zagrożeń z [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) zawierają szczegółowe informacje o podejrzanych działaniach i zalecane działania dotyczące sposobu badania i ograniczyć zagrożenie.  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym 

Usługa SQL Database umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft dzięki funkcji [integracji usługi Azure Active Directory](sql-database-aad-authentication.md). Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Usługa Azure Active Directory obsługuje [uwierzytelnianie wieloskładnikowe](sql-database-ssms-mfa-authentication-configure.md) (MFA, Multi-Factor Authentication) w celu zwiększenia bezpieczeństwa danych i aplikacji, korzystając z procesu jednokrotnego logowania. 

### <a name="authentication"></a>Authentication 
Uwierzytelnianie bazy danych SQL odnosi się do sposobu użytkowników potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:  

- Uwierzytelnianie SQL, które używa nazwy użytkownika i hasła.
- Azure uwierzytelnianie usługi Active Directory, który używa tożsamości zarządzanych przez usługę Azure Active Directory i jest obsługiwana w przypadku domen zarządzanych i zintegrowane. 

### <a name="authorization"></a>Autoryzacja

Autoryzacji odwołuje się do użytkownika używanego można wykonać w bazie danych SQL Azure i kontrolowane przez członkostwo roli bazy danych i uprawnień na poziomie obiektu konta użytkownika. Zarządzane wystąpienie ma takie same możliwości autoryzacji jako 2017 serwera SQL. 

## <a name="database-migration"></a>Migracja bazy danych 

Zarządzane wystąpienia obiektów docelowych użytkownika scenariusze migracji masowej bazy danych z lokalnymi lub IaaS implementacje bazy danych. Zarządzane wystąpienie obsługuje kilka opcji migracji bazy danych: 

### <a name="data-migration-service"></a>Usługi danych migracji

Usługa Azure bazy danych migracji jest pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemowego migracji z wielu źródeł bazy danych do platformy Azure danych z minimalnym czasem przestojów. Ta usługa upraszcza zadania wymagane do przenoszenia istniejącego innych firm i baz danych programu SQL Server na platformie Azure. Opcje wdrażania obejmują usługi Azure SQL Database, zarządzane wystąpienia i programu SQL Server w maszynie Wirtualnej platformy Azure w publicznej wersji zapoznawczej. Zobacz [jak przeprowadzić migrację z lokalnej bazy danych do wystąpienia zarządzane przy użyciu DMS](https://aka.ms/migratetoMIusingDMS). 

### <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie  

Metoda migracji wykorzystuje kopii zapasowych SQL do przechowywania obiektów blob platformy Azure. Kopie zapasowe przechowywane w obiekcie blob magazynu Azure może bezpośrednio przywrócony do zarządzanego wystąpienia. Aby przywrócić istniejącej bazy danych SQL do wystąpienia zarządzany, możesz:

- Użyj [usługi migracji danych (DMS)](/sql/dma/dma-overview). Samouczek, zobacz [migracji do wystąpienia zarządzane przy użyciu usługi migracji bazy danych Azure (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) przywrócenie z pliku kopii zapasowej bazy danych
- Użyj [polecenia T-SQL Przywróć](https://docs.microsoft.com/en-us/sql/t-sql/statements/restore-statements-transact-sql). 
  - Samouczek przedstawiający sposób przywracania World Wide Importers — standardowe pliku kopii zapasowej, zobacz [przywrócić pliku kopii zapasowej do wystąpienia zarządzane](sql-database-managed-instance-restore-from-backup-tutorial.md). Ten samouczek pokazuje, że masz przekazywany plik kopii zapasowej do magazynu Azure blogu i bezpieczne przy użyciu klucza sygnatury dostępu Współdzielonego dostępu współużytkowanego.
  - Aby uzyskać informacje dotyczące przywracania z adresu URL, zobacz [natywnego Przywracanie z adresu URL](sql-database-managed-instance-migrate.md#native-restore-from-url).
- [Importuj z pliku pliku BACPAC](sql-database-import.md)

## <a name="sql-features-supported"></a>Obsługiwane funkcje SQL 

Zarządzane wystąpienia ma na celu dostarczenia blisko powierzchni 100% zgodności z lokalnym programem SQL Server przychodzących etapy, aż do ogólnej dostępności usługi. Dla funkcji i listy porównanie, zobacz [wspólne funkcje SQL](sql-database-features.md).
 
Zarządzane wystąpienia zgodności z poprzednimi wersjami obsługuje do baz danych SQL 2008. Migracja bezpośrednio z serwerami baz danych programu SQL 2005 jest obsługiwana, poziom zgodności bazy danych SQL 2005 zmigrowane zostały zaktualizowane do programu SQL 2008. 
 
Poniższy diagram przedstawia zgodność powierzchni w wystąpieniu zarządzane:  

![Migracji](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Podstawowe różnice między lokalnej instalacji programu SQL Server oraz wystąpienia zarządzane 

Zarządzane wystąpienia korzyści z jest zawsze do uaktualnia w chmury, co oznacza, że niektóre funkcje w lokalnym programie SQL Server mogą być nieaktualne, wycofane lub mieć alternatyw. Istnieją szczególnych przypadkach, gdy potrzeba rozpoznaje, że poszczególnych funkcji działa w sposób nieco inny lub usługa nie jest uruchomiona w środowisku, które nie pełni kontroli narzędzi: 

- Wysokiej dostępności jest wbudowana i wstępnie skonfigurowane. Zawsze włączone funkcje wysokiej dostępności nie są widoczne w taki sam sposób jak w implementacji SQL IaaS 
- Automatyczne kopie zapasowe i punktu w czasie przywracania. Odbiorcy mogą inicjować `copy-only` kopie zapasowe, które nie zakłóca automatyczne łańcuch kopii zapasowych. 
- Zarządzane wystąpienie nie zezwala na określanie ścieżek pełną fizyczną, więc wszystkich odpowiednich scenariuszach muszą być obsługiwane inaczej: Przywracanie bazy danych nie obsługuje przenoszenie, tworzenie bazy danych nie zezwala na ścieżki fizyczne, BULK INSERT współpracuje z obiektami blob Azure tylko itp. 
- Zarządzane wystąpienie obsługuje [uwierzytelniania usługi Azure AD](sql-database-aad-authentication.md) chmury alternatywę dla uwierzytelniania systemu Windows. 
- Zarządzane wystąpienia automatycznie zarządza XTP grupy plików i plików baz danych zawierających obiekty OLTP w pamięci
 
### <a name="managed-instance-administration-features"></a>Funkcje administracyjne wystąpienia zarządzane  

Zarządzane wystąpienia Włącz administratorowi systemu skupić się na co jest ważne, większość dla firm. Wiele działań administratora/administrator systemu nie są wymagane, lub są proste. Na przykład systemu operacyjnego / RDBMS instalacji i aktualizacji dynamicznej wystąpienie zmiana rozmiaru i konfiguracji, kopie zapasowe, replikacji bazy danych (w tym systemowych baz danych), konfiguracja wysokiej dostępności i konfigurację danych monitorowania kondycji i wydajności strumienie. 

> [!IMPORTANT]
> Aby uzyskać listę obsługiwanych, częściowo obsługiwane i nieobsługiwane funkcje, zobacz [funkcje bazy danych SQL](sql-database-features.md). Aby uzyskać listę różnice T-SQL w zarządzanych wystąpień i SQL Server, zobacz [zarządzane wystąpienia T-SQL różnice z programu SQL Server](sql-database-managed-instance-transact-sql-information.md)
 
## <a name="next-steps"></a>Kolejne kroki

- Dla funkcji i listy porównanie, zobacz [wspólne funkcje SQL](sql-database-features.md).
- Aby uzyskać więcej informacji o konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-vnet-configuration.md).
- Samouczek, która tworzy wystąpienie zarządzane i przywrócenie bazy danych z pliku kopii zapasowej, zobacz [utworzyć wystąpienia zarządzanego](sql-database-managed-instance-create-tutorial-portal.md).
- Aby skorzystać z samouczka w zakresie używania usługi Azure Database Migration Service (DMS) do celów migracji, zobacz [Migracja wystąpień zarządzanych przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).
