---
title: Usługa Azure SQL Database Managed Przegląd instancji | Dokumentacja firmy Microsoft
description: W tym temacie opisano wystąpienia usługi Azure SQL Database Managed oraz wyjaśniono, jak to działa, i jak jest inny niż pojedynczej bazy danych w usłudze Azure SQL Database.
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/30/2018
ms.author: bonova
ms.openlocfilehash: 5caafdfbaca1a701d2728afdfcb31e8e73b4fded
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050310"
---
# <a name="what-is-a-managed-instance-preview"></a>Co to jest wystąpienie zarządzane (wersja zapoznawcza)?

Wystąpienie usługi Azure SQL Database Managed (wersja zapoznawcza) jest nowy model wdrażania usługi Azure SQL Database, zapewniając prawie 100% utrzymywania zgodności z najnowszą programu SQL Server w środowisku lokalnym aparatu bazy danych (Enterprise Edition), zapewniających macierzystej [(sieć wirtualna Sieć wirtualna)](../virtual-network/virtual-networks-overview.md) implementacji, odnoszący się do typowe problemy dotyczące zabezpieczeń i [modelu biznesowego](https://azure.microsoft.com/pricing/details/sql-database/) korzystna dla klientów programu SQL Server w środowisku lokalnym. Wystąpienie zarządzane umożliwia istniejących klientów programu SQL Server do lift- and -shift ich aplikacji lokalnych do chmury przy minimalnych zmianach aplikacji i baz danych. W tym samym czasie, wystąpienie zarządzane zachowuje wszystkie możliwości PaaS (Aktualizacje automatyczne stosowanie poprawek i wersja, [automatyczne kopie zapasowe](sql-database-automated-backups.md), [wysokiej dostępności](sql-database-high-availability.md) ), która znacząco zmniejsza obciążenie zarządzania a całkowity koszt posiadania.

> [!IMPORTANT]
> Listę regionów, w których jest obecnie dostępne wystąpienie zarządzane, można znaleźć w temacie [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance (Migrowanie baz danych do w pełni zarządzanej usługi za pomocą wystąpienia zarządzanego usługi Azure SQL Database)](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/).
 
Poniższy diagram przedstawia kluczowe funkcje wystąpienia zarządzanego:

![Najważniejsze funkcje](./media/sql-database-managed-instance/key-features.png)

Wystąpienie usługi Azure SQL Database Managed zaprojektowano z myślą klienci, którzy chcą migracji dużej liczby aplikacji z lokalną lub IaaS, opracowany własny, lub niezależnego dostawcy oprogramowania wyposażone w środowisku, aby w pełni zarządzanym środowisku chmury PaaS jako niski migracji nakład pracy, jak to możliwe. Przy użyciu w pełni zautomatyzowane [Data Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) na platformie Azure, klienci mogą lift- and shift ich na lokalnym serwerze SQL Server do wystąpienia zarządzanego, który zapewnia zgodność z programu SQL Server w środowisku lokalnym i pełną izolację pamięci wystąpienia klientów z natywną obsługą sieci Wirtualnej.  Z pakietem Software Assurance mogą wymieniać swoich istniejących licencji do korzystania z taryf rabatowych na wystąpieniu zarządzanym bazy danych SQL przy użyciu [korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Wystąpienie zarządzane usługi SQL Database to najlepsze miejsce docelowe migracji w chmurze dla wystąpień programu SQL Server, które wymagają wysokiego poziomu zabezpieczeń i powierzchni sformatowanego programowania. 

Wg ogólnej dostępności wystąpienia zarządzanego ma na celu dostarczać blisko powierzchni 100% zgodności z najnowszą wersją programu SQL Server w środowisku lokalnym za pośrednictwem planu wersji etapowe. 

Podjęcie decyzji, między pojedynczej bazy danych Azure SQL Database, wystąpienia zarządzanego Azure SQL Database i SQL Server IaaS hostowanych maszyn wirtualnych, zobacz [sposobu wybierania właściwej wersji programu SQL Server w chmurze platformy Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Najważniejsze funkcje i możliwości 

Wystąpienie usługi Azure SQL Database Managed łączy najlepsze funkcje, które są dostępne zarówno w usłudze Azure SQL Database i aparatu bazy danych programu SQL Server.

> [!IMPORTANT]
> Wystąpienie zarządzane jest uruchamiany z wszystkich funkcji najnowszą wersję programu SQL Server, w tym operacje online, plan automatyczne poprawki i inne ulepszenia wydajności enterprise. 

| **Korzyści PaaS** | **Ciągłość działalności biznesowej:** |
| --- | --- |
|Zakupu sprzętu i zarządzania <br>Brak zarządzania w czasie zarządzania podstawową infrastrukturą <br>Szybka aprowizacja i skalowanie usług <br>Automatyczne stosowanie poprawek i wersja uaktualnienia <br>Integracja z innymi usługami danych PaaS |dostępność przez 99,99% umowę SLA  <br>Wbudowane [wysokiej dostępności](sql-database-high-availability.md) <br>Dane są chronione za pomocą [automatyczne kopie zapasowe](sql-database-automated-backups.md) <br>Okres przechowywania kopii zapasowych można skonfigurować klienta (stałe do 7 dni w publicznej wersji zapoznawczej) <br>Użytkownik zainicjował [kopii zapasowych](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Punkt w czasie przywracania bazy danych](sql-database-recovery-using-backups.md#point-in-time-restore) możliwości |
|**Zabezpieczenia i zgodność** | **Zarządzanie**|
|Środowisko izolowane ([Integracja z siecią wirtualną](sql-database-managed-instance-vnet-configuration.md), pojedynczej dzierżawy usługi, dedykowanych obliczeń i magazynowania) <br>[Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Uwierzytelnianie usługi Azure AD](sql-database-aad-authentication.md), pojedynczy Obsługa logowania jednokrotnego <br>Zgodnego ze standardami zgodności takie same jak Azure SQL database <br>[Inspekcja SQL](sql-database-managed-instance-auditing.md) <br>[Wykrywanie zagrożeń](sql-database-managed-instance-threat-detection.md) |Interfejs API Azure Resource Manager do automatyzowania usługi aprowizacja i skalowanie <br>Funkcjonalność portalu platformy Azure dla usługi ręczna aprowizacja i skalowanie <br>Data Migration Service 

## <a name="vcore-based-purchasing-model"></a>model zakupu w oparciu o rdzeń wirtualny

[Modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) zapewnia elastyczność, kontrola, przejrzystości i prostą metodę tłumaczenia wymagań dotyczących obciążenia lokalne do chmury. Ten model umożliwia skalowanie zasobów obliczeniowych, pamięci i magazynu, w zależności od ich potrzeb obciążenia. Model rdzenia wirtualnego jest również kwalifikuje się do 30 procent oszczędności w przypadku [korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

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
- **Ogólnego przeznaczenia**: przeznaczone dla aplikacji za pomocą wydajności typowe wymagania dotyczące opóźnień we/wy.
- **Krytyczne dla działania firmy**: przeznaczone dla aplikacji za pomocą niskie wymagania dotyczące opóźnień We/Wy i minimalny wpływ podstawowych operacji konserwacji na obciążenie pracą.

Obie warstwy usług gwarantuje dostępność na poziomie 99,99% i pozwalają na niezależne wybierz rozmiar magazynu i moc obliczeniową. Aby uzyskać więcej informacji na temat architektury wysokiej dostępności usługi Azure SQL Database, zobacz [wysokiej dostępności i Azure SQL Database](sql-database-high-availability.md).

> [!IMPORTANT]
> Zmiana warstwie usługi, z ogólnego przeznaczenia na krytyczne dla działania firmy lub odwrotnie nie jest obsługiwana w publicznej wersji zapoznawczej. Użytkownik chce migrować swoje bazy danych do wystąpienia w warstwie innej usługi, można tworzyć nowe wystąpienie, Przywróć bazy danych przy użyciu punktu w czasie przywracania z oryginalnego wystąpienia i następnie upuść oryginalne wystąpienie, jeśli nie jest już potrzebna. 

### <a name="general-purpose-service-tier"></a>Warstwy usług w usłudze ogólnego przeznaczenia

Na poniższej liście opisano kluczowe cechy charakterystyczne dla warstwy usług ogólnego przeznaczenia: 

- Projektowanie pod kątem większość aplikacji biznesowych z wymaganiami dotyczącymi wydajności typowe 
- Magazyn Azure w wersji Premium o wysokiej wydajności (8 TB) 
- 100 baz danych dla każdego wystąpienia 

Poniższa lista zawiera opis kluczowych charakterystyk warstwy usług ogólnego przeznaczenia:

|Cecha | Opis|
|---|---|
| Liczba rdzeni wirtualnych * | 8, 16, 24 (gen 4)<br>8, 16, 24, 32, 40, 64, 80 (gen 5)|
| Wersja programu SQL Server / build | Aparat bazy danych programu SQL Server (Najnowsza wersja stabilna) |
| Minimalny rozmiar magazynu | 32 GB |
| Maksymalny rozmiar magazynu | 8 TB |
| Maksymalny rozmiar magazynu na bazę danych | Określony przez rozmiar maksymalnego rozmiaru magazynu dla każdego wystąpienia |
| Oczekiwany magazynu, operacje We/Wy | 500-7500 IOPS na plik danych (w zależności od danych plików). Zobacz [magazynu w warstwie Premium](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| Liczba plików danych (wiersze) na bazę danych | Wiele | 
| Liczba plików dziennika (dziennik) na bazę danych | 1 | 
| Zarządzane automatycznych kopii zapasowych | Yes |
| WYSOKA DOSTĘPNOŚĆ | Dane przechowywane w usłudze Azure Storage i [usługi Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Wbudowane wystąpienia i bazy danych monitorowania i metryki | Yes |
| Automatyczne stosowanie poprawek | Yes |
| Sieć wirtualna - wdrożenia usługi Azure Resource Manager | Yes |
| Sieć wirtualna - klasycznego modelu wdrażania | Nie |
| Portal pomocy technicznej | Yes|
|||

\* Rdzeń wirtualny reprezentuje logiczny Procesor CPU z opcją wyboru generacji sprzętu. Logiczne procesory CPU 4 generacji wykorzystują procesory Intel E5-2673 v3 (Haswell) 2,4 GHz, a logiczne procesory CPU 5 ogólnego są oparte na Intel E5-2673 v4 (broadwell z zegarem) 2,3 GHz. 

Aby uzyskać więcej informacji, zobacz [dostępności Standard/ogólnego przeznaczenia i architektura](sql-database-high-availability.md#standardgeneral-purpose-availability) w usłudze Azure SQL Database.

### <a name="business-critical-service-tier"></a>Warstwy usług krytycznych biznesowych

Warstwy usług krytycznych firm zaprojektowano pod kątem aplikacji za pomocą wysokie wymagania dotyczące operacji We/Wy. Oferuje ona najwyższą odporność na awarie, korzystając z kilku izolowanych zawsze włączonych replik. 

Poniższa lista zawiera opis kluczowych charakterystyk krytyczne dla działania firmy warstwy usług: 
-   Przeznaczona dla aplikacji biznesowych o najwyższej wydajności i wymaganiami wysokiej dostępności 
-   Dołączono superszybkiego magazyn SSD (maksymalnie 1 TB na Gen 4 i maksymalnie 4 TB w Gen 5)
-   Obsługuje maksymalnie 100 baz danych na wystąpienie 
- Wbudowane dodatkowe tylko do odczytu wystąpienie, które mogą służyć do raportowania i innych obciążeń, tylko do odczytu
- [Przetwarzanie OLTP danych w pamięci](sql-database-in-memory.md) które mogą być używane w przypadku obciążeń z wymogami wysokiej prefrmance  

|Cecha | Opis|
|---|---|
| Liczba rdzeni wirtualnych * | 8, 16, 24, 32 (gen 4)<br>8, 16, 24, 32, 40, 64, 80 (gen 5)|
| Wersja programu SQL Server / build | Program SQL Server najnowsza wersja (dostępne) |
| Dodatkowe funkcje | [Przetwarzanie OLTP danych w pamięci](sql-database-in-memory.md)<br> 1 dodatkowe repliki tylko do odczytu ([odczytu skalowalnego w poziomie](sql-database-read-scale-out.md))
| Minimalny rozmiar magazynu | 32 GB |
| Maksymalny rozmiar magazynu | Gen 4: 1 TB (wszystkie rozmiary generacji — rdzeń wirtualny)<br> 5. generacji:<ul><li>1 TB, 8, 16 rdzeni wirtualnych</li><li>2 TB dla 24 rdzenie wirtualne</li><li>4 TB dla 32, 40, 64, 80 rdzeni wirtualnych</ul>|
| Maksymalny rozmiar magazynu na bazę danych | Określony przez rozmiar maksymalnego rozmiaru magazynu dla każdego wystąpienia |
| Liczba plików danych (wiersze) na bazę danych | Wiele | 
| Liczba plików dziennika (dziennik) na bazę danych | 1 | 
| Zarządzane automatycznych kopii zapasowych | Yes |
| WYSOKA DOSTĘPNOŚĆ | Dane przechowywane na lokalnych dyskach SSD i użyj [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) i [usługi Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Wbudowane wystąpienia i bazy danych monitorowania i metryki | Yes |
| Automatyczne stosowanie poprawek | Yes |
| Sieć wirtualna - wdrożenia usługi Azure Resource Manager | Yes |
| Sieć wirtualna - klasycznego modelu wdrażania | Nie |
| Portal pomocy technicznej | Yes|
|||

Aby uzyskać więcej informacji, zobacz [dostępność w warstwie Premium/krytyczne i architektura](sql-database-high-availability.md#premiumbusiness-critical-availability) w usłudze Azure SQL Database.

## <a name="advanced-security-and-compliance"></a>Zaawansowane zabezpieczenia i zgodność 

Wystąpienie usługi Azure SQL Database Managed łączy zaawansowane funkcje zabezpieczeń udostępnianych przez chmury platformy Azure i aparat bazy danych programu SQL Server. 

### <a name="managed-instance-security-isolation-in-azure-cloud"></a>Zarządzane wystąpienia izolacja na potrzeby zabezpieczeń w chmurze platformy Azure 

Wystąpienie zarządzane zapewniają dodatkowe zabezpieczenia izolację od pozostałych dzierżawców w chmurze platformy Azure. Izolacja na potrzeby zabezpieczeń obejmują: 

- [Implementacja natywnych sieci wirtualnej](sql-database-managed-instance-vnet-configuration.md) i łączności do środowiska lokalnego przy użyciu usługi Azure Expressroute lub bram sieci VPN 
- Punkt końcowy SQL jest uwidaczniany wyłącznie za pośrednictwem prywatnego adresu IP, umożliwiając bezpieczne połączenie z prywatnej platformy Azure lub sieci hybrydowe
- Jednej dzierżawy za pomocą dedykowanego podstawowej infrastruktury (moc obliczeniowa, Magazyn)

Poniższy diagram przedstawia różne opcje łączności dla aplikacji: 

![Wysoka dostępność](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Aby dowiedzieć się więcej na temat integracji sieci wirtualnej i sieci enforcements zasad na poziomie podsieci, zobacz [Konfigurowanie sieci wirtualnej dla wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance-vnet-configuration.md) i [łączenia aplikacji z usługi Azure SQL Database Wystąpienie zarządzane](sql-database-managed-instance-connect-app.md). 

> [!IMPORTANT]
> W tej samej podsieci, należy umieścić wiele wystąpienia zarządzanego, wszędzie tam, gdzie, jest dozwolona przez Twoje wymagania dotyczące bezpieczeństwa, ponieważ, zostaną wyświetlone dodatkowe korzyści. Collocating wystąpień w tej samej podsieci spowoduje znacznie upraszcza konserwację infrastruktury sieci i zmniejszyć czas, obsługi administracyjnej, ponieważ aprowizacja długi czas trwania jest skojarzony z kosztów wdrażania pierwszego wystąpienia zarządzanego w podsieci wystąpienia.

### <a name="azure-sql-database-security-features"></a>Funkcje zabezpieczeń bazy danych Azure SQL

Usługa Azure SQL Database udostępnia zestaw zaawansowanych funkcji zabezpieczeń, które mogą służyć do ochrony danych.

- [Zarządzane wystąpienia inspekcji](sql-database-managed-instance-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w pliku dziennika inspekcji umieścić na koncie magazynu platformy Azure. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń. 
- Szyfrowanie danych w ruchu — wystąpienie zarządzane zabezpiecza dane, udostępniając szyfrowanie danych w ruchu przy użyciu Transport Layer Security. Oprócz zabezpieczeń warstwy transportu, wystąpienie zarządzane usługi SQL Database oferuje ochronę danych poufnych w locie, podczas przechowywania i podczas przetwarzania zapytań przy użyciu [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Funkcja Always Encrypted to pierwsze w branży rozwiązanie, które oferuje bezkonkurencyjne zabezpieczenie danych przed naruszeniami, w tym przed kradzieżą danych o kluczowym znaczeniu. Na przykład z funkcją Always Encrypted numery kart kredytowych są przechowywane w bazie danych zawsze, nawet podczas przetwarzania zapytania, z możliwością odszyfrowania w punkcie użycia przez autoryzowany personel lub przez aplikacje wymagające przetwarzania tych danych. 
- [Wykrywanie zagrożeń](sql-database-managed-instance-threat-detection.md) uzupełnia [inspekcji wystąpienia zarządzanego](sql-database-managed-instance-auditing.md) , zapewniając dodatkową warstwę zabezpieczeń analizy wbudowana w usługę, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystać bazy danych. Alerty o podejrzanych działań, potencjalnych lukach w zabezpieczeniach i ataki wstrzyknięcie kodu SQL, a także bazy danych nietypowe wzorce dostępu. Alerty wykrywania zagrożeń, mogą być wyświetlane z [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/) zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.  
- [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking) ogranicza ujawnianie poufnych danych przez ich maskowanie nieuprzywilejowanym użytkownikom. Dynamiczne maskowanie danych pomaga zapobiec nieautoryzowanemu dostępowi do danych poufnych, umożliwiając wyznaczenie, jaka część danych poufnych może zostać odsłonięta, przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian. 
- [Zabezpieczenia](/sql/relational-databases/security/row-level-security) umożliwia kontrolę dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (takich jak przez grupy członkostwa lub kontekstu wykonania). Zabezpieczenia na poziomie wiersza (RLS, Row-Level Security) upraszczają projektowanie i kodowanie zabezpieczeń w aplikacji. Zabezpieczenia na poziomie wiersza umożliwiają zaimplementowanie ograniczeń w dostępie do wiersza danych. Na przykład zapewnienie, że pracownicy mają dostęp tylko wiersze danych, które są odpowiednie do ich działu, lub ograniczenie dostępu do danych do odpowiednich danych. 
- [Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) szyfruje wystąpienia zarządzanego Azure SQL, pliki danych, nazywane szyfrowanie danych magazynowanych. Funkcja TDE wykonuje w czasie rzeczywistym operacji We/Wy szyfrowania i odszyfrowywania plików danych i dziennika. Szyfrowanie używa klucza szyfrowania bazy danych (klucz szyfrowania danych), który jest przechowywany w rekordzie rozruchowym bazy danych dostępności podczas odzyskiwania. Umożliwia ochronę wszystkich baz danych w wystąpieniu zarządzanym za pomocą technologii transparent data encryption. Funkcja TDE jest sprawdzoną w SQL technologią szyfrowania danych w spoczynku, która przez wiele standardów zgodności jest wymagana do ochrony przed kradzieżą nośników magazynowania. W publicznej wersji zapoznawczej model automatyczne zarządzanie kluczami jest obsługiwane (wykonywane przez platformy PaaS). 

Migracja szyfrowanej bazy danych do wystąpienia zarządzanego SQL jest obsługiwana za pomocą usługi Azure Database Migration Service (DMS) lub native przywracania. Jeśli planujesz migrację szyfrowanej bazy danych przy użyciu funkcji przywracania natywnych migracji istniejącego certyfikatu TDE z programu SQL Server w środowisku lokalnym lub maszyny Wirtualnej programu SQL Server do wystąpienia zarządzanego jest to krok wymagany. Aby uzyskać więcej informacji na temat opcji migracji, zobacz [migracja wystąpienia programu SQL Server do wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integracja z usługą Azure Active Directory

Wystąpienie usługi Azure SQL Database Managed obsługuje tradycyjne logowania aparatu bazy danych programu SQL server i nazwami logowania, zintegrowane za pomocą usługi Azure Active Directory (AAD). Logowania do usługi AAD jest wersja chmury platformy Azure Windows logowania do bazy danych używane w środowisku w środowisku lokalnym.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym 

Wystąpienie zarządzane umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft, za pomocą [integracji usługi Azure Active Directory](sql-database-aad-authentication.md). Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Usługa Azure Active Directory obsługuje [uwierzytelnianie wieloskładnikowe](sql-database-ssms-mfa-authentication-configure.md) (MFA, Multi-Factor Authentication) w celu zwiększenia bezpieczeństwa danych i aplikacji, korzystając z procesu jednokrotnego logowania. 

### <a name="authentication"></a>Authentication 
Zarządzane wystąpienia uwierzytelniania, który odwołuje się do sposobu użytkowników potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:  

- Uwierzytelnianie SQL, które używa nazwy użytkownika i hasła.
- Usługa Azure uwierzytelnianie usługi Active Directory, która korzysta z tożsamości zarządzanej przez usługę Azure Active Directory i jest obsługiwana w przypadku zarządzanych i zintegrowanych domen. 

### <a name="authorization"></a>Autoryzacja

Autoryzacja Określa, co można zrobić w ramach usługi Azure SQL Database i jest kontrolowana przez członkostw roli bazy danych konta użytkownika i uprawnienia na poziomie obiektu. Wystąpienie zarządzane ma te same możliwości autoryzacji, co program SQL Server 2017. 

## <a name="database-migration"></a>Migracja bazy danych 

Zarządzane wystąpienia obiektów docelowych scenariusze dotyczące użytkowników za pomocą migracji bazy danych w pamięci masowej z wdrożenia lokalne czy implementacje bazy danych IaaS. Zarządzane wystąpienie obsługuje kilka opcji migracji bazy danych: 

### <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie  

Podejście do migracji wykorzystuje kopii zapasowych SQL w usłudze Azure blob storage. Kopie zapasowe przechowywane w obiekcie blob usługi Azure storage może bezpośrednio przywrócony do wystąpienia zarządzanego przy użyciu [polecenia języka T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current). 
  - Samouczek przedstawiający sposób przywracania Wide World Importers — Standard pliku kopii zapasowej można znaleźć [przywrócić pliku kopii zapasowej do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md). Ten samouczek pokazuje, że trzeba przekazać plik kopii zapasowej do magazynu w blogu dotyczącym platformy Azure i bezpieczne, za pomocą klucza podpisu (SAS) dostępu współdzielonego.
  - Aby uzyskać informacji na temat przywracania z adresu URL, zobacz [natywnych przywracania z adresu URL](sql-database-managed-instance-migrate.md#native-restore-from-url).
  
### <a name="data-migration-service"></a>Data Migration Service

Azure Database Migration Service to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemową migrację z wielu źródłowych baz danych na danych na platformę Azure przy minimalnych przestojach. Ta usługa usprawnia zadania wymagane do przenoszenia istniejących innych firm i baz danych programu SQL Server na platformie Azure. Opcje wdrażania obejmują usługi Azure SQL Database, wystąpienia zarządzanego i programu SQL Server w maszynie Wirtualnej platformy Azure w publicznej wersji zapoznawczej. Zobacz [jak przeprowadzić migrację z lokalnej bazy danych do wystąpienia zarządzanego przy użyciu usługi DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Funkcji SQL, które są obsługiwane 

Zarządzane wystąpienia ma na celu dostarczać blisko powierzchni 100% zgodności z lokalnego programu SQL Server zostaną dodane w etapach, aż do ogólnej dostępności usługi. Dla funkcji i listy porównanie, zobacz [porównanie funkcji usługi SQL Database](sql-database-features.md)i aby uzyskać listę różnic języka T-SQL w wystąpieniach zarządzanych w stosunku do programu SQL Server, zobacz [różnice języka T-SQL wystąpienia zarządzanego z programu SQL Server](sql-database-managed-instance-transact-sql-information.md).
 
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

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Jak programowo identyfikować wystąpienia zarządzanego

W poniższej tabeli przedstawiono kilka właściwości, za pośrednictwem języka Transact SQL, można użyć do wykrywania, czy aplikacja działa przy użyciu wystąpienia zarządzanego i pobrać ważne właściwości.

|Właściwość|Wartość|Komentarz|
|---|---|---|
|`@@VERSION`|Program Microsoft SQL Azure (RTM) — 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ta wartość jest ten sam, jak bazy danych SQL.|
|`SERVERPROPERTY ('Edition')`|Usługi SQL Azure|Ta wartość jest ten sam, jak bazy danych SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Ta wartość jest jednoznacznie identyfikuje wystąpienie zarządzane.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nazwa DNS pełnego wystąpienia w następującym formacie:<instanceName>.<dnsPrefix>. Database.Windows.NET, gdzie <instanceName> jest nazwa podana przez klienta, podczas gdy <dnsPrefix> jest generowane automatycznie na częścią nazwy, gwarantujących globalnego unikatowość nazwy DNS ("wcus17662feb9ce98", na przykład)|Example: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [przewodnika Szybki start](sql-database-managed-instance-get-started.md).
- Dla funkcji i listy porównanie, zobacz [typowe funkcje SQL](sql-database-features.md).
- Aby uzyskać więcej informacji o konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-vnet-configuration.md).
- Aby uzyskać samouczek, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [utworzysz wystąpienie zarządzane](sql-database-managed-instance-get-started.md).
- Aby skorzystać z samouczka w zakresie używania usługi Azure Database Migration Service (DMS) do celów migracji, zobacz [Migracja wystąpień zarządzanych przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać informacje o cenach, zobacz [wystąpienie zarządzane usługi SQL Database, cennik](https://azure.microsoft.com/pricing/details/sql-database/managed/).
