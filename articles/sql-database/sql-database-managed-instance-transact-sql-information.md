---
title: Różnice w usługi Azure SQL Database zarządzane wystąpienia języka T-SQL | Dokumentacja firmy Microsoft
description: W tym artykule omówiono różnice języka T-SQL wystąpienia zarządzanego usługi Azure SQL Database i programu SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 08920a25fc7213a773ef0d76a5daddbab3f765c2
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866863"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Różnice w usługi Azure SQL Database zarządzane wystąpienia języka T-SQL z programu SQL Server

Ten artykuł zawiera podsumowanie i wyjaśnia różnice w składnią i zachowaniem między wystąpienia zarządzanego Azure SQL Database i aparatu bazy danych serwera SQL w środowisku lokalnym. Omówiono następujące zagadnienia: <a name="Differences"></a>

- [Dostępność](#availability) zawiera różnice w [zawsze włączonej](#always-on-availability) i [kopie zapasowe](#backup).
- [Zabezpieczenia](#security) zawiera różnice w [inspekcji](#auditing), [certyfikaty](#certificates), [poświadczenia](#credential), [dostawcy usług kryptograficznych](#cryptographic-providers), [identyfikatory logowania i użytkowników](#logins-and-users)i [klucza usługi i klucz główny usługi](#service-key-and-service-master-key).
- [Konfiguracja](#configuration) zawiera różnice w [buforu rozszerzenia puli](#buffer-pool-extension), [sortowania](#collation), [poziomy zgodności](#compatibility-levels), [dublowania bazy danych ](#database-mirroring), [opcje bazy danych](#database-options), [programu SQL Server Agent](#sql-server-agent), i [Opcje tabeli](#tables).
- [Funkcje](#functionalities) obejmuje [ZBIORCZEGO WSTAWIANIA/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transakcje rozproszone](#distributed-transactions), [zdarzeniom rozszerzonym](#extended-events), [zewnętrznych bibliotekach](#external-libraries), [filestream i FileTable](#filestream-and-filetable), [pełnotekstowe wyszukiwanie semantyczne](#full-text-semantic-search), [serwery połączone](#linked-servers), [PolyBase](#polybase), [replikacji](#replication), [PRZYWRÓCIĆ](#restore-statement), [programu Service Broker](#service-broker), [procedury składowane, funkcje i wyzwalacze](#stored-procedures-functions-and-triggers).
- [Funkcje, które mają różne zachowanie w zarządzane wystąpienia](#Changes).
- [Tymczasowe ograniczenia i znane problemy](#Issues).

Opcji wdrożenia wystąpienia zarządzanego zapewnia wysoką zgodność z aparatem bazy danych serwera SQL w środowisku lokalnym. Większość funkcje aparatu bazy danych programu SQL Server są obsługiwane w wystąpieniu zarządzanym.

![Migracja](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Dostępność

### <a name="always-on-availability"></a>Zawsze włączone

[Wysoka dostępność](sql-database-high-availability.md) jest wbudowana w wystąpieniu zarządzanym i nie mogą być kontrolowane przez użytkowników. Poniższe instrukcje nie są obsługiwane:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [UTWÓRZ GRUPĘ DOSTĘPNOŚCI](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [POLECENIA ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [GRUPA DOSTĘPNOŚCI LISTY](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) klauzuli [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) — instrukcja

### <a name="backup"></a>Backup

Wystąpienia zarządzanego mają automatycznych kopii zapasowych, dzięki czemu użytkownicy mogą tworzyć pełne kopie `COPY_ONLY` kopii zapasowych. Różnicowe kopie zapasowe, log oraz kopii zapasowych migawki pliku nie są obsługiwane.

- Za pomocą wystąpienia zarządzanego kopii zapasowych wystąpienia bazy danych tylko do konta usługi Azure Blob storage:
  - Tylko `BACKUP TO URL` jest obsługiwana.
  - `FILE`, `TAPE`, a urządzenia kopii zapasowej nie są obsługiwane.
- Większość ogólnych `WITH` opcje są obsługiwane.
  - `COPY_ONLY` jest wymagany.
  - `FILE_SNAPSHOT` nie jest obsługiwane.
  - Opcje taśmy: `REWIND`, `NOREWIND`, `UNLOAD`, i `NOUNLOAD` nie są obsługiwane.
  - Opcje dziennika: `NORECOVERY`, `STANDBY`, i `NO_TRUNCATE` nie są obsługiwane.

Ograniczenia: 

- Za pomocą wystąpienia zarządzanego, kopii zapasowych wystąpienia bazy danych w kopii zapasowej z maksymalnie 32 rozkłada, który jest wystarczająca dla baz danych do 4 TB, jeśli kompresja kopii zapasowej jest używany.
- Rozmiar maksymalny kopii zapasowej woluminu rozłożonego przy użyciu `BACKUP` polecenie w wystąpienia zarządzanego jest 195 GB, czyli rozmiar maksymalny obiektu blob. Zwiększ liczbę paski w kopii zapasowej polecenie, aby zmniejszyć rozmiar woluminu rozłożonego indywidualnych i w ramach tego limitu.

    > [!TIP]
    > Aby obejść to ograniczenie, podczas wykonywania kopii zapasowej bazy danych z dowolnego programu SQL Server w środowisku lokalnym lub na maszynie wirtualnej, możesz wykonywać następujące czynności:
    >
    > - Wykonaj kopię zapasową `DISK` zamiast kopie zapasowe na `URL`.
    > - Przekaż pliki kopii zapasowej do magazynu obiektów Blob.
    > - Przywróć do wystąpienia zarządzanego.
    >
    > `Restore` Polecenia w wystąpieniu zarządzanym obsługuje większych rozmiarów obiektów blob w pliki kopii zapasowej, ponieważ typ inny obiektu blob jest używany do przechowywania przekazywanych plików kopii zapasowej.

Aby uzyskać informacji na temat kopii zapasowych przy użyciu języka T-SQL, zobacz [kopii zapasowej](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Bezpieczeństwo

### <a name="auditing"></a>Inspekcja

Podstawowe różnice między inspekcji w bazach danych Azure SQL Database i baz danych programu SQL Server są:

- Za pomocą opcji wdrożenia wystąpienia zarządzanego usługi Azure SQL Database inspekcja działa na poziomie serwera. `.xel` Pliki dziennika są przechowywane w usłudze Azure Blob storage.
- Korzystając z pojedynczą bazę danych i pul elastycznych opcji wdrażania w usłudze Azure SQL Database inspekcja działa na poziomie bazy danych.
- W programie SQL Server w środowisku lokalnym lub maszyn wirtualnych inspekcji działa na poziomie serwera. Zdarzenia są przechowywane w systemie plików lub Windows, dzienniki zdarzeń.
 
Inspekcji systemu XEvent, w wystąpieniu zarządzanym obsługuje cele usługi Azure Blob storage. Plik i Windows, dzienniki nie są obsługiwane.

Klucz różnice w `CREATE AUDIT` składnia inspekcji w usłudze Azure Blob storage są:

- Nowa składnia `TO URL` pod warunkiem, że można użyć, aby określić adres URL kontenera magazynu obiektów Blob Azure gdzie `.xel` pliki zostaną umieszczone.
- Składnia `TO FILE` nie jest obsługiwana, ponieważ wystąpienia zarządzanego nie można uzyskać dostępu do udziałów plików w Windows.

Aby uzyskać więcej informacji, zobacz: 

- [TWORZENIE INSPEKCJI SERWERA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [INSTRUKCJA ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Inspekcja](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certyfikaty

Wystąpienia zarządzanego nie można uzyskać dostępu udziałów plików i folderów Windows, więc obowiązują następujące ograniczenia:

- `CREATE FROM` / `BACKUP TO` Plik nie jest obsługiwany w przypadku certyfikatów.
- `CREATE` / `BACKUP` Certyfikat od `FILE` / `ASSEMBLY` nie jest obsługiwane. Nie można użyć plików kluczy prywatnych. 

Zobacz [Utwórz certyfikat](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) i [kopii zapasowej certyfikatu](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Obejście**: Skrypt dla certyfikatu lub klucza prywatnego, zapisać jako plik SQL i tworzenie na podstawie pliku binarnego:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Poświadczenie

Tylko usługi Azure Key Vault i `SHARED ACCESS SIGNATURE` tożsamości są obsługiwane. Windows, użytkownicy nie są obsługiwane.

Zobacz [Utwórz POŚWIADCZENIA](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) i [POŚWIADCZEŃ ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Dostawcy usług kryptograficznych

Wystąpienia zarządzanego nie można uzyskać dostęp do plików, więc nie można utworzyć dostawcy usług kryptograficznych:

- `CREATE CRYPTOGRAPHIC PROVIDER` nie jest obsługiwane. Zobacz [dostawcy usług KRYPTOGRAFICZNYCH Utwórz](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` nie jest obsługiwane. Zobacz [dostawcy usług KRYPTOGRAFICZNYCH ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Nazwy logowania i użytkownicy

- Utworzone za pomocą nazw logowania SQL `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, i `FROM SID` są obsługiwane. Zobacz [logowania Utwórz](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Usługa Azure Active Directory (Azure AD) serwera podmiotów zabezpieczeń (logowania) utworzone za pomocą [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) składni lub [utworzyć z LOGOWANIEM użytkownika [Azure AD Login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) składni są obsługiwane (publiczna wersja zapoznawcza). Te identyfikatory logowania są tworzone na poziomie serwera.

    Wystąpienie zarządzane obsługuje podmiotów zabezpieczeń bazy danych usługi Azure AD przy użyciu składni `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Ta funkcja jest nazywana usługi Azure AD zawartych użytkowników bazy danych.

- Logowania Windows utworzonych za pomocą `CREATE LOGIN ... FROM WINDOWS` składni nie są obsługiwane. Za pomocą usługi Azure Active Directory, logowania i użytkowników.
- Użytkownik usługi Azure AD, który utworzył wystąpienie ma [nieograniczone uprawnienia administratora](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Użytkownicy poziomu bazy danych niebędącego administratorem usługi Azure AD można utworzyć za pomocą `CREATE USER ... FROM EXTERNAL PROVIDER` składni. Zobacz [Utwórz użytkownika... Z ZEWNĘTRZNEGO DOSTAWCY](sql-database-manage-logins.md#non-administrator-users).
- Jednostki serwera w usłudze Azure AD (logowania) obsługują funkcje SQL w ramach tylko jedno wystąpienie zarządzane. Funkcje, które wymagają interakcji między wystąpienia, niezależnie od tego, czy są one w tej samej usłudze Azure AD dzierżawy lub różnych dzierżawach, nie są obsługiwane dla użytkowników usługi Azure AD. Przykłady takich funkcji:

  - Replikacja transakcyjna programu SQL.
  - Serwer łączy.

- Ustawienia logowania usługi Azure AD mapowane na grupę usługi Azure AD, jako właściciel bazy danych nie jest obsługiwane.
- Personifikacja głównych poziomu serwera usługi Azure AD przy użyciu innych podmiotów zabezpieczeń usługi Azure AD jest obsługiwany, takich jak [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) klauzuli. Dostępne są następujące ograniczenia EXECUTE AS:

  - EXECUTE AS USER nie jest obsługiwane dla użytkowników usługi Azure AD, gdy nazwa różni się od nazwy logowania. Przykładem jest, gdy użytkownik zostanie utworzony przy użyciu składni CREATE USER [myAadUser] z logowania [john@contoso.com] i próby personifikacji za pośrednictwem EXEC AS USER = _myAadUser_. Po utworzeniu **użytkownika** z usługi Azure AD podmiot zabezpieczeń serwera (identyfikator logowania), należy określić nazwa_użytkownika jako ten sam login_name z **logowania**.
  - Tylko dla programu SQL Server na poziomie nazw głównych (logowania), które są częścią `sysadmin` roli można wykonywać następujące operacje, przeznaczonych dla podmiotów zabezpieczeń usługi Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Ograniczenia publicznej wersji zapoznawczej dla podmiotów zabezpieczeń serwera usługi Azure AD (logowania):

  - Active Directory ograniczenia administratora dla wystąpienia zarządzanego:

    - Administrator usługi Azure AD umożliwia skonfigurowanie wystąpienia zarządzanego nie może służyć do tworzenia usługi Azure AD podmiot zabezpieczeń serwera (identyfikator logowania) w ramach wystąpienia zarządzanego. Należy utworzyć pierwszy Azure podmiot zabezpieczeń serwera usługi AD (identyfikator logowania) za pomocą konta programu SQL Server to `sysadmin` roli. To tymczasowe ograniczenie zostaną usunięte po podmiotów zabezpieczeń serwera usługi Azure AD (logowania) stanie się ogólnie dostępna. Jeśli spróbujesz utworzyć identyfikator logowania przy użyciu konta administratora usługi Azure AD, zostanie wyświetlony następujący błąd: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Obecnie, należy utworzyć pierwszego logowania usługi Azure AD, utworzone w bazie danych master przy użyciu konta programu SQL Server standard (spoza platformy Azure AD) to `sysadmin` roli przy użyciu [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) z zewnętrznego dostawcy. Po ogłoszeniu ogólnej dostępności to ograniczenie zostanie usunięte. Możesz utworzyć początkową logowania usługi Azure AD przy użyciu administratora usługi Active Directory dla wystąpienia zarządzanego.
    - DacFx (importu/eksportu), używany przy użyciu programu SQL Server Management Studio lub SqlPackage nie jest obsługiwane w przypadku logowania do usługi Azure AD. To ograniczenie zostanie usunięty po podmiotów zabezpieczeń serwera usługi Azure AD (logowania) stanie się ogólnie dostępna.
    - Używanie podmiotów zabezpieczeń serwera usługi Azure AD (logowania) za pomocą programu SQL Server Management Studio:

      - Obsługa skryptów logowania do usługi Azure AD, korzystających z dowolnym uwierzytelnionym logowania nie jest obsługiwane.
      - Funkcja IntelliSense nie rozpoznaje instrukcji tworzenia z zewnętrznego dostawcy logowania i pokazuje czerwoną linią.

- Tylko poziomu serwera głównego identyfikatora logowania, który jest tworzony przez wystąpienie zarządzane, inicjowanie obsługi administracyjnej procesu, członkowie ról serwera, takie jak `securityadmin` lub `sysadmin`, lub innych nazw logowania z uprawnieniami ALTER ANY LOGIN na poziomie serwera można tworzyć usługi Azure AD Serwer podmiotów zabezpieczeń (logowania) w bazie danych master dla wystąpienia zarządzanego.
- Jeśli ta nazwa logowania jest podmiotem zabezpieczeń programu SQL, tylko nazwy logowania. które są częścią `sysadmin` roli Utwórz polecenie służy do tworzenia identyfikatorów logowania dla konta usługi Azure AD.
- Logowania usługi Azure AD, musi należeć do usługi Azure AD, w tym samym katalogu, który służy do wystąpienia zarządzanego Azure SQL Database.
- Jednostki serwera w usłudze Azure AD (logowania) są widoczne w Eksploratorze obiektów, począwszy od programu SQL Server Management Studio w wersjach 18.0 w wersji zapoznawczej 5.
- Nakładające się podmiotów serwera usługi Azure AD (logowania) przy użyciu konta administratora usługi Azure AD jest dozwolone. Jednostki serwera w usłudze Azure AD (logowania) mają pierwszeństwo przed administrator usługi Azure AD podczas rozwiązywania podmiot zabezpieczeń i zastosować uprawnienia do wystąpienia zarządzanego.
- Podczas uwierzytelniania następująca sekwencja jest stosowany do rozwiązania jednostki uwierzytelniania:

    1. Istnienia konta usługi Azure AD jako bezpośrednio mapowany do usługi Azure AD podmiot zabezpieczeń serwera (identyfikator logowania), który jest obecny w sys.server_principals jako typ "E", udzielanie dostępu i zastosuj uprawnienia podmiotu zabezpieczeń serwera usługi Azure AD (identyfikator logowania).
    2. Jeśli konto usługi Azure AD jest członkiem grupy usługi Azure AD, który jest mapowany do usługi Azure AD podmiot zabezpieczeń serwera (identyfikator logowania), który jest obecny w sys.server_principals jako typu "X", przyznać dostęp i zastosuj uprawnienia logowania grupy usługi Azure AD.
    3. Jeśli konto usługi Azure AD jest specjalny skonfigurowane w portalu administratora usługi Azure AD dla wystąpienia zarządzanego, który nie istnieje w widokach systemu w wystąpieniu zarządzanym, mają zastosowanie specjalnych stałych uprawnień administratora usługi Azure AD dla wystąpienia zarządzanego (starsza wersja trybu).
    4. Jeśli istnieje konto usługi Azure AD jako bezpośrednio zmapowane do użytkownika usługi Azure AD w bazie danych, który jest obecny w sys.database_principals jako typ "E", przyznać dostęp i zastosuj uprawnienia użytkownika bazy danych usługi Azure AD.
    5. Jeśli konto usługi Azure AD jest członkiem grupy usługi Azure AD, który jest mapowany do użytkownika usługi Azure AD w bazie danych, który jest obecny w sys.database_principals jako typ "X", przyznać dostęp i zastosuj uprawnienia logowania grupy usługi Azure AD.
    6. W przypadku logowania usługi Azure AD zamapowanych na konto użytkownika usługi Azure AD lub konta grupy usługi Azure AD, który jest rozpoznawany jako użytkownik, który jest uwierzytelniany, są stosowane wszystkie uprawnienia z tymi danymi logowania usługi Azure AD.

### <a name="service-key-and-service-master-key"></a>Usługa klucza głównego klucza i usługi

- [Kopię zapasową klucza głównego](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).
- [Przywróć klucz główny](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).
- [Kopię zapasową klucza głównego usługi](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).
- [Przywróć klucz główny usługi](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).

## <a name="configuration"></a>Konfigurowanie

### <a name="buffer-pool-extension"></a>Rozszerzenie puli buforów

- [Rozszerzenie puli bufora](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nie jest obsługiwane.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` nie jest obsługiwane. Zobacz [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Sortowanie

Domyślnym sortowaniem wystąpienia jest `SQL_Latin1_General_CP1_CI_AS` i może być określony jako parametr tworzenia. Zobacz [sortowania](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Poziomy zgodności

- Poziomy zgodności obsługiwane są 100, 110, 120, 130 i 140.
- Poziomy zgodności poniżej 100 nie są obsługiwane.
- Domyślny poziom zgodności dla nowych baz danych jest 140. W przypadku przywróconych baz danych poziom zgodności pozostaje niezmieniony, jeśli był 100 lub nowszym.

Zobacz [poziom zgodności bazy danych ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Dublowanie bazy danych

Funkcja dublowania bazy danych nie jest obsługiwane.

- `ALTER DATABASE SET PARTNER` i `SET WITNESS` opcje nie są obsługiwane.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` nie jest obsługiwane.

Aby uzyskać więcej informacji, zobacz [ALTER DATABASE SET PARTNER i SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) i [CREATE ENDPOINT... Aby UZYSKAĆ DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opcje bazy danych

- Wiele plików dziennika nie są obsługiwane.
- Obiekty w pamięci nie są obsługiwane w przypadku warstwy usług ogólnego przeznaczenia. 
- Ma limitu 280 plików ogólnego przeznaczenia wystąpienia, co oznacza maksymalnie 280 plików na bazę danych. Pliki dziennika i danych w warstwie przeznaczenie ogólne są wliczane do tego limitu. [Obsługuje pliki 32 767 na bazę danych o warstwie krytyczne dla działania firmy](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Baza danych nie mogą zawierać grup plików, które zawierają dane filestream. Przywracanie nie powiodło się, jeśli zawiera .bak `FILESTREAM` danych. 
- Każdy plik zostanie umieszczony w usłudze Azure Blob storage. We/Wy i przepływność na pliku są zależne od rozmiaru każdego pliku.

#### <a name="create-database-statement"></a>Instrukcja CREATE DATABASE

Poniższe ograniczenia mają zastosowanie do `CREATE DATABASE`:

- Nie można zdefiniować pliki i grupy plików. 
- `CONTAINMENT` Opcja nie jest obsługiwana. 
- `WITH` Opcje nie są obsługiwane. 
   > [!TIP]
   > Jako obejście tego problemu, należy użyć `ALTER DATABASE` po `CREATE DATABASE` można ustawić opcji bazy danych, aby dodać pliki lub ustawić zawierania. 

- `FOR ATTACH` Opcja nie jest obsługiwana.
- `AS SNAPSHOT OF` Opcja nie jest obsługiwana.

Aby uzyskać więcej informacji, zobacz [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrukcja ALTER DATABASE

Niektóre właściwości pliku nie można ustawić lub zmienić:

- Nie można określić ścieżkę do pliku w `ALTER DATABASE ADD FILE (FILENAME='path')` instrukcję języka T-SQL. Usuń `FILENAME` ze skryptu, ponieważ wystąpienie zarządzane automatycznie umieszcza pliki. 
- Nie można zmienić nazwy pliku przy użyciu `ALTER DATABASE` instrukcji.

Poniższe opcje są domyślnie i nie można zmienić:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Nie można zmodyfikować następujące opcje:

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

Aby uzyskać więcej informacji, zobacz [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>Program SQL Server Agent

- Ustawienia programu SQL Server Agent jest tylko do odczytu. Procedura `sp_set_agent_properties` nie jest obsługiwana w wystąpieniu zarządzanym. 
- Stanowiska
  - Kroki w zadaniu języka T-SQL są obsługiwane.
  - Obsługiwane są następujące zadania replikacji:
    - Czytnik dziennika transakcji
    - Snapshot
    - Dystrybutor
  - Kroki zadania SSIS są obsługiwane.
  - Inne rodzaje kroków zadania nie są obecnie obsługiwane:
    - Krok zadania replikacji scalającej nie jest obsługiwane. 
    - Czytnik kolejki nie jest obsługiwane. 
    - Powłoka poleceń nie jest jeszcze obsługiwana.
  - Wystąpienia zarządzanego nie może uzyskiwać dostęp do zasobów zewnętrznych, na przykład udziałach sieciowych za pomocą narzędzia robocopy. 
  - SQL Server Analysis Services nie są obsługiwane.
- Powiadomienia są obsługiwane częściowo.
- Powiadomienie e-mail jest obsługiwany, mimo że wymaga, aby skonfigurować profil poczty bazy danych. Agent programu SQL Server można użyć tylko jeden profil poczty bazy danych, a musi być wywołana `AzureManagedInstance_dbmail_profile`. 
  - Pagera nie jest obsługiwane. 
  - NetSend nie jest obsługiwane.
  - Alerty nie są jeszcze obsługiwane.
  - Serwery proxy nie są obsługiwane. 
- Dziennik zdarzeń nie jest obsługiwane.

Obecnie następujące funkcje nie są obsługiwane, ale zostaną włączone w przyszłości:

- Serwery proxy
- Planowanie zadań w stanie bezczynności procesora CPU
- Włączanie lub wyłączanie agenta
- Alerty

Aby uzyskać informacje na temat agenta programu SQL Server, zobacz [SQL Server Agent (Agent programu SQL Server)](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabele

Następujące tabele nie są obsługiwane:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

Aby uzyskać informacje o tym, jak utworzyć i zmienić tabel, zobacz [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) i [instrukcji ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkcje

### <a name="bulk-insert--openrowset"></a>Wstawianie zbiorcze / openrowset

Wystąpienia zarządzanego nie można uzyskać dostępu udziałów plików i folderów Windows, więc należy importować pliki z usługi Azure Blob storage:

- `DATASOURCE` jest wymagany w `BULK INSERT` poleceń podczas importowania plików z magazynu obiektów Blob platformy Azure. Zobacz [WSTAWIANIA ZBIORCZEGO](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` jest wymagany w `OPENROWSET` działają w przypadku odczytać zawartość pliku z usługi Azure Blob storage. Zobacz [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Wystąpienia zarządzanego nie można uzyskać dostępu udziałów plików i folderów Windows, więc obowiązują następujące ograniczenia:

- Tylko `CREATE ASSEMBLY FROM BINARY` jest obsługiwana. Zobacz [tworzenia zestawu z danych](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` nie jest obsługiwane. Zobacz [tworzenie zestawów z pliku](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` Nie można odwoływać się do plików. Zobacz [zmiana zestawu](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Nieudokumentowany instrukcji DBCC, które są włączone w programie SQL Server nie są obsługiwane w wystąpieniach zarządzanych.

- `Trace flags` nie są obsługiwane. Zobacz [flagi śledzenia](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` nie jest obsługiwane. Zobacz [polecenia DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` nie jest obsługiwane. Zobacz [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transakcje rozproszone

Usługa MSDTC i [transakcje elastyczne](sql-database-elastic-transactions-overview.md) nie są obecnie obsługiwane w wystąpieniach zarządzanych.

### <a name="extended-events"></a>Rozszerzone zdarzenia

Niektóre cele Windows specyficznych dla Extended Events (XEvents) nie są obsługiwane:

- `etw_classic_sync` Element docelowy nie jest obsługiwany. Store `.xel` plików w usłudze Azure Blob storage. Zobacz [docelowej etw_classic_sync](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file` Element docelowy nie jest obsługiwany. Store `.xel` plików w usłudze Azure Blob storage. Zobacz [docelowej event_file](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Zewnętrzne biblioteki

W bazie danych języków R i Python, zewnętrznych bibliotekach nie są jeszcze obsługiwane. Zobacz [programu SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FILESTREAM i FileTable

- danych FILESTREAM nie jest obsługiwane.
- Baza danych nie mogą zawierać grup plików przy użyciu `FILESTREAM` danych.
- `FILETABLE` nie jest obsługiwane.
- Tabele nie mogą mieć `FILESTREAM` typów.
- Następujące funkcje nie są obsługiwane:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Aby uzyskać więcej informacji, zobacz [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) i [Filetable](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Semantyczne wyszukiwanie pełnotekstowe

[Wyszukiwanie semantyczne](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) nie jest obsługiwane.

### <a name="linked-servers"></a>Serwery połączone

Połączone serwery w wystąpieniach zarządzanych obsługują ograniczoną liczbę elementów docelowych:

- Obsługiwane obiekty docelowe są programu SQL Server i bazy danych SQL.
- Obiekty docelowe, które nie są obsługiwane są pliki, usługi Analysis Services i inne RDBMS.

Operacje

- Transakcje zapisu dla wielu wystąpień nie są obsługiwane.
- `sp_dropserver` jest obsługiwana dla porzucenie połączonego serwera. Zobacz [serwera sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` Funkcja może służyć do wykonywania zapytań tylko w wystąpieniach programu SQL Server. Mogą być zarządzane lokalnie, lub na maszynach wirtualnych. Zobacz [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` Funkcja może służyć do wykonywania zapytań tylko w wystąpieniach programu SQL Server. Mogą być zarządzane lokalnie, lub na maszynach wirtualnych. Tylko `SQLNCLI`, `SQLNCLI11`, i `SQLOLEDB` wartości są obsługiwane jako dostawca. Może to być na przykład `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zobacz [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

Zewnętrzne tabel, które odwołują, plików w systemie plików HDFS lub Azure Blob storage nie są obsługiwane. Aby uzyskać informacji na temat technologii PolyBase, zobacz [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikacja

Replikacja jest dostępna w publicznej wersji zapoznawczej dla wystąpienia zarządzanego. Aby uzyskać informacji o replikacji, zobacz [replikacji programu SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>Przywracanie — instrukcja 

- Obsługiwanej składni:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nieobsługiwana składnia:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Źródło: 
  - `FROM URL` (Magazyn obiektów Blob platformy azure) jest jedyną obsługiwaną opcją.
  - `FROM DISK`/`TAPE`/ urządzenie kopii zapasowej nie jest obsługiwane.
  - Zestawy kopii zapasowych nie są obsługiwane.
- `WITH` Opcje nie są obsługiwane, takich jak nie `DIFFERENTIAL` lub `STATS`.
- `ASYNC RESTORE`: Restore będzie kontynuowane, nawet w przypadku zerwania połączenia klienta. Jeśli połączenie zostanie porzucone, możesz sprawdzić `sys.dm_operation_status` widok stanu operacji przywracania, a następnie utwórz i docelowej bazy danych. Zobacz [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Następujące opcje bazy danych są ustawiane lub zastąpione i nie można zmienić później: 

- `NEW_BROKER` Jeśli broker nie jest włączone w pliku bak. 
- `ENABLE_BROKER` Jeśli broker nie jest włączone w pliku bak. 
- `AUTO_CLOSE=OFF` Jeśli baza danych w pliku bak `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` Jeśli baza danych w pliku bak `SIMPLE` lub `BULK_LOGGED` trybu odzyskiwania.
- Grupy plików zoptymalizowanej pod kątem pamięci zostaną dodane i wywoływane XTP, jeśli to nie było w pliku bak źródła. 
- Wszelkie istniejące grupy plików zoptymalizowanej pod kątem pamięci została zmieniona na XTP. 
- `SINGLE_USER` i `RESTRICTED_USER` opcje są konwertowane na `MULTI_USER`.

Ograniczenia: 

- `.BAK` Nie można przywrócić pliki, które zawierają wiele zestawów kopii zapasowych. 
- `.BAK` Nie można przywrócić pliki, które zawierają wiele plików dziennika.
- Przywracanie nie powiodło się, jeśli zawiera .bak `FILESTREAM` danych.
- Nie można przywrócić kopii zapasowych, które zawierają bazy danych, które mają aktywne obiekty w pamięci w wystąpieniu ogólnego przeznaczenia. Aby uzyskać informacje na temat instrukcji restore, zobacz [instrukcji RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Usługa Service broker

Broker usług dla wielu wystąpień nie jest obsługiwane:

- `sys.routes`: Jako warunek wstępny należy wybrać adres z sys.routes. Adres musi być lokalny dla każdej ścieżki. Zobacz [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Nie można użyć `CREATE ROUTE` z `ADDRESS` innych niż `LOCAL`. Zobacz [Utwórz trasy](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Nie można użyć `ALTER ROUTE` z `ADDRESS` innych niż `LOCAL`. Zobacz [trasy ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedury składowane, funkcje i wyzwalacze

- `NATIVE_COMPILATION` nie jest obsługiwany w warstwie ogólnego przeznaczenia.
- Następujące [procedury składowanej sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) opcje nie są obsługiwane: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` nie jest obsługiwane. Zobacz [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` nie jest obsługiwane. Zobacz [procedury xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` nie są obsługiwane w tym `sp_addextendedproc`  i `sp_dropextendedproc`. Zobacz [rozszerzonych procedur składowanych](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`, i `sp_detach_db` nie są obsługiwane. Zobacz [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), i [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Zmiany zachowania

Następujące zmienne, funkcje i widoki zwracają różne wyniki:

- `SERVERPROPERTY('EngineEdition')` Zwraca wartość 8. Ta właściwość jest jednoznacznie identyfikuje wystąpienie zarządzane. Zobacz [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Zwraca wartość NULL, ponieważ nie istnieje pojęcie wystąpienia, ponieważ dla programu SQL Server nie ma zastosowania do wystąpienia zarządzanego. Zobacz [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Zwraca pełną nazwę "składnika" DNS, na przykład Moje instance.wcus17662feb9ce98.database.windows.net zarządzane. Zobacz [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Zwraca pełną nazwę "składnika" DNS, takich jak `myinstance.domain.database.windows.net` dla właściwości "name" i "data_source." Zobacz [SYS. SERWERY](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Zwraca wartość NULL, ponieważ koncepcję usług, ponieważ nie istnieje dla programu SQL Server nie ma zastosowania do wystąpienia zarządzanego. Zobacz [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` jest obsługiwany. Zwraca wartość NULL, jeśli nie ma sys.syslogins logowania usługi Azure AD. Zobacz [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` nie jest obsługiwane. Nieprawidłowe dane są zwracane, który jest znany problem tymczasowy. Zobacz [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a> Znane problemy i ograniczenia

### <a name="tempdb-size"></a>Rozmiar bazy danych TEMPDB

Maksymalny rozmiar pliku wynoszący `tempdb` nie może być większa niż 24 GB na każdy rdzeń w warstwie ogólnego przeznaczenia. Maksymalna `tempdb` rozmiar w warstwie krytyczne dla działania firmy jest ograniczone i rozmiar magazynu wystąpienia. `tempdb` Bazy danych zawsze zostanie podzielona na 12 danych plików. Nie można zmienić ten maksymalny rozmiar każdego pliku, a nowe pliki, które można dodać do `tempdb`. Niektóre zapytania może zwrócić błąd, gdy potrzebują więcej niż 24 GB na rdzeń procesora w `tempdb`.

### <a name="cant-restore-contained-database"></a>Nie można przywrócić zawartej bazy danych

Wystąpienia zarządzanego nie można przywrócić [zawartych baz danych](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). W momencie przywracania istniejące zawarte bazy danych nie działa w przypadku wystąpienia zarządzanego. Ten problem zostanie rozwiązany wkrótce. W międzyczasie zaleca się, Usuń opcja relacji zawierania z baz danych, które są umieszczane w wystąpieniu zarządzanym. Nie używaj opcji zawierania dla baz danych w środowisku produkcyjnym. 

### <a name="exceeding-storage-space-with-small-database-files"></a>Przekroczenia miejsca do magazynowania z plikami małej bazy danych

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, i `RESTORE DATABASE` instrukcji może zakończyć się niepowodzeniem, ponieważ wystąpienie może osiągnąć limit usługi Azure Storage.

Każdego wystąpienia zarządzanego ogólnego przeznaczenia jest zarezerwowana dla miejsca na dysku w warstwie Premium usługi Azure storage do 35 TB. Każdy plik bazy danych jest umieszczany na innym dysku fizycznym. Rozmiary dysków może być 128 GB, 256 GB, 512 GB, 1 TB lub 4 TB. Nieużywane miejsce na dysku nie jest naliczana, ale suma rozmiarów dysków w warstwie Premium platformy Azure nie może przekraczać 35 TB. W niektórych przypadkach wystąpienia zarządzanego, która nie wymaga 8 TB w sumie może przekraczać 35 TB Azure limit rozmiaru magazynu z powodu wewnętrznego fragmentacji.

Na przykład wystąpienia zarządzanego ogólnego przeznaczenia może mieć jeden plik jest 1,2 TB w rozmiarze umieszczane na dysku 4 TB. Może również mieć 248 plików znajdujących się na każdy 1 GB, rozmiar, które są umieszczone na oddzielnych dyskach 128 GB. W tym przykładzie:

- Rozmiar magazynu całkowitego miejsca na dysku przydzielonego to 1-4 TB + 248 × 128 GB = 35 TB.
- łączne miejsce zarezerwowane dla baz danych w wystąpieniu jest 1 x 1,2 TB + 248 x 1 GB = 1,4 TB pojemności.

Ten przykład ilustruje, że w pewnych okolicznościach, ze względu na dystrybucji określonych plików, wystąpienia zarządzanego może osiągnięcia limitu 35 TB, zarezerwowane dla dołączonego dysku Premium platformy Azure, może nie oczekujesz.

W tym przykładzie istniejących baz danych w dalszym ciągu działać i można rozwijać bez żadnych przeszkód, tak długo, jak nie są dodawane nowe pliki. Nowe bazy danych nie można utworzyć ani przywrócić, ponieważ nie ma wystarczającej ilości miejsca dla nowych dysków twardych, nawet wtedy, gdy łączny rozmiar wszystkich baz danych nie osiągną limit rozmiaru wystąpienia. Błąd, który jest zwracany w takiej sytuacji nie jest jasne.

Możesz [określenie liczby pozostałych plików](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) przy użyciu widoków systemowych. Jeśli przekroczysz ten limit, spróbuj [pustych, a następnie usuń część mniejsze pliki przy użyciu instrukcji DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) lub przełącz się do [warstwie krytyczne dla działania firmy, która nie ma tego limitu](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>Przywróć niepoprawnej konfiguracji klucza sygnatury dostępu Współdzielonego podczas bazy danych

`RESTORE DATABASE` czy odczytuje plik bak może być stale ponawianie próby odczytać .bak pliku i zwraca błędu po długim okresie czasu, jeśli sygnatury dostępu współdzielonego w `CREDENTIAL` jest niepoprawny. Przed przywróceniem bazy danych należy upewnić się, że klucz sygnatury dostępu Współdzielonego jest poprawny, należy wykonać przywracanie HEADERONLY.
Upewnij się, że usuwa wiodące `?` z klucza sygnatury dostępu Współdzielonego, który jest generowany przy użyciu witryny Azure portal.

### <a name="tooling"></a>Narzędzia

SQL Server Management Studio i SQL Server Data Tools może być pewne problemy podczas uzyskiwania dostępu do wystąpienia zarządzanego.

- Przy użyciu jednostek serwera usługi Azure AD (logowania) i użytkownikami (publiczna wersja zapoznawcza) przy użyciu programu SQL Server Data Tools aktualnie nie jest obsługiwane.
- Funkcje tworzenia skryptów podmiotów zabezpieczeń serwera usługi Azure AD (logowania) i użytkownikami (publiczna wersja zapoznawcza) nie jest obsługiwana w programie SQL Server Management Studio.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Nieprawidłowa baza danych nazw w niektórych widoków, dzienników i komunikatów

Kilka widoków systemowych, liczniki wydajności, komunikaty o błędach, XEvents i wpisów dziennika błędów Wyświetl identyfikator GUID identyfikatory bazy danych zamiast nazwy bazy danych. Nie należy polegać na tych identyfikatorów GUID ponieważ są one zastąpiony nazwy bazy danych w przyszłości.

### <a name="database-mail"></a>Poczta bazy danych

`@query` Parametru w [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) procedura nie działa.

### <a name="database-mail-profile"></a>Profil poczty bazy danych

Profil poczty bazy danych używane przez agenta programu SQL Server musi zostać wywołana `AzureManagedInstance_dbmail_profile`. Nie ma żadnych ograniczeń dla innych nazw profil poczty bazy danych.

### <a name="error-logs-arent-persisted"></a>Dzienniki błędów nie są zachowywane.

Dzienniki błędów, które są niedostępne w wystąpieniu zarządzanym nie są zachowywane, a ich rozmiar nie jest zawarty w limicie pamięci masowej. Dzienniki błędów może automatycznie usuwane w przypadku przejścia do trybu failover.

### <a name="error-logs-are-verbose"></a>Dzienniki błędów są pełne

Wystąpienie zarządzane umieszcza pełne informacje w dziennikach błędów, a większość go nie ma znaczenia. W przyszłości zmniejszy się ilość informacji w dziennikach błędów.

**Obejście:** Użyj niestandardowej procedury, aby odczytać dzienniki błędów, które odfiltrowuje niektóre wpisy nie ma znaczenia. Aby uzyskać więcej informacji, zobacz [wystąpienia zarządzanego — sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Zakres transakcji na dwie bazy danych w ramach tego samego wystąpienia nie jest obsługiwane.

`TransactionScope` Klasy na platformie .NET nie działa, jeśli dwa zapytania są wysyłane do dwóch baz danych w ramach tego samego wystąpienia w ramach tego samego zakresu transakcji:

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Mimo że ten kod działa z danymi w ramach tego samego wystąpienia, wymagane usługi MSDTC.

**Obejście:** Użyj [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) używać innej bazy danych w kontekście połączenia, zamiast korzystać z dwóch połączeń.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Moduły środowiska CLR i czasami połączone serwery nie mogą odwoływać się lokalny adres IP

Moduły środowiska CLR, umieszczone w wystąpienia zarządzanego i połączone serwery lub zapytań rozproszonych, które czasami odwoływać się do bieżącego wystąpienia nie można rozpoznać adres IP lokalnego wystąpienia. Ten błąd jest przejściowy problem.

**Obejście:** Jeśli to możliwe używać kontekstu połączeń w module środowiska CLR.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>Zaszyfrowane przez funkcję TDE baz danych za pomocą klucza zarządzanego przez usługę nie obsługuje kopii zapasowych zainicjowanych przez użytkownika

Nie można wykonać `BACKUP DATABASE ... WITH COPY_ONLY` w bazie danych, która jest szyfrowany przy użyciu zarządzanego przez usługę przezroczystego szyfrowania danych (TDE). Zarządzane przez usługę TDE wymusza szyfrowania z kluczem wewnętrznym TDE kopii zapasowych. Nie można wyeksportować klucza, więc nie można przywrócić kopii zapasowej.

**Obejście:** Użyj automatycznych kopii zapasowych i przywracania w momencie lub [zarządzanych przez klienta (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) zamiast tego. Można również wyłączyć szyfrowania bazy danych.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat zarządzanych wystąpień przez zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
- Dla funkcji i listy porównanie, zobacz [porównanie funkcji usługi Azure SQL Database](sql-database-features.md).
- Aby uzyskać szybki start przedstawiający sposób tworzenia nowego wystąpienia zarządzanego, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
