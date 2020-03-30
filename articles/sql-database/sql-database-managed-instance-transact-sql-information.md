---
title: Różnice T-SQL wystąpienia zarządzanego
description: W tym artykule omówiono różnice T-SQL między wystąpieniem zarządzanym w bazie danych SQL Azure i programie SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: e01c61ca4f415ffbb46c86034d4b7441bc2617d9
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365491"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>Różnice i ograniczenia T-SQL wystąpienia zarządzanego

W tym artykule podsumowano i wyjaśniono różnice w składni i zachowaniu między wystąpieniem zarządzanym usługi Azure SQL Database a lokalnym aparatem baz danych programu SQL Server. Opcja wdrażania wystąpienia zarządzanego zapewnia wysoką zgodność z lokalnym aparatem baz danych programu SQL Server. Większość funkcji aparatu bazy danych programu SQL Server są obsługiwane w wystąpieniu zarządzanym.

![Migracja](./media/sql-database-managed-instance/migration.png)

Istnieją pewne ograniczenia PaaS, które są wprowadzane w wystąpieniu zarządzanym i niektóre zmiany zachowania w porównaniu do programu SQL Server. Różnice są podzielone na następujące kategorie:<a name="Differences"></a>

- [Dostępność](#availability) obejmuje różnice w [grupach dostępności zawsze włączonych](#always-on-availability-groups) i [kopiach zapasowych.](#backup)
- [Zabezpieczenia](#security) obejmują różnice w [inspekcji,](#auditing) [certyfikatach,](#certificates) [poświadczeniach,](#credential) [dostawcach kryptograficznych,](#cryptographic-providers) [logowaniach i użytkownikach](#logins-and-users)oraz [kluczu usługi i klucza głównego usługi.](#service-key-and-service-master-key)
- [Konfiguracja](#configuration) obejmuje różnice w [rozszerzeniu puli buforów,](#buffer-pool-extension) [sortowaniu,](#collation) [poziomach zgodności,](#compatibility-levels) [dublowaniu bazy danych,](#database-mirroring) [opcjach bazy danych,](#database-options) [programie SQL Server Agent](#sql-server-agent)i [opcjach tabeli.](#tables)
- [Funkcje](#functionalities) obejmują [BULK INSERT / OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transakcje rozproszone](#distributed-transactions), [rozszerzone zdarzenia](#extended-events), [biblioteki zewnętrzne](#external-libraries), [filestream i FileTable](#filestream-and-filetable), [wyszukiwanie semantyczne pełnotekstowe](#full-text-semantic-search), [serwery połączone](#linked-servers), [PolyBase](#polybase), [replikacja](#replication), [przywracanie](#restore-statement), [Service Broker](#service-broker) [, procedury przechowywane, funkcje i wyzwalacze](#stored-procedures-functions-and-triggers).
- [Ustawienia środowiska,](#Environment) takie jak sieci wirtualne i konfiguracje podsieci.

Większość z tych funkcji są ograniczenia architektoniczne i reprezentują funkcje usługi.

Tymczasowe znane problemy, które są odnalezione w wystąpieniu zarządzanym i które zostaną rozwiązane w przyszłości, są opisane na [stronie informacji o wersji](sql-database-release-notes.md).

## <a name="availability"></a>Dostępność

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Zawsze włączone grupy dostępności

[Wysoka dostępność](sql-database-high-availability.md) jest wbudowana w wystąpienie zarządzane i nie może być kontrolowana przez użytkowników. Następujące instrukcje nie są obsługiwane:

- [TWORZENIE PUNKTU KOŃCOWEGO ... DLA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [TWORZENIE GRUPY DOSTĘPNOŚCI](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ZMIENIANIE GRUPY DOSTĘPNOŚCI](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [GRUPA DOSTĘPNOŚCI UPUŚĆ](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Klauzula [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) instrukcji [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Tworzenie kopii zapasowych

Wystąpienia zarządzane mają automatyczne kopie zapasowe, `COPY_ONLY` dzięki czemu użytkownicy mogą tworzyć pełne kopie zapasowe bazy danych. Różnicowe, dziennik i migawka pliku kopie zapasowe nie są obsługiwane.

- Za pomocą wystąpienia zarządzanego można wyw.
  - Obsługiwane `BACKUP TO URL` jest tylko.
  - `FILE``TAPE`, a urządzenia kopii zapasowych nie są obsługiwane.
- Większość opcji `WITH` ogólnych są obsługiwane.
  - `COPY_ONLY`jest obowiązkowe.
  - `FILE_SNAPSHOT`nie jest obsługiwany.
  - Opcje `REWIND`taśmy: `NOREWIND` `UNLOAD`, `NOUNLOAD` , i nie są obsługiwane.
  - Opcje specyficzne dla `NORECOVERY` `STANDBY`dziennika: `NO_TRUNCATE` , i nie są obsługiwane.

Ograniczenia: 

- Za pomocą wystąpienia zarządzanego można wykonać kopię zapasową bazy danych wystąpienia w kopii zapasowej z maksymalnie 32 paskami, co wystarcza bazom danych o pojemności do 4 TB, jeśli używana jest kompresja kopii zapasowej.
- Nie można wykonać `BACKUP DATABASE ... WITH COPY_ONLY` w bazie danych, która jest szyfrowana za pomocą zarządzanego przez usługę przezroczystego szyfrowania danych (TDE). TDE zarządzane przez usługę wymusza szyfrowanie kopii zapasowych za pomocą wewnętrznego klucza TDE. Nie można wyeksportować klucza, więc nie można przywrócić kopii zapasowej. Użyj automatycznych kopii zapasowych i przywracania punktu w czasie lub użyj [zarządzanego przez klienta (BYOK) TDE.](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) Można również wyłączyć szyfrowanie w bazie danych.
- Maksymalny rozmiar paska kopii `BACKUP` zapasowej przy użyciu polecenia w wystąpieniu zarządzanym wynosi 195 GB, co jest maksymalnym rozmiarem obiektu blob. Zwiększ liczbę pasków w poleceniu kopii zapasowej, aby zmniejszyć rozmiar pojedynczego paska i pozostać w tym limicie.

    > [!TIP]
    > Aby obejść to ograniczenie, podczas tworzenie kopii zapasowej bazy danych z programu SQL Server w środowisku lokalnym lub na maszynie wirtualnej, można:
    >
    > - Kopię zapasową zamiast `DISK` tworzenia `URL`kopii zapasowej do pliku .
    > - Przekaż pliki kopii zapasowej do magazynu obiektów Blob.
    > - Przywróć do wystąpienia zarządzanego.
    >
    > Polecenie `Restore` w wystąpieniu zarządzanym obsługuje większe rozmiary obiektów blob w plikach kopii zapasowej, ponieważ do przechowywania przekazanych plików kopii zapasowej jest używany inny typ obiektu blob.

Aby uzyskać informacje na temat tworzenia kopii zapasowych przy użyciu funkcji T-SQL, zobacz [KOPIA ZAPASOWA](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Zabezpieczenia

### <a name="auditing"></a>Inspekcja

Kluczowe różnice między inspekcją w bazach danych w usłudze Azure SQL Database i baz danych w programie SQL Server to:

- Dzięki opcji wdrażania wystąpienia zarządzanego w usłudze Azure SQL Database inspekcja działa na poziomie serwera. Pliki `.xel` dziennika są przechowywane w magazynie obiektów Blob platformy Azure.
- Dzięki opcji wdrażania pojedynczej bazy danych i puli elastycznej w usłudze Azure SQL Database inspekcja działa na poziomie bazy danych.
- W programie SQL Server na maszynach lokalnych lub wirtualnych inspekcja działa na poziomie serwera. Zdarzenia są przechowywane w systemie plików lub dziennikach zdarzeń systemu Windows.
 
Inspekcja XEvent w wystąpieniu zarządzanym obsługuje obiekty docelowe magazynu obiektów Blob platformy Azure. Dzienniki plików i systemu Windows nie są obsługiwane.

Kluczowe różnice w `CREATE AUDIT` składni do inspekcji do magazynu obiektów Blob platformy Azure są następujące:

- Nowa składnia `TO URL` jest pod warunkiem, że można użyć do określenia adresu `.xel` URL kontenera magazynu obiektów Blob platformy Azure, w którym są umieszczane pliki.
- Składnia nie `TO FILE` jest obsługiwana, ponieważ wystąpienie zarządzane nie może uzyskać dostępu do udziałów plików systemu Windows.

Aby uzyskać więcej informacji, zobacz: 

- [TWORZENIE INSPEKCJI SERWERA](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [KONTROLA SERWERA ALTER](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Inspekcja](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certyfikaty

Wystąpienie zarządzane nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, dlatego obowiązują następujące ograniczenia:

- `CREATE FROM` / Plik `BACKUP TO` nie jest obsługiwany dla certyfikatów.
- `CREATE` / Certyfikat `BACKUP` `FILE` z / nie jest obsługiwany. `ASSEMBLY` Nie można używać plików kluczy prywatnych. 

Zobacz [TWORZENIE CERTYFIKATU](/sql/t-sql/statements/create-certificate-transact-sql) I [CERTYFIKATU KOPII ZAPASOWEJ](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Obejście**: Zamiast tworzyć kopię zapasową certyfikatu i przywracać kopię zapasową, [pobierz zawartość binarną certyfikatu i klucz prywatny, przechowuj go jako plik .sql i twórz z pliku binarnego:](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Poświadczenie

Obsługiwane są tylko `SHARED ACCESS SIGNATURE` usługi Azure Key Vault i tożsamości. Użytkownicy systemu Windows nie są obsługiwani.

Zobacz [TWORZENIE POŚWIADCZEŃ](/sql/t-sql/statements/create-credential-transact-sql) I [ZMIENIANIE POŚWIADCZEŃ](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Dostawcy kryptograficznych

Wystąpienie zarządzane nie może uzyskać dostępu do plików, więc nie można utworzyć dostawców kryptograficznych:

- `CREATE CRYPTOGRAPHIC PROVIDER`nie jest obsługiwany. Zobacz [TWORZENIE DOSTAWCY KRYPTOGRAFICZNEGO](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER`nie jest obsługiwany. Zobacz [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Nazwy logowania i użytkownicy

- Logowania SQL utworzone `FROM CERTIFICATE`za `FROM ASYMMETRIC KEY`pomocą `FROM SID` programu , i są obsługiwane. Zobacz [TWORZENIE LOGOWANIA .](/sql/t-sql/statements/create-login-transact-sql)
- Obsługiwane są podmioty główne (logowania) serwera usługi Azure Active Directory (Azure AD) utworzone za pomocą składni [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) lub CREATE USER FROM [LOGIN [Azure AD Login].](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) Te logowania są tworzone na poziomie serwera.

    Wystąpienie zarządzane obsługuje podmioty bazy danych `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`usługi Azure AD ze składnią . Ta funkcja jest również znany jako użytkownicy bazy danych zawarte w usłudze Azure AD.

- Logowania systemu Windows `CREATE LOGIN ... FROM WINDOWS` utworzone ze składnią nie są obsługiwane. Użyj logowania usługi Azure Active Directory i użytkowników.
- Użytkownik usługi Azure AD, który utworzył wystąpienie, ma [nieograniczone uprawnienia administratora](sql-database-manage-logins.md).
- Użytkownicy na poziomie bazy danych usługi Azure `CREATE USER ... FROM EXTERNAL PROVIDER` AD niebędący administratorem usługi Azure mogą być tworzone przy użyciu składni. Zobacz [CREATE USER ... OD ZEWNĘTRZNEGO DOSTAWCY](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
- Podmioty serwera usługi Azure AD (logowania) obsługują funkcje SQL tylko w ramach jednego wystąpienia zarządzanego. Funkcje, które wymagają interakcji między wystąpieniami, niezależnie od tego, czy znajdują się w tej samej dzierżawie usługi Azure AD lub różnych dzierżaw, nie są obsługiwane dla użytkowników usługi Azure AD. Przykładami takich funkcji są:

  - Replikacja transakcyjna SQL.
  - Serwer łączy.

- Ustawianie logowania usługi Azure AD mapowane do grupy usługi Azure AD jako właściciel bazy danych nie jest obsługiwany.
- Personifikacja podmiotów na poziomie serwera usługi Azure AD przy użyciu innych podmiotów usługi Azure AD jest obsługiwana, na przykład klauzula [EXECUTE AS.](/sql/t-sql/statements/execute-as-transact-sql) Ograniczenia EXECUTE AS to:

  - Execute AS USER nie jest obsługiwany dla użytkowników usługi Azure AD, gdy nazwa różni się od nazwy logowania. Przykładem jest, gdy użytkownik jest tworzony za pomocą składni CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] i personifikacji jest podejmowana za pośrednictwem EXEC AS USER = _myAadUser_. Podczas tworzenia **użytkownika** z jednostki serwera usługi Azure AD (login), należy określić user_name jako ten sam login_name z **loginu**.
  - Tylko podmioty na poziomie programu SQL Server (logowania), `sysadmin` które są częścią roli, mogą wykonywać następujące operacje, które są przeznaczone dla podmiotów platformy Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Eksportowanie/importowanie bazy danych przy użyciu plików bacpac jest obsługiwane dla użytkowników usługi Azure AD w wystąpieniu zarządzanym przy użyciu [ssms V18.4 lub nowszego](/sql/ssms/download-sql-server-management-studio-ssms)lub [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - Następujące konfiguracje są obsługiwane przy użyciu pliku bacpac bazy danych: 
    - Eksportowanie/importowanie bazy danych między różnymi wystąpieniami zarządzania w tej samej domenie usługi Azure AD.
    - Eksportuj bazę danych z wystąpienia zarządzanego i importuj do bazy danych SQL w tej samej domenie usługi Azure AD. 
    - Eksportuj bazę danych z bazy danych SQL i importuj do wystąpienia zarządzanego w tej samej domenie usługi Azure AD.
    - Eksportuj bazę danych z wystąpienia zarządzanego i importuj do programu SQL Server (wersja 2012 lub nowsza).
      - W tej konfiguracji wszyscy użytkownicy usługi Azure AD są tworzone jako podmioty bazy danych SQL (użytkownicy) bez logowania. Typ użytkowników są wymienione jako SQL (widoczne jako SQL_USER w pliku sys.database_principals). Ich uprawnienia i role pozostają w metadanych bazy danych programu SQL Server i mogą być używane do personifikacji. Jednak nie można ich używać do uzyskiwania dostępu i logowania się do programu SQL Server przy użyciu ich poświadczeń.

- Tylko logowania głównego na poziomie serwera, który jest tworzony przez proces inicjowania `securityadmin` `sysadmin`obsługi administracyjnej wystąpienia zarządzanego, członków ról serwera, takich jak lub , lub innych loginów z alter any logowania uprawnienia na poziomie serwera można utworzyć jednostki serwera usługi Azure AD (logowania) w głównej bazie danych dla wystąpienia zarządzanego.
- Jeśli login jest podmiotem SQL, tylko logowania, `sysadmin` które są częścią roli można użyć polecenia utwórz do tworzenia logowania dla konta usługi Azure AD.
- Logowania usługi Azure AD musi być członkiem usługi Azure AD w tym samym katalogu, który jest używany dla usługi Azure SQL Database wystąpienia zarządzanego.
- Podmioty serwera usługi Azure AD (loginy) są widoczne w Eksploratorze obiektów, począwszy od programu SQL Server Management Studio 18.0 preview 5.
- Dozwolone jest nakładanie się podmiotów zabezpieczeń serwera usługi Azure AD (logowania) z kontem administratora usługi Azure AD. Podmioty serwera usługi Azure AD (logowania) mają pierwszeństwo przed administratorem usługi Azure AD po rozwiązaniu podmiotu zabezpieczeń i zastosowaniu uprawnień do wystąpienia zarządzanego.
- Podczas uwierzytelniania w celu rozwiązania podmiotu uwierzytelniającego stosowana jest następująca sekwencja:

    1. Jeśli konto usługi Azure AD istnieje jako bezpośrednio mapowane do podmiotu zabezpieczeń serwera usługi Azure AD (login), który jest obecny w pliku sys.server_principals jako typ "E", udzielić dostępu i zastosować uprawnienia podmiotu zabezpieczeń serwera usługi Azure AD (login).
    2. Jeśli konto usługi Azure AD jest członkiem grupy usługi Azure AD, która jest mapowana na podmiot zabezpieczeń serwera usługi Azure AD (login), który jest obecny w pliku sys.server_principals jako typ "X", udzielić dostępu i zastosować uprawnienia logowania grupy usługi Azure AD.
    3. Jeśli konto usługi Azure AD jest specjalnym administratorem usługi Azure AD skonfigurowanym przez portal dla wystąpienia zarządzanego, które nie istnieje w widokach systemu wystąpienia zarządzanego, zastosuj specjalne stałe uprawnienia administratora usługi Azure AD dla wystąpienia zarządzanego (tryb starszej wersji).
    4. Jeśli konto usługi Azure AD istnieje jako bezpośrednio mapowane do użytkownika usługi Azure AD w bazie danych, która jest obecna w pliku sys.database_principals jako typ "E", udzielić dostępu i zastosować uprawnienia użytkownika bazy danych usługi Azure AD.
    5. Jeśli konto usługi Azure AD jest członkiem grupy usługi Azure AD, która jest mapowana na użytkownika usługi Azure AD w bazie danych, która jest obecna w pliku sys.database_principals jako typ "X", udzielić dostępu i zastosować uprawnienia logowania grupy usługi Azure AD.
    6. Jeśli istnieje interfejs logowania usługi Azure AD mapowany na konto użytkownika usługi Azure AD lub konto grupy usługi Azure AD, które jest rozpoznawane przez użytkownika, który uwierzytelnia, zostaną zastosowane wszystkie uprawnienia z tego logowania usługi Azure AD.

### <a name="service-key-and-service-master-key"></a>Klucz główny klucza usługi i usługi

- [Kopia zapasowa klucza](/sql/t-sql/statements/backup-master-key-transact-sql) głównego nie jest obsługiwana (zarządzana przez usługę bazy danych SQL).
- [Przywracanie klucza](/sql/t-sql/statements/restore-master-key-transact-sql) głównego nie jest obsługiwane (zarządzane przez usługę bazy danych SQL).
- [Kopia zapasowa klucza głównego usługi](/sql/t-sql/statements/backup-service-master-key-transact-sql) nie jest obsługiwana (zarządzana przez usługę bazy danych SQL).
- [Przywracanie klucza głównego usługi](/sql/t-sql/statements/restore-service-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę bazy danych SQL).

## <a name="configuration"></a>Konfigurowanie

### <a name="buffer-pool-extension"></a>Rozszerzenie puli buforów

- [Rozszerzenie puli buforów](/sql/database-engine/configure-windows/buffer-pool-extension) nie jest obsługiwane.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`nie jest obsługiwany. Zobacz [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Sortowanie

Domyślne sortowanie `SQL_Latin1_General_CP1_CI_AS` wystąpienia jest i może być określone jako parametr tworzenia. Zobacz [Sortowania](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Poziomy zgodności

- Obsługiwane poziomy zgodności to 100, 110, 120, 130, 140 i 150.
- Poziomy zgodności poniżej 100 nie są obsługiwane.
- Domyślny poziom zgodności dla nowych baz danych to 140. W przypadku przywróconych baz danych poziom zgodności pozostaje niezmieniony, jeśli był 100 i powyżej.

Zobacz [Poziom zgodności bazy danych ALTER](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Dublowanie bazy danych

Dublowanie bazy danych nie jest obsługiwane.

- `ALTER DATABASE SET PARTNER`i `SET WITNESS` opcje nie są obsługiwane.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`nie jest obsługiwany.

Aby uzyskać więcej informacji, zobacz [ALTER DATABASE SET PARTNER i SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) and CREATE [ENDPOINT ... dla DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opcje bazy danych

- Wiele plików dziennika nie są obsługiwane.
- Obiekty w pamięci nie są obsługiwane w warstwie usługi ogólnego przeznaczenia. 
- Istnieje limit 280 plików na wystąpienie ogólnego przeznaczenia, co oznacza maksymalnie 280 plików na bazę danych. Pliki danych i dzienników w warstwie ogólnego przeznaczenia są wliczane do tego limitu. [Warstwa Krytyczna dla firmy obsługuje 32 767 plików na bazę danych.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)
- Baza danych nie może zawierać grup plików zawierających dane strumienia plików. Przywracanie kończy się `FILESTREAM` niepowodzeniem, jeśli plik .bak zawiera dane. 
- Każdy plik jest umieszczany w magazynie obiektów Blob platformy Azure. We/Wy i przepływność na plik zależą od rozmiaru każdego pliku.

#### <a name="create-database-statement"></a>INSTRUKCJA TWORZENIE BAZY DANYCH

Następujące ograniczenia mają `CREATE DATABASE`zastosowanie do:

- Nie można zdefiniować plików i grup plików. 
- Opcja `CONTAINMENT` nie jest obsługiwana. 
- `WITH`opcje nie są obsługiwane. 
   > [!TIP]
   > Aby obejść ten `ALTER DATABASE` problem, należy użyć opcji `CREATE DATABASE` po dodaniu plików lub ustawieniu zamknięcia. 

- Opcja `FOR ATTACH` nie jest obsługiwana.
- Opcja `AS SNAPSHOT OF` nie jest obsługiwana.

Aby uzyskać więcej informacji, zobacz [TWORZENIE BAZY DANYCH](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrukcja ALTER DATABASE

Niektórych właściwości pliku nie można ustawić ani zmienić:

- Nie można określić ścieżki pliku `ALTER DATABASE ADD FILE (FILENAME='path')` w instrukcji T-SQL. Usuń `FILENAME` ze skryptu, ponieważ wystąpienie zarządzane automatycznie umieszcza pliki. 
- Nie można zmienić nazwy pliku `ALTER DATABASE` przy użyciu instrukcji.

Następujące opcje są ustawione domyślnie i nie można ich zmienić:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Nie można zmodyfikować następujących opcji:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Aby uzyskać więcej informacji, zobacz [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>Program SQL Server Agent

- Włączanie i wyłączanie programu SQL Server Agent nie jest obecnie obsługiwane w wystąpieniu zarządzanym. Agent SQL zawsze działa.
- Ustawienia programu SQL Server Agent są tylko do odczytu. Procedura `sp_set_agent_properties` nie jest obsługiwana w wystąpieniu zarządzanym. 
- Stanowiska
  - Obsługiwane są kroki zadania T-SQL.
  - Obsługiwane są następujące zadania replikacji:
    - Czytnik dzienników transakcji
    - Snapshot
    - Dystrybutor
  - Obsługiwane są kroki zadania SSIS.
  - Inne typy zadań nie są obecnie obsługiwane:
    - Krok zadania replikacji korespondencji seryjnej nie jest obsługiwany. 
    - Czytnik kolejki nie jest obsługiwany. 
    - Powłoka polecenia nie jest jeszcze obsługiwana.
  - Wystąpienia zarządzane nie mogą uzyskiwać dostępu do zasobów zewnętrznych, na przykład udziałów sieciowych za pośrednictwem roboboskopii. 
  - Usługi SQL Server Analysis Services nie są obsługiwane.
- Powiadomienia są częściowo obsługiwane.
- Powiadomienie e-mail jest obsługiwane, chociaż wymaga skonfigurowania profilu poczty bazy danych. Program SQL Server Agent może używać tylko jednego `AzureManagedInstance_dbmail_profile`profilu poczty bazy danych i musi być nazywany . 
  - Pager nie jest obsługiwany.
  - NetSend nie jest obsługiwany.
  - Alerty nie są jeszcze obsługiwane.
  - Serwery proxy nie są obsługiwane.
- EventLog nie jest obsługiwany.

Następujące funkcje programu SQL Agent obecnie nie są obsługiwane:

- Serwerów proxy
- Planowanie zadań na bezczynnym procesorze CPU
- Włączanie lub wyłączanie agenta
- Alerty

Aby uzyskać informacje na temat agenta programu SQL Server, zobacz [SQL Server Agent (Agent programu SQL Server)](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabele

Następujące typy tabel nie są obsługiwane:

- [Filestream](/sql/relational-databases/blob/filestream-sql-server)
- [Filetable](/sql/relational-databases/blob/filetables-sql-server)
- [TABELA ZEWNĘTRZNA](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (nie jest obsługiwana tylko w warstwie ogólnego przeznaczenia)

Aby uzyskać informacje dotyczące tworzenia i zmieniania tabel, zobacz [TWORZENIE TABELI](/sql/t-sql/statements/create-table-transact-sql) i [ZMIEŃ TABELĘ](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkcje

### <a name="bulk-insert--openrowset"></a>Wkładka zbiorcza / OPENROWSET

Wystąpienie zarządzane nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, więc pliki muszą zostać zaimportowane z magazynu obiektów Blob platformy Azure:

- `DATASOURCE`jest wymagane w `BULK INSERT` poleceniu podczas importowania plików z magazynu obiektów Blob platformy Azure. Zobacz [WKŁAD ZBIORCZY INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`jest wymagane w `OPENROWSET` funkcji podczas odczytywania zawartości pliku z magazynu obiektów Blob platformy Azure. Zobacz [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET`może służyć do odczytywania danych z innych pojedynczych baz danych azure SQL, wystąpień zarządzanych lub wystąpień programu SQL Server. Inne źródła, takie jak bazy danych Oracle lub pliki programu Excel, nie są obsługiwane.

### <a name="clr"></a>CLR

Wystąpienie zarządzane nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, dlatego obowiązują następujące ograniczenia:

- Obsługiwane `CREATE ASSEMBLY FROM BINARY` jest tylko. Zobacz [Tworzenie ASSEM BLY z pliku binary](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE`nie jest obsługiwany. Zobacz [Tworzenie złożenia z pliku](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY`nie można odwoływać się do plików. Patrz [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Poczta bazy danych (db_mail)
 - `sp_send_dbmail`nie może wysyłać załączników przy użyciu @file_attachments parametru. Lokalny system plików i udziały zewnętrzne lub usługa Azure Blob Storage nie są dostępne z tej procedury.
 - Zobacz znane problemy `@query` związane z parametrem i uwierzytelnianiem.
 
### <a name="dbcc"></a>Dbcc

Nieudokumentowane instrukcje DBCC, które są włączone w programie SQL Server nie są obsługiwane w wystąpieniach zarządzanych.

- Obsługiwane są tylko ograniczone flagi śledzenia globalnego. Poziom sesji `Trace flags` nie są obsługiwane. Zobacz [Śledzenie flag](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) i [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) współpracują z ograniczoną liczbą globalnych flag śledzenia.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) z opcjami REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST i REPAIR_REBUILD nie można użyć, ponieważ nie `SINGLE_USER` można ustawić bazy danych w trybie - patrz [różnice alter database](#alter-database-statement). Potencjalne uszkodzenia bazy danych są obsługiwane przez zespół pomocy technicznej platformy Azure. Skontaktuj się z pomocą techniczną platformy Azure, jeśli zauważysz uszkodzenie bazy danych, które powinno zostać naprawione.

### <a name="distributed-transactions"></a>Transakcje rozproszone

MSDTC i [elastyczne transakcje](sql-database-elastic-transactions-overview.md) obecnie nie są obsługiwane w wystąpieniach zarządzanych.

### <a name="extended-events"></a>Rozszerzone zdarzenia

Niektóre obiekty docelowe specyficzne dla systemu Windows dla zdarzeń rozszerzonych (XEvents) nie są obsługiwane:

- Obiekt `etw_classic_sync` docelowy nie jest obsługiwany. Przechowuj `.xel` pliki w magazynie obiektów Blob platformy Azure. Zobacz [etw_classic_sync celu](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Obiekt `event_file` docelowy nie jest obsługiwany. Przechowuj `.xel` pliki w magazynie obiektów Blob platformy Azure. Zobacz [event_file celu](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Biblioteki zewnętrzne

W bazie danych R i Python biblioteki zewnętrzne nie są jeszcze obsługiwane. Zobacz [USŁUGI UCZENIA MASZYNOWEGO PROGRAMU SQL Server](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Strumień plików i tabela plików

- Dane strumienia plików nie są obsługiwane.
- Baza danych nie może zawierać `FILESTREAM` grup plików z danymi.
- `FILETABLE`nie jest obsługiwany.
- Tabele nie `FILESTREAM` mogą mieć typów.
- Następujące funkcje nie są obsługiwane:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Aby uzyskać więcej informacji, zobacz [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) i [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Wyszukiwanie semantyczne pełnotekstowe

[Wyszukiwanie semantyczne](/sql/relational-databases/search/semantic-search-sql-server) nie jest obsługiwane.

### <a name="linked-servers"></a>Serwery połączone

Połączone serwery w wystąpieniach zarządzanych obsługują ograniczoną liczbę obiektów docelowych:

- Obsługiwane obiekty docelowe to wystąpienia zarządzane, pojedyncze bazy danych i wystąpienia programu SQL Server. 
- Serwery połączone nie obsługują transakcji podlegaalnych z podziałem (MS DTC).
- Obiekty docelowe, które nie są obsługiwane, to pliki, usługi Analysis Services i inne usługi RDBMS. Spróbuj użyć natywnego importu CSV z usługi Azure Blob Storage przy użyciu `BULK INSERT` lub `OPENROWSET` jako alternatywa dla importu plików.

Operacje

- Transakcje zapisu między wystąpieniami nie są obsługiwane.
- `sp_dropserver`jest obsługiwany w przypadku upuszczania połączonego serwera. Zobacz [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Funkcja `OPENROWSET` może służyć do wykonywania kwerend tylko w wystąpieniach programu SQL Server. Mogą być zarządzane lokalnie lub na maszynach wirtualnych. Zobacz [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- Funkcja `OPENDATASOURCE` może służyć do wykonywania kwerend tylko w wystąpieniach programu SQL Server. Mogą być zarządzane lokalnie lub na maszynach wirtualnych. Tylko `SQLNCLI`, `SQLNCLI11`i `SQLOLEDB` wartości są obsługiwane jako dostawca. Może to być na przykład `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zobacz [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Połączone serwery nie mogą być używane do odczytywania plików (Excel, CSV) z udziałów sieciowych. Spróbuj użyć [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) lub [OPENROWSET,](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) który odczytuje pliki CSV z usługi Azure Blob Storage. Śledzenie tych żądań w [elemencie Opinii wystąpienia zarządzanego](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Tabele zewnętrzne, które odwołują się do plików w pamięci HDFS lub usługi Azure Blob storage nie są obsługiwane. Aby uzyskać informacje o PolyBase, zobacz [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikacja

- Obsługiwane są typy replikacji dwukierunkowej i dwukierunkowej. Scalanie replikacji, replikacji równorzędnej i subskrypcji aktualizowanych nie są obsługiwane.
- [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md) jest dostępna w publicznej wersji zapoznawczej w wystąpieniu zarządzanym z pewnymi ograniczeniami:
    - Wszystkie typy uczestników replikacji (Wydawca, Dystrybutor, Pull Subskrybent i Push Subskrybent) mogą być umieszczane w wystąpieniach zarządzanych, ale wydawca i dystrybutor muszą być zarówno w chmurze, jak i lokalnie.
    - Wystąpienia zarządzane mogą komunikować się z najnowszymi wersjami programu SQL Server. Zobacz [macierz obsługiwanych wersji,](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems) aby uzyskać więcej informacji.
    - Replikacja transakcyjna ma pewne [dodatkowe wymagania sieciowe](sql-database-managed-instance-transactional-replication.md#requirements).

Aby uzyskać więcej informacji na temat konfigurowania replikacji transakcyjnej, zobacz następujące samouczki:
- [Replikacja między wydawcą MI a subskrybentem](replication-with-sql-database-managed-instance.md)
- [Replikacja między wydawcą MI, dystrybutorem MI i subskrybentem programu SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)

### <a name="restore-statement"></a>Instrukcja RESTORE 

- Obsługiwana składnia:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nieobsługiwała składnia:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Źródło: 
  - `FROM URL`(Usługa Azure Blob storage) jest jedyną obsługiwaną opcją.
  - `FROM DISK`/`TAPE`/backup urządzenie nie jest obsługiwane.
  - Zestawy kopii zapasowych nie są obsługiwane.
- `WITH`opcje nie są obsługiwane, takie `DIFFERENTIAL` `STATS`jak no lub .
- `ASYNC RESTORE`: Przywracanie jest kontynuowane, nawet jeśli połączenie klienta zostanie przerwane. Jeśli połączenie zostanie przerwane, `sys.dm_operation_status` można sprawdzić widok stanu operacji przywracania i bazy danych CREATE i DROP. Zobacz [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Następujące opcje bazy danych są ustawiane lub zastępowane i nie można ich później zmienić: 

- `NEW_BROKER`jeśli broker nie jest włączony w pliku .bak. 
- `ENABLE_BROKER`jeśli broker nie jest włączony w pliku .bak. 
- `AUTO_CLOSE=OFF`jeśli baza danych w pliku `AUTO_CLOSE=ON`.bak ma . 
- `RECOVERY FULL`jeśli baza danych w pliku `SIMPLE` `BULK_LOGGED` .bak ma lub tryb odzyskiwania.
- Grupa plików zoptymalizowana pod kątem pamięci jest dodawana i nazywana XTP, jeśli nie znajduje się w źródle pliku .bak. 
- Nazwa istniejącej grupy plików zoptymalizowanej pod kątem pamięci zostanie zmieniona na XTP. 
- `SINGLE_USER`i `RESTRICTED_USER` opcje są `MULTI_USER`konwertowane na .

Ograniczenia: 

- Kopie zapasowe uszkodzonych baz danych mogą zostać przywrócone w zależności od typu uszkodzenia, ale automatyczne kopie zapasowe nie zostaną wykonane, dopóki uszkodzenie nie zostanie naprawione. Upewnij się, `DBCC CHECKDB` że uruchomisz wystąpienie `WITH CHECKSUM` źródłowe i użyj kopii zapasowej, aby zapobiec temu problemowi.
- Przywracanie `.BAK` pliku bazy danych, który zawiera wszelkie ograniczenia opisane `FILESTREAM` `FILETABLE` w tym dokumencie (na przykład lub obiektów) nie można przywrócić na wystąpienie zarządzane.
- `.BAK`plików zawierających wiele zestawów kopii zapasowych nie można przywrócić. 
- `.BAK`plików zawierających wiele plików dziennika nie można przywrócić.
- Kopie zapasowe zawierające bazy danych o pojemności większej niż 8 TB, aktywne obiekty OLTP w pamięci lub liczbę plików, które przekraczałyby 280 plików na wystąpienie, nie mogą zostać przywrócone w wystąpieniu ogólnego przeznaczenia. 
- Kopie zapasowe zawierające bazy danych o pojemności większej niż 4 TB lub obiekty OLTP w pamięci o całkowitym rozmiarze większym niż rozmiar opisany w [limitach zasobów](sql-database-managed-instance-resource-limits.md) nie mogą zostać przywrócone w wystąpieniu Krytyczna dla firmy.
Aby uzyskać informacje na temat przywracania instrukcji, zobacz [instrukcje przywracania](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Te same ograniczenia dotyczą wbudowanej operacji przywracania punktu w czasie. Na przykład bazy danych ogólnego przeznaczenia większej niż 4 TB nie można przywrócić w wystąpieniu krytyczne dla firmy. Baza danych o znaczeniu krytycznym dla firmy z plikami OLTP w pamięci lub więcej niż 280 plików nie może zostać przywrócona w wystąpieniu ogólnego przeznaczenia.

### <a name="service-broker"></a>Broker usług

Broker usług między wystąpieniami nie jest obsługiwany:

- `sys.routes`: Jako warunek wstępny należy wybrać adres z pliku sys.routes. Adres musi być lokalny na każdej trasie. Zobacz [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Nie można `CREATE ROUTE` używać `ADDRESS` z `LOCAL`innymi niż . Zobacz [TWORZENIE TRASY](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Nie można `ALTER ROUTE` używać `ADDRESS` z `LOCAL`innymi niż . Zobacz [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedury przechowywane, funkcje i wyzwalacze

- `NATIVE_COMPILATION`nie jest obsługiwana w warstwie ogólnego przeznaczenia.
- Następujące opcje [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) nie są obsługiwane: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`nie jest obsługiwany. Zobacz [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell`nie jest obsługiwany. Zobacz [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`nie są obsługiwane, co `sp_addextendedproc`   `sp_dropextendedproc`obejmuje i . Zobacz [Rozszerzone procedury przechowywane](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`i `sp_detach_db` nie są obsługiwane. Zobacz [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)i [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Funkcje i zmienne systemu

Następujące zmienne, funkcje i widoki zwracają różne wyniki:

- `SERVERPROPERTY('EngineEdition')`zwraca wartość 8. Ta właściwość jednoznacznie identyfikuje wystąpienie zarządzane. Zobacz [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`zwraca wartość NULL, ponieważ koncepcja wystąpienia w stanie takim, w jakim istnieje dla programu SQL Server, nie ma zastosowania do wystąpienia zarządzanego. Zobacz [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`zwraca pełną nazwę DNS "connectable", na przykład my-managed-instance.wcus17662feb9ce98.database.windows.net. Zobacz [@SERVERNAME@](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`zwraca pełną nazwę DNS "connectable", `myinstance.domain.database.windows.net` taką jak właściwości "name" i "data_source". Zobacz [SYS. SERWERÓW](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`zwraca wartość NULL, ponieważ pojęcie usługi w stanie takim, w jakim istnieje dla programu SQL Server, nie ma zastosowania do wystąpienia zarządzanego. Zobacz [@SERVICENAME@](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID`jest obsługiwana. Zwraca wartość NULL, jeśli logowania usługi Azure AD nie jest w sys.syslogins. Zobacz [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID`nie jest obsługiwany. Zwracane są nieprawidłowe dane, co jest tymczasowym znanym problemem. Zobacz [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Ograniczenia środowiskowe

### <a name="subnet"></a>Podsieć
-  Nie można umieścić żadnych innych zasobów (na przykład maszyn wirtualnych) w podsieci, w której wdrożono wystąpienie zarządzane. Wdrażanie tych zasobów przy użyciu innej podsieci.
- Podsieć musi mieć wystarczającą liczbę dostępnych [adresów IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Minimum to 16, podczas gdy zalecenie ma mieć co najmniej 32 adresy IP w podsieci.
- [Nie można skojarzyć punktów końcowych usługi z podsiecią wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Upewnij się, że opcja punktów końcowych usługi jest wyłączona podczas tworzenia sieci wirtualnej.
- Liczba elementów wirtualnych i typów wystąpień, które można wdrożyć w regionie, ma pewne [ograniczenia i limity](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- Istnieją pewne [reguły zabezpieczeń, które muszą być stosowane w podsieci](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>Sieci wirtualnej
- Sieci wirtualnej można wdrożyć przy użyciu modelu zasobów — model klasyczny dla sieci wirtualnej nie jest obsługiwany.
- Po utworzeniu wystąpienia zarządzanego przeniesienie wystąpienia zarządzanego lub sieci wirtualnej do innej grupy zasobów lub subskrypcji nie jest obsługiwane.
- Niektóre usługi, takie jak środowiska usługi app service, aplikacje logiki i wystąpienia zarządzane (używane do replikacji geograficznej, replikacji transakcyjnej lub za pośrednictwem połączonych serwerów) nie mogą uzyskać dostępu do wystąpień zarządzanych w różnych regionach, jeśli ich sieci wirtualne są połączone przy użyciu [globalnej komunikacji równorzędnej](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Można połączyć się z tymi zasobami za pośrednictwem usługi ExpressRoute lub sieci wirtualnej do sieci wirtualnej za pośrednictwem bram sieci wirtualnej.

### <a name="tempdb"></a>Tempdb

Maksymalny rozmiar `tempdb` pliku nie może być większy niż 24 GB na rdzeń w warstwie ogólnego przeznaczenia. Maksymalny `tempdb` rozmiar warstwy Krytyczna dla firmy jest ograniczony przez rozmiar magazynu wystąpienia. `Tempdb`rozmiar pliku dziennika jest ograniczony do 120 GB w warstwie ogólnego przeznaczenia. Niektóre kwerendy mogą zwracać błąd, jeśli potrzebują więcej `tempdb` niż 24 GB na rdzeń lub jeśli generują więcej niż 120 GB danych dziennika.

### <a name="msdb"></a>Msdb

Następujące schematy MSDB w wystąpieniu zarządzanym muszą być własnością ich odpowiednich wstępnie zdefiniowanych ról:

- Role ogólne
  - TargetServersRole (Grupa docelowa)
- [Stałe role bazy danych](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Role Poczty Bazowej:](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile)
  - Databasemailuserrole
- [Role usług integracyjnych:](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15)
  - Db_ssisadmin
  - Db_ssisltduser
  - Db_ssisoperator
  
> [!IMPORTANT]
> Zmiana wstępnie zdefiniowanych nazw ról, nazw schematów i właścicieli schematów przez klientów wpłynie na normalne działanie usługi. Wszelkie zmiany wprowadzone w tych zostaną przywrócone do wstępnie zdefiniowanych wartości, jak tylko wykryte lub w następnej aktualizacji usługi najpóźniej w celu zapewnienia normalnej pracy usługi.

### <a name="error-logs"></a>Dzienniki błędów

Wystąpienie zarządzane umieszcza pełne informacje w dziennikach błędów. Istnieje wiele wewnętrznych zdarzeń systemowych, które są rejestrowane w dzienniku błędów. Użyj procedury niestandardowej, aby odczytać dzienniki błędów, które odfiltrowuje niektóre nieistotne wpisy. Aby uzyskać więcej informacji, zobacz [wystąpienie zarządzane — sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) lub [rozszerzenie wystąpienia zarządzanego(wersja zapoznawcza)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) dla usługi Azure Data Studio.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat wystąpień zarządzanych, zobacz [Co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
- Aby uzyskać funkcje i listę porównawczą, zobacz [porównanie funkcji usługi Azure SQL Database](sql-database-features.md).
- Aby uzyskać informacje o aktualizacjach wersji i stanie znanych problemów, zobacz [informacje o wersji bazy danych SQL](sql-database-release-notes.md)
- Aby uzyskać przewodnik Szybki start, który pokazuje, jak utworzyć nowe wystąpienie zarządzane, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
