---
title: Usługa Azure SQL Database zaawansowane Omówienie zabezpieczeń usługi data | Dokumentacja firmy Microsoft
description: W tym temacie opisano zaawansowanych zabezpieczeń danych usługi Azure SQL Database oraz wyjaśniono, jak to działa, i jak różni się od jednego lub puli bazy danych w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: 15b7bfc9e1d747fcefdbba03ca254a9604197b5f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361826"
---
# <a name="use-sql-database-advanced-data-security-with-virtual-networks-and-near-100-compatibility"></a>Użyj zaawansowanych zabezpieczeń danych przy użyciu sieci wirtualnych i w prawie 100% zgodności bazy danych SQL

Wystąpienie zarządzane to nowa opcja wdrożenia usługi Azure SQL Database, zapewniając prawie 100% utrzymywania zgodności z najnowszą programu SQL Server w środowisku lokalnym aparatu bazy danych (Enterprise Edition), zapewniających macierzystej [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md) wdrożenia, odnoszący się do typowe problemy dotyczące zabezpieczeń i [modelu biznesowego](https://azure.microsoft.com/pricing/details/sql-database/) korzystna dla klientów programu SQL Server w środowisku lokalnym. Model wdrożenia wystąpienia zarządzanego umożliwia istniejących klientów programu SQL Server do lift- and -shift ich aplikacji lokalnych do chmury przy minimalnych zmianach aplikacji i baz danych. W tym samym czasie opcji wdrożenia wystąpienia zarządzanego zachowuje wszystkie możliwości PaaS (Aktualizacje automatyczne stosowanie poprawek i wersja, [automatyczne kopie zapasowe](sql-database-automated-backups.md), [wysokiej dostępności](sql-database-high-availability.md) ), który spowoduje drastyczne zmniejsza koszty zarządzania i całkowity koszt posiadania.

> [!IMPORTANT]
> Aby uzyskać listę regionów, w których jest obecnie dostępna opcji wdrożenia wystąpienia zarządzanego, zobacz [obsługiwane regiony](sql-database-managed-instance-resource-limits.md#supported-regions).

Poniższy diagram przedstawia kluczowe funkcje wystąpienia zarządzanego:

![Najważniejsze funkcje](./media/sql-database-managed-instance/key-features.png)

Model wdrożenia wystąpienia zarządzanego jest przeznaczona dla klientów, które chcesz migrować dużej liczby aplikacji ze środowiska lokalnego lub IaaS, opracowany własny, lub niezależnego dostawcy oprogramowania wyposażone w środowisku, aby w pełni zarządzanym środowisku chmury PaaS jako niski migracji nakład pracy, jak to możliwe. Przy użyciu w pełni zautomatyzowane [Data Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) na platformie Azure, klienci mogą lift- and shift ich na lokalnym serwerze SQL Server do wystąpienia zarządzanego, który zapewnia zgodność z programu SQL Server w środowisku lokalnym i pełną izolację pamięci wystąpienia klientów z natywną obsługą sieci wirtualnej.  Z pakietem Software Assurance mogą wymieniać swoich istniejących licencji do korzystania z taryf rabatowych na wystąpienie zarządzane przy użyciu [korzyść użycia hybrydowego platformy Azure dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  Wystąpienie zarządzane to najlepsze miejsce docelowe migracji w chmurze dla wystąpień programu SQL Server, które wymagają wysokiego poziomu zabezpieczeń i powierzchni sformatowanego programowania.

Cele opcji wdrożenia wystąpienia zarządzanego dostarcza blisko powierzchni 100% zgodności z najnowszą wersją programu SQL Server w środowisku lokalnym za pośrednictwem planu wersji etapowe.

Aby wybrać opcje wdrażania usługi Azure SQL Database: pojedynczej bazy danych, baza danych w puli, a wystąpienia zarządzanego i programu SQL Server hostowanego na maszynie wirtualnej, zobacz [sposobu wybierania właściwej wersji programu SQL Server na platformie Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Najważniejsze funkcje i możliwości

Zarządzane wystąpienia łączy najlepsze funkcje, które są dostępne zarówno w usłudze Azure SQL Database i aparatu bazy danych programu SQL Server.

> [!IMPORTANT]
> Wystąpienie zarządzane jest uruchamiany z wszystkich funkcji najnowszą wersję programu SQL Server, w tym operacje online, plan automatyczne poprawki i inne ulepszenia wydajności enterprise. Porównanie funkcji dostępnych została wyjaśniona w [porównanie funkcji: Usługa Azure SQL Database i programu SQL Server](sql-database-features.md).

| **Korzyści PaaS** | **Ciągłość działalności biznesowej:** |
| --- | --- |
|Zakupu sprzętu i zarządzania <br>Brak zarządzania w czasie zarządzania podstawową infrastrukturą <br>Szybka aprowizacja i skalowanie usług <br>Automatyczne stosowanie poprawek i wersja uaktualnienia <br>Integracja z innymi usługami danych PaaS |dostępność przez 99,99% umowę SLA  <br>Wbudowane [wysokiej dostępności](sql-database-high-availability.md) <br>Dane są chronione za pomocą [automatyczne kopie zapasowe](sql-database-automated-backups.md) <br>Okres przechowywania kopii zapasowych można skonfigurować klienta <br>Użytkownik zainicjował [kopii zapasowych](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Punkt w czasie przywracania bazy danych](sql-database-recovery-using-backups.md#point-in-time-restore) możliwości |
|**Zabezpieczenia i zgodność** | **Zarządzanie**|
|Środowisko izolowane ([Integracja z siecią wirtualną](sql-database-managed-instance-connectivity-architecture.md), pojedynczej dzierżawy usługi, dedykowanych obliczeń i magazynowania) <br>[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Uwierzytelnianie usługi Azure AD](sql-database-aad-authentication.md), pojedynczy Obsługa logowania jednokrotnego <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Jednostki serwera w usłudze Azure AD (logowania)</a> (**publicznej wersji zapoznawczej**) <br>Zgodnego ze standardami zgodności takie same jak Azure SQL database <br>[Inspekcja SQL](sql-database-managed-instance-auditing.md) <br>[Wykrywanie zagrożeń](sql-database-managed-instance-threat-detection.md) |Interfejs API Azure Resource Manager do automatyzowania usługi aprowizacja i skalowanie <br>Funkcjonalność portalu platformy Azure dla usługi ręczna aprowizacja i skalowanie <br>Data Migration Service

> [!IMPORTANT]
> Usługa Azure SQL Database (wszystkich opcjach wdrażania) zostanie certyfikowana na liczbie standardów zgodności. Aby uzyskać więcej informacji, zobacz [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) gdzie można znaleźć najbardziej aktualną listą ze zgodnością bazy danych SQL.

Najważniejsze funkcje zarządzanych wystąpień przez przedstawiono w poniższej tabeli:

|Cecha | Opis|
|---|---|
| Wersja programu SQL Server / build | Aparat bazy danych programu SQL Server (Najnowsza wersja stabilna) |
| Zarządzane automatycznych kopii zapasowych | Yes |
| Wbudowane wystąpienia i bazy danych monitorowania i metryki | Yes |
| Automatyczne stosowanie poprawek | Yes |
| Najnowsze funkcje aparatu bazy danych | Yes |
| Liczba plików danych (wiersze) na bazę danych | Wiele |
| Liczba plików dziennika (dziennik) na bazę danych | 1 |
| Sieć wirtualna - wdrożenia usługi Azure Resource Manager | Yes |
| Sieć wirtualna - klasycznego modelu wdrażania | Nie |
| Portal pomocy technicznej | Yes|
| Wbudowana integracja Service (SSIS) | Nie — SSIS jest częścią [PaaS fabryki danych platformy Azure](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Wbudowane Analysis Services (SSAS) | Nie — usługi SSAS jest oddzielona [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Wbudowana usługa raportowania (SSRS) | Nie — przy użyciu usługi Power BI lub IaaS usług SSRS |
|||

## <a name="vcore-based-purchasing-model"></a>Model zakupu bazujący na rdzeniach wirtualnych

[Modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) dla wystąpienia zarządzanego zapewnia elastyczność możesz, kontrola, przejrzystości i prostą metodę tłumaczenia wymagań obciążenia w chmurze lokalnie. Ten model umożliwia zmianę obliczeniowych, pamięci i magazynu, w zależności od potrzeb obciążenia. Model rdzenia wirtualnego jest również kwalifikuje się do 30 procent oszczędności w przypadku [korzyść użycia hybrydowego platformy Azure dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

Model rdzenia wirtualnego można wybrać generacji sprzętu.

- **4. generacji** procesorów logicznych są oparte na Intel E5-2673 v3 (Haswell) 2,4 GHz, procesory, dołączonych dysków SSD, fizyczne rdzenie, 7 GB pamięci RAM na rdzeń i rozmiarów wystąpień obliczeniowych od 8 do 24 rdzenie wirtualne.
- **5. generacji** procesorów logicznych są oparte na Intel E5-2673 v4 (broadwell z zegarem) 2.3 — GHz, szybkie dyski SSD NVMe, funkcji hyper Threading rdzeń logiczny, a obliczenia rozmiarów, od 8 do 80 rdzeni.

Znajdź więcej informacji na temat różnic między generacji sprzętu w [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Zarządzane wystąpienie warstwy usług

Wystąpienie zarządzane jest dostępna w dwóch warstwach usługi:

- **Ogólnego przeznaczenia**: Zaprojektowana na potrzeby aplikacji za pomocą wydajności typowe wymagania dotyczące opóźnień we/wy.
- **Krytyczne dla działania**: Zaprojektowana na potrzeby aplikacji za pomocą niskie wymagania dotyczące opóźnień We/Wy i minimalny wpływ podstawowych operacji konserwacji obciążenia pracą.

Obie warstwy usług gwarantuje dostępność na poziomie 99,99% i pozwalają na niezależne wybierz rozmiar magazynu i moc obliczeniową. Aby uzyskać więcej informacji na temat architektury wysokiej dostępności usługi Azure SQL Database, zobacz [wysokiej dostępności i Azure SQL Database](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Warstwy usług ogólnego przeznaczenia

Na poniższej liście opisano kluczowe cechy charakterystyczne dla warstwy usług ogólnego przeznaczenia:

- Projektowanie pod kątem większość aplikacji biznesowych z wymaganiami dotyczącymi wydajności typowe
- Magazyn obiektów Blob platformy Azure o wysokiej wydajności (8 TB)
- Wbudowane [wysokiej dostępności](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) oparciu o niezawodny magazyn obiektów Blob platformy Azure i [usługi Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Aby uzyskać więcej informacji, zobacz [magazynu w warstwie ogólnie rzecz biorąc warstwie przeznaczenie](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) i [magazynu najlepsze rozwiązania w zakresie wydajności i zagadnienia dotyczące zarządzane wystąpienia (ogólnego przeznaczenia)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Znajdź więcej informacji na temat różnic między warstwami usług w [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Warstwy usług krytycznych biznesowych

Warstwy usług krytycznych firm zaprojektowano pod kątem aplikacji za pomocą wysokie wymagania dotyczące operacji We/Wy. Oferuje ona najwyższą odporność na awarie, korzystając z kilku izolowanych replik.

Poniższa lista zawiera opis kluczowych charakterystyk krytyczne dla działania firmy warstwy usług:

- Przeznaczona dla aplikacji biznesowych o najwyższej wydajności i wymaganiami wysokiej dostępności
- Dołączono superszybkiego lokalny magazyn SSD (do 1 TB na 4. generacji i maksymalnie 4 TB na 5. generacji)
- Wbudowane [wysokiej dostępności](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) na podstawie [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) i [usługi Azure Service Fabric](../service-fabric/service-fabric-overview.md).
- Dodatkowe wbudowane [repliki bazy danych tylko do odczytu](sql-database-read-scale-out.md) mogą służyć do raportowania i innych obciążeń, tylko do odczytu
- [Przetwarzanie OLTP danych w pamięci](sql-database-in-memory.md) które mogą być używane w przypadku obciążeń z wymogami wysokiej wydajności  

Znajdź więcej informacji na temat różnic między warstwami usług w [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="advanced-security-and-compliance"></a>Zaawansowane zabezpieczenia i zgodność

Opcji wdrożenia wystąpienia zarządzanego łączy zaawansowane funkcje zabezpieczeń udostępnianych przez chmury platformy Azure i aparat bazy danych programu SQL Server.

### <a name="managed-instance-security-isolation"></a>Izolacja na potrzeby zabezpieczeń wystąpienia zarządzanego

Wystąpienie zarządzane zapewnia dodatkowe zabezpieczenia izolację od pozostałych dzierżawców w chmurze platformy Azure. Izolacja na potrzeby zabezpieczeń obejmują:

- [Implementacja natywnych sieci wirtualnej](sql-database-managed-instance-connectivity-architecture.md) i łączności do środowiska lokalnego przy użyciu usługi Azure Expressroute lub bram sieci VPN.
- Punkt końcowy SQL jest uwidaczniany wyłącznie za pośrednictwem prywatnego adresu IP, umożliwiając bezpieczne połączenie na platformie Azure prywatnej lub hybrydowej sieci.
- Jednej dzierżawy za pomocą dedykowanego podstawowej infrastruktury (moc obliczeniowa, Magazyn).

Poniższy diagram przedstawia różne opcje łączności dla aplikacji:

![Wysoka dostępność](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Aby dowiedzieć się więcej na temat integracji sieci wirtualnej i sieci wymuszanie zasad na poziomie podsieci, zobacz [architektury sieci wirtualnej dla wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md) i [połączyć aplikację z wystąpienia zarządzanego](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> W tej samej podsieci, należy umieścić wiele wystąpienia zarządzanego, wszędzie tam, gdzie, jest dozwolona przez Twoje wymagania dotyczące bezpieczeństwa, ponieważ, zostaną wyświetlone dodatkowe korzyści. Collocating wystąpień w tej samej podsieci spowoduje znacznie upraszcza konserwację infrastruktury sieci i zmniejszyć czas, obsługi administracyjnej, ponieważ aprowizacja długi czas trwania jest skojarzony z kosztami wdrażania pierwszego wystąpienia zarządzanego w podsieci wystąpienia.

### <a name="azure-sql-database-security-features"></a>Funkcje zabezpieczeń bazy danych Azure SQL

Usługa Azure SQL Database udostępnia zestaw zaawansowanych funkcji zabezpieczeń, które mogą służyć do ochrony danych.

- [Zarządzane wystąpienia inspekcji](sql-database-managed-instance-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w pliku dziennika inspekcji umieścić na koncie magazynu platformy Azure. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.
- Szyfrowanie danych w ruchu — wystąpienie zarządzane zabezpiecza dane, zapewniając szyfrowanie danych w ruchu przy użyciu Transport Layer Security. Oprócz zabezpieczeń warstwy transportu, opcji wdrożenia wystąpienia zarządzanego zapewnia ochronę danych poufnych w locie, podczas przechowywania i podczas przetwarzania zapytań przy użyciu [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Funkcja Always Encrypted to pierwsze w branży rozwiązanie, które oferuje bezkonkurencyjne zabezpieczenie danych przed naruszeniami, w tym przed kradzieżą danych o kluczowym znaczeniu. Na przykład z funkcją Always Encrypted numery kart kredytowych są przechowywane w bazie danych zawsze, nawet podczas przetwarzania zapytania, z możliwością odszyfrowania w punkcie użycia przez autoryzowany personel lub przez aplikacje wymagające przetwarzania tych danych.
- [Wykrywanie zagrożeń](sql-database-managed-instance-threat-detection.md) uzupełnia [inspekcji](sql-database-managed-instance-auditing.md) , zapewniając dodatkową warstwę zabezpieczeń analizy wbudowana w usługę, która wykrywa nietypowe i potencjalnie szkodliwe próby dostępu lub wykorzystania baz danych. Alerty o podejrzanych działań, potencjalnych lukach w zabezpieczeniach i ataki wstrzyknięcie kodu SQL, a także bazy danych nietypowe wzorce dostępu. Alerty wykrywania zagrożeń, mogą być wyświetlane z [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/) zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.  
- [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking) ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników. Dynamiczne maskowanie danych pomaga zapobiec nieautoryzowanemu dostępowi do danych poufnych, umożliwiając wyznaczenie, jaka część danych poufnych może zostać odsłonięta, przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian.
- [Zabezpieczenia](/sql/relational-databases/security/row-level-security) umożliwia kontrolę dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (takich jak przez grupy członkostwa lub kontekstu wykonania). Zabezpieczenia na poziomie wiersza (RLS, Row-Level Security) upraszczają projektowanie i kodowanie zabezpieczeń w aplikacji. Zabezpieczenia na poziomie wiersza umożliwiają zaimplementowanie ograniczeń w dostępie do wiersza danych. Na przykład zapewnienie, że pracownicy mają dostęp tylko wiersze danych, które są odpowiednie do ich działu, lub ograniczenie dostępu do danych do odpowiednich danych.
- [Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) szyfruje pliki danych wystąpienia zarządzanego, znane jako szyfrowanie danych magazynowanych. Funkcja TDE wykonuje w czasie rzeczywistym operacji We/Wy szyfrowania i odszyfrowywania plików danych i dziennika. Szyfrowanie używa klucza szyfrowania bazy danych (klucz szyfrowania danych), który jest przechowywany w rekordzie rozruchowym bazy danych dostępności podczas odzyskiwania. Umożliwia ochronę wszystkich baz danych w wystąpieniu zarządzanym za pomocą technologii transparent data encryption. Funkcja TDE jest program SQL Server, sprawdzonych technologii szyfrowania podczas spoczynku, która jest wymagana przez wiele standardów zgodności w celu ochrony przed kradzieżą nośników magazynowania.

Migracja zaszyfrowanych bazy danych do wystąpienia zarządzanego jest obsługiwana za pomocą usługi Azure Database Migration Service (DMS) lub native przywracania. Jeśli planujesz migrację zaszyfrowanej przy użyciu natywnych przywracania bazy danych, migracja istniejących funkcji TDE certyfikatu z lokalnego programu SQL Server lub SQL Server na maszynie wirtualnej wystąpienia zarządzanego jest to krok wymagany. Aby uzyskać więcej informacji na temat opcji migracji, zobacz [migracja wystąpienia programu SQL Server do wystąpienia zarządzanego](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integracja z usługą Azure Active Directory

Opcji wdrożenia wystąpienia zarządzanego obsługuje tradycyjne logowania aparatu bazy danych programu SQL server i nazwami logowania, zintegrowane za pomocą usługi Azure Active Directory (AAD). Jednostki serwera w usłudze Azure AD (logowania) (**publicznej wersji zapoznawczej**) są wersja chmury platformy Azure w środowisku lokalnym logowania do bazy danych używane w środowisku w środowisku lokalnym. Jednostki serwera w usłudze Azure AD (logowania) pozwala na określenie użytkowników i grup w usłudze Azure Active Directory dzierżawy na wartość true zakresie wystąpienia jednostki, może wykonać żadnych operacji na poziomie wystąpienia, takich jak zapytania wielu baz danych w tym samym wystąpienie zarządzane.

Wprowadzono nową składnię do tworzenia podmiotów zabezpieczeń serwera (logowania) usługi Azure AD (**publicznej wersji zapoznawczej**), **z zewnętrznego dostawcy**. Aby uzyskać więcej informacji na temat składni, zobacz <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>i przejrzyj [aprowizacji administrator usługi Azure Active Directory dla wystąpienia zarządzanego](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) artykułu.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym

Opcji wdrożenia wystąpienia zarządzanego umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft, za pomocą [integracji usługi Azure Active Directory](sql-database-aad-authentication.md). Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Usługa Azure Active Directory obsługuje [uwierzytelnianie wieloskładnikowe](sql-database-ssms-mfa-authentication-configure.md) (MFA, Multi-Factor Authentication) w celu zwiększenia bezpieczeństwa danych i aplikacji, korzystając z procesu jednokrotnego logowania.

### <a name="authentication"></a>Authentication

Wystąpienie zarządzane uwierzytelnianie odnosi się do sposobu użytkowników potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:  

- **Uwierzytelnianie SQL**:

  Ta metoda uwierzytelniania używa nazwy użytkownika i hasła.
- **Usługa Azure Active Directory Authentication**:

  Ta metoda uwierzytelniania korzysta z tożsamości zarządzanej przez usługę Azure Active Directory i jest obsługiwany w przypadku zarządzanych i zintegrowanych domen. Używaj uwierzytelniania usługi Active Directory (zabezpieczeń zintegrowanych), [gdy tylko jest to możliwe](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autoryzacja

Autoryzacja Określa, co można zrobić w ramach usługi Azure SQL Database i jest kontrolowana przez członkostw roli bazy danych konta użytkownika i uprawnienia na poziomie obiektu. Wystąpienie zarządzane ma te same możliwości autoryzacji, co program SQL Server 2017.

## <a name="database-migration"></a>Migracja bazy danych

Scenariusze użytkownika obiektów docelowych — Opcja wdrażania wystąpienia zarządzanego przy użyciu migracji pamięci masowej bazy danych ze środowiska lokalnego lub IaaS bazy danych implementacji. Wystąpienie zarządzane obsługuje kilka opcji migracji bazy danych:

### <a name="back-up-and-restore"></a>Tworzenie kopii zapasowej i przywracanie  

Podejście do migracji wykorzystuje kopii zapasowych SQL w usłudze Azure Blob storage. Kopie zapasowe przechowywane w obiekcie blob usługi Azure storage może bezpośrednio przywrócony do wystąpienia zarządzanego przy użyciu [polecenia języka T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Aby uzyskać szybki start przedstawiający sposób przywracania Wide World Importers — Standard pliku kopii zapasowej, zobacz [przywrócić pliku kopii zapasowej do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md). Ten przewodnik Szybki Start pokazuje, że trzeba przekazać plik kopii zapasowej do magazynu w blogu dotyczącym platformy Azure i bezpieczne, za pomocą klucza podpisu (SAS) dostępu współdzielonego.
- Aby uzyskać informacji na temat przywracania z adresu URL, zobacz [natywnych przywracania z adresu URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Kopie zapasowe z wystąpienia zarządzanego można przywrócić tylko do innego wystąpienia zarządzanego. Nie można przywrócić do lokalnego programu SQL Server lub do pojedynczej bazy danych/elastycznej puli.

### <a name="data-migration-service"></a>Data Migration Service

Azure Database Migration Service to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemową migrację z wielu źródłowych baz danych na danych na platformę Azure przy minimalnych przestojach. Ta usługa usprawnia zadania wymagane do przeniesienia istniejących innych firm i baz danych programu SQL Server do usługi Azure SQL Database (pojedynczych baz danych, bazy danych w puli w elastycznej puli i bazy danych wystąpień zarządzanych wystąpienie) i programu SQL Server na maszynie Wirtualnej platformy Azure. Zobacz [jak przeprowadzić migrację z lokalnej bazy danych do wystąpienia zarządzanego przy użyciu usługi DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Funkcji SQL, które są obsługiwane

Opcji wdrożenia wystąpienia zarządzanego ma na celu dostarczać blisko powierzchni 100% zgodności z lokalnego programu SQL Server zostaną dodane w etapach, aż do ogólnej dostępności usługi. Dla funkcji i listy porównanie, zobacz [porównanie funkcji usługi SQL Database](sql-database-features.md)i aby uzyskać listę różnic języka T-SQL w wystąpieniach zarządzanych w stosunku do programu SQL Server, zobacz [zarządzać różnice wystąpienia języka T-SQL za pomocą programu SQL Server](sql-database-managed-instance-transact-sql-information.md).

Opcji wdrożenia wystąpienia zarządzanego obsługuje zgodności z poprzednimi wersjami z bazami danych SQL 2008. Bezpośrednie migracja z serwerami baz danych SQL 2005 jest obsługiwana, poziom zgodności dla migrowanych baz danych SQL 2005 zostały zaktualizowane pod kątem programu SQL 2008.
  
Poniższy diagram przedstawia zgodność powierzchni w wystąpienia zarządzanego:  

![Migracja](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Podstawowe różnice między lokalnego programu SQL Server i wystąpienia zarządzanego

Korzyści z opcji wdrożenia wystąpienia zarządzanego nie zawsze w górę — od początku w chmurze, która oznacza, że niektóre funkcje programu SQL Server w środowisku lokalnym mogą być nieaktualne, wycofania lub mieć alternatyw. Istnieją szczególnych przypadkach, gdy konieczne rozpoznaje, że dana funkcja działa w sposób nieco lub usługa nie jest uruchomiona w środowisku, które nie mają pełnej kontroli nad narzędzi:

- Wysoka dostępność jest wbudowana w i wstępnie skonfigurowane przy użyciu technologii, podobnie jak [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Automatyczne kopie zapasowe i punktu w czasie przywracania. Klient może zainicjować `copy-only` kopie zapasowe, które nie kolidują z automatycznego łańcuch kopii zapasowych.
- Wystąpienie zarządzane nie zezwala na określanie ścieżek Pełna fizyczna, dzięki czemu wszystkie scenariusze odpowiedniego mają być obsługiwane inaczej: Przywracanie bazy danych nie obsługuje opcji WITH MOVE, tworzenie bazy danych nie zezwala na ścieżki fizyczne BULK INSERT współpracuje z obiektów blob platformy Azure, tylko itp.
- Zarządzane wystąpienie obsługuje [uwierzytelniania usługi Azure AD](sql-database-aad-authentication.md) chmury alternatywę dla uwierzytelniania Windows.
- Wystąpienie zarządzane automatycznie zarządza XTP plików i plików baz danych zawierających obiekty OLTP w pamięci
- Wystąpienie zarządzane obsługuje usługi SQL Server Integration Services (SSIS) i mogą hosta wykazu usług SSIS (SSISDB), która przechowuje pakietów usług SSIS, ale są one wykonywane na zarządzanego środowiska Azure-SSIS Integration Runtime (IR) w usłudze Azure Data Factory (ADF), zobacz [Create Azure-SSIS IR w usłudze ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Aby porównać funkcji SSIS w usłudze SQL Database, zobacz [porównanie usługi Azure SQL Database pojedynczej bazy danych/elastycznych pul i wystąpienia zarządzanego](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance).

### <a name="managed-instance-administration-features"></a>Funkcje administracyjne wystąpienia zarządzanego

Opcji wdrożenia wystąpienia zarządzanego umożliwia administratorowi systemu poświęcać mniej czasu na zadania administracyjne, ponieważ usługa SQL Database je wykonuje lub znacznie upraszcza tych zadań. Na przykład [systemu operacyjnego / RDBMS instalacji i stosowanie poprawek](sql-database-high-availability.md), [wystąpienia dynamiczna zmiana rozmiaru i Konfiguracja](sql-database-single-database-scale.md), [kopie zapasowe](sql-database-automated-backups.md), [replikacji bazy danych](replication-with-sql-database-managed-instance.md)(w tym systemowych baz danych), [Konfiguracja wysokiej dostępności](sql-database-high-availability.md)i konfiguracja kondycji i [monitorowania wydajności](../azure-monitor/insights/azure-sql.md) strumieni danych.

> [!IMPORTANT]
> Aby uzyskać listę obsługiwanych, częściowo obsługiwane i nieobsługiwane funkcje, zobacz [funkcji usługi SQL Database](sql-database-features.md). Aby uzyskać listę różnic języka T-SQL w wystąpieniach zarządzanych w stosunku do programu SQL Server, zobacz [zarządzać różnice wystąpienia języka T-SQL za pomocą programu SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Jak programowo identyfikować wystąpienia zarządzanego

W poniższej tabeli przedstawiono kilka właściwości, za pośrednictwem języka Transact SQL, można użyć do wykrywania, czy aplikacja działa przy użyciu wystąpienia zarządzanego i pobrać ważne właściwości.

|Właściwość|Wartość|Komentarz|
|---|---|---|
|`@@VERSION`|Program Microsoft SQL Azure (RTM) — 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ta wartość jest ten sam, jak bazy danych SQL.|
|`SERVERPROPERTY ('Edition')`|Usługi SQL Azure|Ta wartość jest ten sam, jak bazy danych SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Ta wartość jest jednoznacznie identyfikuje wystąpienie zarządzane.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nazwa DNS pełnego wystąpienia w następującym formacie:`<instanceName>`.`<dnsPrefix>`.Database.Windows.NET, gdzie `<instanceName>` jest nazwa podana przez klienta, podczas gdy `<dnsPrefix>` jest automatycznie wygenerowany częścią nazwy gwarantujących globalnego unikatowość nazwy DNS ("wcus17662feb9ce98", na przykład)|Example: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak utworzyć pierwszego wystąpienia zarządzanego, zobacz [przewodnika Szybki Start](sql-database-managed-instance-get-started.md).
- Dla funkcji i listy porównanie, zobacz [typowe funkcje SQL](sql-database-features.md).
- Aby uzyskać więcej informacji o konfiguracji sieci wirtualnej, zobacz [konfiguracja sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
- Aby uzyskać szybki Start, która tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Samouczek dotyczący korzystania z usługi Azure Database Migration Service (DMS) do migracji, zobacz [zarządzane wystąpienia migracji przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać informacje na temat zaawansowanego monitorowania wydajności bazy danych wystąpienia zarządzanego z wbudowaną analizą rozwiązywania problemów, zobacz [Monitor Azure SQL Database using Azure SQL Analytics (Monitorowanie usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics)](../azure-monitor/insights/azure-sql.md)
- Aby uzyskać informacje o cenach, zobacz [zarządzana ceny wystąpienia usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
