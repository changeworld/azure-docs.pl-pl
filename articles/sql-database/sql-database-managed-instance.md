---
title: Usługa Azure SQL Database Managed Przegląd instancji | Dokumentacja firmy Microsoft
description: W tym temacie opisano wystąpienia usługi Azure SQL Database Managed oraz wyjaśniono, jak to działa, i jak jest inny niż pojedynczej bazy danych w usłudze Azure SQL Database.
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: bonova
ms.openlocfilehash: 698b02e1bad7da3f793a35184d634eaf5c948988
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071581"
---
# <a name="what-is-a-managed-instance-preview"></a>Co to jest wystąpienie zarządzane (wersja zapoznawcza)?

Wystąpienie usługi Azure SQL Database Managed (wersja zapoznawcza) to nowa funkcja usługi Azure SQL Database, zapewniając prawie 100% utrzymywania zgodności z programu SQL Server (Enterprise Edition), lokalne dostarczanie natywny [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md) wdrożenia, odnoszący się do typowe problemy dotyczące zabezpieczeń i [modelu biznesowego](https://azure.microsoft.com/pricing/details/sql-database/) korzystna dla klientów programu SQL Server w środowisku lokalnym. Wystąpienie zarządzane umożliwia istniejących klientów programu SQL Server do lift- and -shift ich aplikacji lokalnych do chmury przy minimalnych zmianach aplikacji i baz danych. W tym samym czasie wystąpienie zarządzane zachowuje wszystkie możliwości PaaS (Aktualizacje automatyczne stosowanie poprawek i wersja, kopia zapasowa, wysokiej dostępności), które znacząco zmniejsza koszty zarządzania i całkowitego kosztu posiadania.

> [!IMPORTANT]
> Listę regionów, w których jest obecnie dostępne wystąpienie zarządzane, można znaleźć w temacie [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance (Migrowanie baz danych do w pełni zarządzanej usługi za pomocą wystąpienia zarządzanego usługi Azure SQL Database)](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/).
 
Poniższy diagram przedstawia kluczowe funkcje wystąpienia zarządzanego:

![Najważniejsze funkcje](./media/sql-database-managed-instance/key-features.png)

Wystąpienie zarządzane jest Zaplanowaliśmy jako preferowaną platformę w następujących scenariuszach: 

- / Klientom IaaS, które chcesz migrować swoje aplikacje, aby w pełni zarządzana usługa przy minimalnym projektowanie zmian programu SQL Server w środowisku lokalnym.
- Niezależni dostawcy oprogramowania, opierając się na baz danych SQL, którzy chcą włączyć swoim klientom migrację do chmury i dlatego osiągnąć znaczną przewagę konkurencyjną lub dotrzeć do rynku globalnego. 

Wg ogólnej dostępności wystąpienia zarządzanego ma na celu dostarczać blisko powierzchni 100% zgodności z najnowszą wersją programu SQL Server w środowisku lokalnym za pośrednictwem planu wersji etapowe. 

Poniższej przedstawiono tabeli klucza różnice i Zaplanowaliśmy scenariusze użycia między SQL IaaS, usługi Azure SQL Database i wystąpienie zarządzane usługi SQL Database:

| | Scenariusz użycia | 
| --- | --- | 
|Wystąpienie zarządzane usługi SQL Database |W przypadku klientów poszukujących migracji dużej liczby aplikacji z lokalną lub IaaS, utworzone samodzielnie lub niezależnego dostawcy oprogramowania, pod warunkiem za pomocą jako niski migracji nakład pracy, jak to możliwe, zaproponować wystąpienia zarządzanego. Przy użyciu w pełni zautomatyzowane [Data Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) na platformie Azure, klienci mogą lift- and shift ich na lokalnym serwerze SQL Server do wystąpienia zarządzanego, który zapewnia zgodność z programu SQL Server w środowisku lokalnym i pełną izolację pamięci wystąpienia klientów z natywną obsługą sieci Wirtualnej.  Z pakietem Software Assurance mogą wymieniać swoich istniejących licencji do korzystania z taryf rabatowych na wystąpieniu zarządzanym bazy danych SQL przy użyciu [korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Wystąpienie zarządzane usługi SQL Database to najlepsze miejsce docelowe migracji w chmurze dla wystąpień programu SQL Server, które wymagają wysokiego poziomu zabezpieczeń i powierzchni sformatowanego programowania. |
|Usługa Azure SQL Database (pojedyncza lub puli) |**Pule elastyczne**: dla klientów tworzenia nowych aplikacji z wieloma dzierżawami SaaS lub celowo przekształcanie ich istniejących aplikacji lokalnych do wielodostępnych aplikacji SaaS, zaproponować pul elastycznych. Zalety tego modelu są: <br><ul><li>Konwersja modelu biznesowego sprzedaży licencji do sprzedaży, subskrypcji usług (w przypadku niezależnych dostawców oprogramowania)</li></ul><ul><li>Izolacji dzierżawcy łatwy i dowód punktora</li></ul><ul><li>Uproszczony model programowania skoncentrowane na bazy danych</li></ul><ul><li>Możliwość skalowania bez osiągnięcia limitu twarde</li></ul>**Pojedyncze bazy danych**: dla klientów, tworzenie nowych aplikacji innych niż SaaS wielodostępne, których obciążenie jest stabilne i przewidywalne, zaproponować pojedynczych baz danych. Zalety tego modelu są:<ul><li>Uproszczony model programowania skoncentrowane na bazy danych</li></ul>  <ul><li>Przewidywalna wydajność dla każdej bazy danych</li></ul>|
|Maszyna wirtualna SQL IaaS|Aby klienci nie muszą dostosować system operacyjny lub serwera bazy danych, jak również klientów określone wymagania dotyczące uruchamiania aplikacji innych firm siebie z programu SQL Server (w tej samej maszyny Wirtualnej), zaproponować maszyn wirtualnych SQL / IaaS jako najlepszego rozwiązania|
|||

## <a name="how-to-programmatically-identify-a-managed-instance"></a>Jak programowo identyfikować wystąpienia zarządzanego

W poniższej tabeli przedstawiono kilka właściwości, za pośrednictwem języka Transact SQL, można użyć do wykrywania, czy aplikacja działa przy użyciu wystąpienia zarządzanego i pobrać ważne właściwości.

|Właściwość|Wartość|Komentarz|
|---|---|---|
|`@@VERSION`|Program Microsoft SQL Azure (RTM) — 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ta wartość jest ten sam, jak bazy danych SQL.|
|`SERVERPROPERTY ('Edition')`|Usługi SQL Azure|Ta wartość jest ten sam, jak bazy danych SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Ta wartość jest jednoznacznie identyfikuje wystąpienie zarządzane.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nazwa DNS pełnego wystąpienia w następującym formacie:<instanceName>.<dnsPrefix>. Database.Windows.NET, gdzie <instanceName> jest nazwa podana przez klienta, podczas gdy <dnsPrefix> jest generowane automatycznie na częścią nazwy, gwarantujących globalnego unikatowość nazwy DNS ("wcus17662feb9ce98", na przykład)|Example: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>Najważniejsze funkcje i możliwości wystąpienia zarządzanego 

> [!IMPORTANT]
> Wystąpienie zarządzane jest uruchamiany z wszystkich funkcji najnowszą wersję programu SQL Server, w tym operacje online, plan automatyczne poprawki i inne ulepszenia wydajności enterprise. 

| **Korzyści PaaS** | **Ciągłość działalności biznesowej:** |
| --- | --- |
|Zakupu sprzętu i zarządzania <br>Brak zarządzania w czasie zarządzania podstawową infrastrukturą <br>Szybka aprowizacja i skalowanie usług <br>Automatyczne stosowanie poprawek i wersja uaktualnienia <br>Integracja z innymi usługami danych PaaS |dostępność przez 99,99% umowę SLA  <br>Wbudowana wysoka dostępność <br>Dane są chronione za pomocą automatycznych kopii zapasowych <br>Okres przechowywania kopii zapasowych można skonfigurować klienta (stałe do 7 dni w publicznej wersji zapoznawczej) <br>Kopii zapasowych zainicjowanych przez użytkownika <br>Przywracanie do punktu w czasie w bazie danych możliwości |
|**Zabezpieczenia i zgodność** | **Zarządzanie**|
|Środowisko izolowane (Integracja z siecią wirtualną, Usługa pojedynczej dzierżawy, dedykowanych Obliczanie i magazynowanie) <br>Transparent Data Encryption<br>Uwierzytelnianie usługi Azure AD, obsługę rejestracji jednokrotnej <br>Zgodnego ze standardami zgodności takie same jak Azure SQL database <br>Inspekcja SQL <br>Wykrywanie zagrożeń |Interfejs API Azure Resource Manager do automatyzowania usługi aprowizacja i skalowanie <br>Funkcjonalność portalu platformy Azure dla usługi ręczna aprowizacja i skalowanie <br>Data Migration Service 

![Logowanie jednokrotne](./media/sql-database-managed-instance/sso.png) 

## <a name="vcore-based-purchasing-model-preview"></a>model zakupu opartego na rdzeniach wirtualnych (wersja zapoznawcza)

Oparty na rdzeniach wirtualnych model zakupu (wersja zapoznawcza) zapewnia elastyczność możesz, kontrola, przejrzystości i prostą metodę tłumaczenia wymagań obciążenia w chmurze lokalnie. Ten model umożliwia skalowanie zasobów obliczeniowych, pamięci i magazynu, w zależności od ich potrzeb obciążenia. Model rdzenia wirtualnego jest również kwalifikuje się do 30 procent oszczędności w przypadku [korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Rdzeń wirtualny reprezentuje logiczny Procesor CPU z opcją wyboru generacji sprzętu.
- Logiczne procesory CPU 4. generacji wykorzystują procesory Intel E5-2673 v3 (Haswell) z zegarem 2,4 GHz.
- Logiczne procesory CPU 5 generacji wykorzystują procesory Intel E5-2673 v4 (broadwell z zegarem) 2,3 GHz.

Poniższa tabela pomoże Ci zrozumieć, jak wybrać optymalną konfigurację wystąpień obliczeniowych, pamięci, magazynu i zasoby we/wy.

||4. generacja|5. generacja|
|----|------|-----|
|Sprzęt|Intel E5-2673 v3 (Haswell) procesorów 2,4 GHz, dołączone dyski SSD — rdzeń wirtualny = 1 PP (fizycznych rdzeni)|Intel E5-2673 v4 (broadwell z zegarem) 2,3 GHz procesorów, szybkie eNVM dyski SSD, — rdzeń wirtualny = LP 1 (hyper wątek)|
|Poziomy wydajności|8, 16, 24 rdzenie wirtualne|8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych|
|Memory (Pamięć)|7 GB na rdzeń wirtualny|5.5 GB na rdzeń wirtualny|
||||

## <a name="managed-instance-service-tiers"></a>Zarządzane wystąpienie warstwy usług

Wystąpienie zarządzane jest dostępna w dwóch warstwach usługi:
- **Ogólnego przeznaczenia**: przeznaczone dla aplikacji przy użyciu typowego dostępności i typowe wymagania dotyczące opóźnień we/wy.
- **Krytyczne dla działania firmy**: przeznaczone dla aplikacji o wysokiej dostępności i niskie wymagania dotyczące opóźnień we/wy.
 
> [!IMPORTANT]
> Zmiana warstwie usługi, z ogólnego przeznaczenia na krytyczne dla działania firmy lub odwrotnie nie jest obsługiwana w publicznej wersji zapoznawczej. Użytkownik chce migrować swoje bazy danych do wystąpienia w warstwie innej usługi, można tworzyć nowe wystąpienie, Przywróć bazy danych przy użyciu punktu w czasie przywracania z oryginalnego wystąpienia i następnie upuść oryginalne wystąpienie, jeśli nie jest już potrzebna. 

### <a name="general-purpose-service-tier"></a>Warstwy usług w usłudze ogólnego przeznaczenia

Na poniższej liście opisano kluczowe cechy charakterystyczne dla warstwy usług ogólnego przeznaczenia: 

- Projektowanie dla większości aplikacji biznesowych z typowym wydajności i wymaganiami wysokiej dostępności 
- Magazyn Azure w wersji Premium o wysokiej wydajności (8 TB) 
- 100 baz danych / wystąpienie 

W tej warstwie możesz niezależnie Wybieranie magazynu i moc obliczeniową. 

Na poniższym diagramie przedstawiono active obliczeniowych i nadmiarowych węzłów w tej warstwie usługi.
 
![Warstwy usług w usłudze ogólnego przeznaczenia](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

Poniższa lista zawiera opis kluczowych charakterystyk warstwy usług ogólnego przeznaczenia:

|Cecha | Opis|
|---|---|
| Liczba rdzeni wirtualnych * | 8, 16, 24 (gen 4)<br>8, 16, 24, 32, 40, 64, 80 (gen 5)|
| Wersja programu SQL Server / build | Program SQL Server najnowsza wersja (dostępne) |
| Minimalny rozmiar magazynu | 32 GB |
| Maksymalny rozmiar magazynu | 8 TB |
| Maksymalny rozmiar magazynu na bazę danych | Określony przez rozmiar maksymalnego rozmiaru magazynu dla każdego wystąpienia |
| Oczekiwany magazynu, operacje We/Wy | 500-7500 IOPS na plik danych (w zależności od danych plików). Zobacz [magazynu w warstwie Premium](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| Liczba plików danych (wiersze) na bazę danych | Wiele | 
| Liczba plików dziennika (dziennik) na bazę danych | 1 | 
| Zarządzane automatycznych kopii zapasowych | Yes |
| WYSOKA DOSTĘPNOŚĆ | Na podstawie magazynu zdalnego i [usługi Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Wbudowane wystąpienia i bazy danych monitorowania i metryki | Yes |
| Automatyczne stosowanie poprawek | Yes |
| Sieć wirtualna - wdrożenia usługi Azure Resource Manager | Yes |
| Sieć wirtualna - klasycznego modelu wdrażania | Nie |
| Portal pomocy technicznej | Yes|
|||

\* Rdzeń wirtualny reprezentuje logiczny Procesor CPU z opcją wyboru generacji sprzętu. Logiczne procesory CPU 4 generacji wykorzystują procesory Intel E5-2673 v3 (Haswell) 2,4 GHz, a logiczne procesory CPU 5 ogólnego są oparte na Intel E5-2673 v4 (broadwell z zegarem) 2,3 GHz. 

### <a name="business-critical-service-tier"></a>Warstwy usług krytycznych biznesowych

Warstwy usług krytycznych firm zaprojektowano pod kątem aplikacji za pomocą wysokie wymagania dotyczące operacji We/Wy. Oferuje ona najwyższą odporność na awarie, korzystając z kilku izolowanych zawsze włączonych replik. Na poniższym diagramie przedstawiono podstawową architekturę tej warstwie usługi:

![Warstwy usług krytycznych biznesowych](./media/sql-database-managed-instance/business-critical-service-tier.png)  

Poniższa lista zawiera opis kluczowych charakterystyk krytyczne dla działania firmy warstwy usług: 
-   Przeznaczona dla aplikacji biznesowych o najwyższej wydajności i wymaganiami wysokiej dostępności 
-   Dołączono superszybkiego magazyn SSD (maksymalnie 1 TB na Gen 4 i maksymalnie 4 TB w Gen 5) — obsługuje maksymalnie 100 baz danych na wystąpienie 

|Cecha | Opis|
|---|---|
| Liczba rdzeni wirtualnych * | 8, 16, 24 (gen 4)<br>8, 16, 24, 32, 40, 64, 80 (gen 5)|
| Wersja programu SQL Server / build | Program SQL Server najnowsza wersja (dostępne) |
| Dodatkowe funkcje | [Przetwarzanie OLTP danych w pamięci](sql-database-in-memory.md)<br> 1 dodatkowe repliki tylko do odczytu ([odczytu skalowalnego w poziomie](sql-database-read-scale-out.md))
| Minimalny rozmiar magazynu | 32 GB |
| Maksymalny rozmiar magazynu | Gen 4: 1 TB (wszystkich rdzeniach wirtualnych rozmiarów<br> 5. generacji:<ul><li>1 TB, 8, 16 rdzeni wirtualnych</li><li>2 TB dla 24 rdzenie wirtualne</li><li>4 TB, 40, 60, 80 rdzeni wirtualnych</ul>|
| Maksymalny rozmiar magazynu na bazę danych | Określony przez rozmiar maksymalnego rozmiaru magazynu dla każdego wystąpienia |
| Liczba plików danych (wiersze) na bazę danych | Wiele | 
| Liczba plików dziennika (dziennik) na bazę danych | 1 | 
| Zarządzane automatycznych kopii zapasowych | Yes |
| WYSOKA DOSTĘPNOŚĆ | Na podstawie [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) i [usługi Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Wbudowane wystąpienia i bazy danych monitorowania i metryki | Yes |
| Automatyczne stosowanie poprawek | Yes |
| Sieć wirtualna - wdrożenia usługi Azure Resource Manager | Yes |
| Sieć wirtualna - klasycznego modelu wdrażania | Nie |
| Portal pomocy technicznej | Yes|
|||

## <a name="advanced-security-and-compliance"></a>Zaawansowane zabezpieczenia i zgodność 

### <a name="managed-instance-security-isolation"></a>Zarządzane izolacji zabezpieczeń wystąpienia 

Wystąpienie zarządzane zapewniają dodatkowe zabezpieczenia izolację od pozostałych dzierżawców w chmurze platformy Azure. Izolacja na potrzeby zabezpieczeń obejmują: 

- [Implementacja natywnych sieci wirtualnej](sql-database-managed-instance-vnet-configuration.md) i łączności do środowiska lokalnego przy użyciu usługi Azure Expressroute lub bram sieci VPN 
- Punkt końcowy SQL jest uwidaczniany wyłącznie za pośrednictwem prywatnego adresu IP, umożliwiając bezpieczne połączenie z prywatnej platformy Azure lub sieci hybrydowe
- Jednej dzierżawy za pomocą dedykowanego podstawowej infrastruktury (moc obliczeniowa, Magazyn)

Poniższy diagram przedstawia różne opcje łączności dla aplikacji: 

![Wysoka dostępność](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Aby dowiedzieć się więcej na temat integracji sieci wirtualnej i sieci enforcements zasad na poziomie podsieci, zobacz [Konfigurowanie sieci wirtualnej dla wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance-vnet-configuration.md) i [łączenia aplikacji z usługi Azure SQL Database Wystąpienie zarządzane](sql-database-managed-instance-connect-app.md). 

> [!IMPORTANT]
> W tej samej podsieci, należy umieścić wiele wystąpienia zarządzanego, wszędzie tam, gdzie, jest dozwolona przez Twoje wymagania dotyczące bezpieczeństwa, ponieważ, zostaną wyświetlone dodatkowe korzyści. Collocating wystąpień w tej samej podsieci spowoduje znacznie upraszcza konserwację infrastruktury sieci i zmniejszyć czas, obsługi administracyjnej, ponieważ aprowizacja długi czas trwania jest skojarzony z kosztów wdrażania pierwszego wystąpienia zarządzanego w podsieci wystąpienia.


### <a name="auditing-for-compliance-and-security"></a>Inspekcja zgodności i zabezpieczeń 

[Zarządzane wystąpienia inspekcji](sql-database-managed-instance-auditing.md) śledzi zdarzenia bazy danych i zapisuje je do inspekcji logowania na koncie magazynu platformy Azure. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń. 

### <a name="data-encryption-in-motion"></a>Szyfrowanie danych w ruchu 

Wystąpienie zarządzane zabezpiecza dane, udostępniając szyfrowanie danych w ruchu przy użyciu Transport Layer Security.

Oprócz zabezpieczeń warstwy transportu, wystąpienie zarządzane usługi SQL Database oferuje ochronę danych poufnych w locie, podczas przechowywania i podczas przetwarzania zapytań przy użyciu [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Funkcja Always Encrypted to pierwsze w branży rozwiązanie, które oferuje bezkonkurencyjne zabezpieczenie danych przed naruszeniami, w tym przed kradzieżą danych o kluczowym znaczeniu. Na przykład z funkcją Always Encrypted numery kart kredytowych są przechowywane w bazie danych zawsze, nawet podczas przetwarzania zapytania, z możliwością odszyfrowania w punkcie użycia przez autoryzowany personel lub przez aplikacje wymagające przetwarzania tych danych. 

### <a name="data-encryption-at-rest"></a>Szyfrowanie danych w spoczynku 
[Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) szyfruje wystąpienia zarządzanego Azure SQL, pliki danych, nazywane szyfrowanie danych magazynowanych. Funkcja TDE wykonuje w czasie rzeczywistym operacji We/Wy szyfrowania i odszyfrowywania plików danych i dziennika. Szyfrowanie używa klucza szyfrowania bazy danych (klucz szyfrowania danych), który jest przechowywany w rekordzie rozruchowym bazy danych dostępności podczas odzyskiwania. Umożliwia ochronę wszystkich baz danych w wystąpieniu zarządzanym za pomocą technologii transparent data encryption. Funkcja TDE jest sprawdzoną w SQL technologią szyfrowania danych w spoczynku, która przez wiele standardów zgodności jest wymagana do ochrony przed kradzieżą nośników magazynowania. W publicznej wersji zapoznawczej model automatyczne zarządzanie kluczami jest obsługiwane (wykonywane przez platformy PaaS). 

Migracja szyfrowanej bazy danych do wystąpienia zarządzanego SQL jest obsługiwana za pomocą usługi Azure Database Migration Service (DMS) lub native przywracania. Jeśli planujesz migrację szyfrowanej bazy danych przy użyciu funkcji przywracania natywnych migracji istniejącego certyfikatu TDE z programu SQL Server w środowisku lokalnym lub maszyny Wirtualnej programu SQL Server do wystąpienia zarządzanego jest to krok wymagany. Aby uzyskać więcej informacji na temat opcji migracji, zobacz [migracja wystąpienia programu SQL Server do wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance-migrate.md).

### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych 

Baza danych SQL [dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking) ogranicza ujawnianie poufnych danych przez ich maskowanie nieuprzywilejowanym użytkownikom. Dynamiczne maskowanie danych pomaga zapobiec nieautoryzowanemu dostępowi do danych poufnych, umożliwiając wyznaczenie, jaka część danych poufnych może zostać odsłonięta, przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian. 

### <a name="row-level-security"></a>Zabezpieczenia na poziomie wiersza 

[Zabezpieczenia](/sql/relational-databases/security/row-level-security) umożliwia kontrolę dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (takich jak przez grupy członkostwa lub kontekstu wykonania). Zabezpieczenia na poziomie wiersza (RLS, Row-Level Security) upraszczają projektowanie i kodowanie zabezpieczeń w aplikacji. Zabezpieczenia na poziomie wiersza umożliwiają zaimplementowanie ograniczeń w dostępie do wiersza danych. Na przykład zapewnienie, że pracownicy mają dostęp tylko wiersze danych, które są odpowiednie do ich działu, lub ograniczenie dostępu do danych do odpowiednich danych. 

### <a name="threat-detection"></a>Wykrywanie zagrożeń 

[Wykrywanie zagrożeń wystąpienia zarządzanego](sql-database-managed-instance-threat-detection.md) uzupełnia [inspekcji wystąpienia zarządzanego](sql-database-managed-instance-auditing.md) , zapewniając dodatkową warstwę analizy zabezpieczeń wbudowaną w usługę, która wykrywa nietypowe i potencjalnie szkodliwe próby dostęp do lub wykorzystania baz danych. Alerty o podejrzanych działań, potencjalnych lukach w zabezpieczeniach i ataki wstrzyknięcie kodu SQL, a także bazy danych nietypowe wzorce dostępu. Alerty wykrywania zagrożeń, mogą być wyświetlane z [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/) zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym 

Usługa SQL Database umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft dzięki funkcji [integracji usługi Azure Active Directory](sql-database-aad-authentication.md). Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Usługa Azure Active Directory obsługuje [uwierzytelnianie wieloskładnikowe](sql-database-ssms-mfa-authentication-configure.md) (MFA, Multi-Factor Authentication) w celu zwiększenia bezpieczeństwa danych i aplikacji, korzystając z procesu jednokrotnego logowania. 

### <a name="authentication"></a>Authentication 
Uwierzytelnianie bazy danych SQL odnosi się do sposobu użytkowników potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:  

- Uwierzytelnianie SQL, które używa nazwy użytkownika i hasła.
- Usługa Azure uwierzytelnianie usługi Active Directory, która korzysta z tożsamości zarządzanej przez usługę Azure Active Directory i jest obsługiwana w przypadku zarządzanych i zintegrowanych domen. 

### <a name="authorization"></a>Autoryzacja

Autoryzacja Określa, co można zrobić w ramach usługi Azure SQL Database i jest kontrolowana przez członkostw roli bazy danych konta użytkownika i uprawnienia na poziomie obiektu. Wystąpienie zarządzane ma te same możliwości autoryzacji, co program SQL Server 2017. 

## <a name="database-migration"></a>Migracja bazy danych 

Zarządzane wystąpienia obiektów docelowych scenariusze dotyczące użytkowników za pomocą migracji bazy danych w pamięci masowej z wdrożenia lokalne czy implementacje bazy danych IaaS. Zarządzane wystąpienie obsługuje kilka opcji migracji bazy danych: 

### <a name="data-migration-service"></a>Data Migration Service

Azure Database Migration Service to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemową migrację z wielu źródłowych baz danych na danych na platformę Azure przy minimalnych przestojach. Ta usługa usprawnia zadania wymagane do przenoszenia istniejących innych firm i baz danych programu SQL Server na platformie Azure. Opcje wdrażania obejmują usługi Azure SQL Database, wystąpienia zarządzanego i programu SQL Server w maszynie Wirtualnej platformy Azure w publicznej wersji zapoznawczej. Zobacz [jak przeprowadzić migrację z lokalnej bazy danych do wystąpienia zarządzanego przy użyciu usługi DMS](https://aka.ms/migratetoMIusingDMS). 

### <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie  

Podejście do migracji wykorzystuje kopii zapasowych SQL w usłudze Azure blob storage. Kopie zapasowe przechowywane w obiekcie blob usługi Azure storage można przywrócić bezpośrednio do wystąpienia zarządzanego. Aby przywrócić istniejącą bazę danych SQL do wystąpienia zarządzanego, możesz wykonywać następujące czynności:

- Użyj [Data Migration Service (DMS)](../dms/dms-overview.md). Aby zapoznać się z samouczkiem, zobacz [migracji do wystąpienia zarządzanego przy użyciu usługi Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) do przywrócenia z pliku kopii zapasowej bazy danych
- Użyj [polecenia języka T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql). 
  - Samouczek przedstawiający sposób przywracania Wide World Importers — Standard pliku kopii zapasowej można znaleźć [przywrócić pliku kopii zapasowej do wystąpienia zarządzanego](sql-database-managed-instance-restore-from-backup-tutorial.md). Ten samouczek pokazuje, że trzeba przekazać plik kopii zapasowej do magazynu w blogu dotyczącym platformy Azure i bezpieczne, za pomocą klucza podpisu (SAS) dostępu współdzielonego.
  - Aby uzyskać informacji na temat przywracania z adresu URL, zobacz [natywnych przywracania z adresu URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

## <a name="sql-features-supported"></a>Funkcji SQL, które są obsługiwane 

Zarządzane wystąpienia ma na celu dostarczać blisko powierzchni 100% zgodności z lokalnego programu SQL Server zostaną dodane w etapach, aż do ogólnej dostępności usługi. Dla funkcji i listy porównanie, zobacz [typowe funkcje SQL](sql-database-features.md).
 
Zarządzane wystąpienie obsługuje zgodności z poprzednimi wersjami z bazami danych SQL 2008. Bezpośrednie migracja z serwerami baz danych SQL 2005 jest obsługiwana, poziom zgodności dla migrowanych baz danych SQL 2005 zostały zaktualizowane pod kątem programu SQL 2008. 
 
Poniższy diagram przedstawia zgodność powierzchni w wystąpieniu zarządzanym usługi:  

![Migracja](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Podstawowe różnice między lokalnego programu SQL Server i wystąpienia zarządzanego 

Korzyści z wystąpienia zarządzanego nie zawsze w górę — od początku w chmurze, która oznacza, że niektóre funkcje programu SQL Server w środowisku lokalnym mogą być nieaktualne, wycofania lub mieć alternatyw. Istnieją szczególnych przypadkach, gdy konieczne rozpoznaje, że dana funkcja działa w sposób nieco lub usługa nie jest uruchomiona w środowisku, które nie mają pełnej kontroli nad narzędzi: 

- Wysoka dostępność jest wbudowane i wstępnie skonfigurowane. Zawsze włączone funkcje wysokiej dostępności nie są widoczne w taki sam sposób jak w implementacji SQL IaaS 
- Automatyczne kopie zapasowe i punktu w czasie przywracania. Klient może zainicjować `copy-only` kopie zapasowe, które nie kolidują z automatycznego łańcuch kopii zapasowych. 
- Wystąpienie zarządzane nie zezwala na określanie ścieżek Pełna fizyczna, dzięki czemu wszystkie scenariusze odpowiedniego mają być obsługiwane inaczej: Przywracanie bazy danych nie obsługuje opcji WITH MOVE, tworzenie bazy danych nie zezwala na ścieżki fizyczne, BULK INSERT współpracuje z obiektów blob platformy Azure, tylko itp. 
- Zarządzane wystąpienie obsługuje [uwierzytelniania usługi Azure AD](sql-database-aad-authentication.md) chmury alternatywę dla uwierzytelniania Windows. 
- Wystąpienie zarządzane automatycznie zarządza XTP plików i plików baz danych zawierających obiekty OLTP w pamięci
- Wystąpienie zarządzane obsługuje usługi SQL Server Integration Services (SSIS) i mogą hosta wykazu usług SSIS (SSISDB), która przechowuje pakietów usług SSIS, ale są one wykonywane na zarządzanego środowiska Azure-SSIS Integration Runtime (IR) w usłudze Azure Data Factory (ADF), zobacz [Create Azure-SSIS IR w usłudze ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). Aby porównać funkcji SSIS w bazie danych SQL i wystąpienia zarządzanego, zobacz [Porównaj bazy danych SQL i wystąpienia zarządzanego (wersja zapoznawcza)](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview).

### <a name="managed-instance-administration-features"></a>Zarządzane funkcji administrowania wystąpienia  

Zarządzane wystąpienia Włącz administratorowi systemu skupić się na co najistotniejsze dla firm. Wiele działań administratora/administrator systemu nie są wymagane lub są one proste. Na przykład system operacyjny / RDBMS instalacji i wdrażania poprawek, dynamiczne wystąpienia zmiany rozmiaru i konfiguracji, tworzenia kopii zapasowych, replikacja bazy danych (w tym systemowych baz danych), konfiguracja wysokiej dostępności i konfigurację danych monitorowania kondycji i wydajności strumieni. 

> [!IMPORTANT]
> Aby uzyskać listę obsługiwanych, częściowo obsługiwane i nieobsługiwane funkcje, zobacz [funkcji usługi SQL Database](sql-database-features.md). Aby uzyskać listę różnic języka T-SQL w wystąpieniach zarządzanych w stosunku do programu SQL Server, zobacz [różnice języka T-SQL wystąpienia zarządzanego z programu SQL Server](sql-database-managed-instance-transact-sql-information.md)
 
## <a name="next-steps"></a>Kolejne kroki

- Dla funkcji i listy porównanie, zobacz [typowe funkcje SQL](sql-database-features.md).
- Aby uzyskać więcej informacji o konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-vnet-configuration.md).
- Aby uzyskać samouczek, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [utworzysz wystąpienie zarządzane](sql-database-managed-instance-create-tutorial-portal.md).
- Aby skorzystać z samouczka w zakresie używania usługi Azure Database Migration Service (DMS) do celów migracji, zobacz [Migracja wystąpień zarządzanych przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać informacje o cenach, zobacz [wystąpienie zarządzane usługi SQL Database, cennik](https://azure.microsoft.com/pricing/details/sql-database/managed/).
