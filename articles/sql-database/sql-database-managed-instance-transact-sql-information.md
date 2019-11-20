---
title: Różnice T-SQL wystąpienia zarządzanego
description: W tym artykule omówiono różnice w języku T-SQL między wystąpieniem zarządzanym w Azure SQL Database i SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: 636fd5fd17838c729cdbc9e2a322c1f991d93948
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186441"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>Różnice w języku T-SQL wystąpienia zarządzanego, ograniczenia i znane problemy

Ten artykuł zawiera podsumowanie i wyjaśnienie różnic między składnią i zachowaniem Azure SQL Database wystąpienia zarządzanego i aparatu bazy danych SQL Server lokalnych. Opcja wdrażania wystąpienia zarządzanego zapewnia wysoką zgodność z lokalnym aparatem bazy danych SQL Server. Większość funkcji aparatu bazy danych SQL Server jest obsługiwana w wystąpieniu zarządzanym.

![Migracja](./media/sql-database-managed-instance/migration.png)

Istnieją pewne ograniczenia PaaS wprowadzane w wystąpieniu zarządzanym oraz zmiany zachowania w porównaniu z SQL Server. Różnice są podzielone na następujące kategorie:<a name="Differences"></a>

- [Dostępność](#availability) obejmuje różnice w przypadku, gdy są [zawsze włączone](#always-on-availability) i [kopie zapasowe](#backup).
- [Zabezpieczenia](#security) obejmują różnice między [inspekcjami](#auditing), [certyfikatami](#certificates), [poświadczeniami](#credential), [dostawcami usług kryptograficznych](#cryptographic-providers), [logowania i użytkownikami](#logins-and-users)oraz [kluczem usługi i kluczem głównym usługi](#service-key-and-service-master-key).
- [Konfiguracja](#configuration) obejmuje różnice w [rozszerzeniu puli buforów](#buffer-pool-extension), [Sortowanie](#collation), [poziomy zgodności](#compatibility-levels), [dublowanie bazy danych](#database-mirroring), [Opcje bazy danych](#database-options), [Agent SQL Server](#sql-server-agent)i [Opcje tabeli](#tables).
- [Funkcje](#functionalities) obejmują [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transakcji rozproszonych](#distributed-transactions), [zdarzeń rozszerzonych](#extended-events), [bibliotek zewnętrznych](#external-libraries), [FILESTREAM i FileTable](#filestream-and-filetable), [pełnotekstowego Wyszukiwanie semantyczne](#full-text-semantic-search), [połączone serwery](#linked-servers), [baza](#polybase), [replikacja](#replication), [przywracanie](#restore-statement), [Service Broker](#service-broker), [procedury składowane, funkcje i wyzwalacze](#stored-procedures-functions-and-triggers).
- [Ustawienia środowiska](#Environment) , takie jak sieci wirtualnych i konfiguracje podsieci.

Większość z tych funkcji jest ograniczeniami architektury i reprezentuje funkcje usługi.

Ta strona zawiera również informacje o [tymczasowych znanych problemach](#Issues) , które zostały wykryte w wystąpieniu zarządzanym, które zostaną rozwiązane w przyszłości.

## <a name="availability"></a>Dostępność

### <a name="always-on-availability"></a>Zawsze włączone

[Wysoka dostępność](sql-database-high-availability.md) jest wbudowana w wystąpienie zarządzane i nie może być kontrolowana przez użytkowników. Następujące instrukcje nie są obsługiwane:

- [UTWÓRZ PUNKT KOŃCOWY... DLA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [UTWÓRZ GRUPĘ DOSTĘPNOŚCI](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ZMIEŃ GRUPĘ DOSTĘPNOŚCI](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [PORZUĆ GRUPĘ DOSTĘPNOŚCI](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Klauzula [SET HADR Cluster](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) instrukcji [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

Wystąpienia zarządzane mają automatyczne kopie zapasowe, dlatego użytkownicy mogą tworzyć pełne kopie zapasowe `COPY_ONLY`. Kopie zapasowe, dzienniki i migawki plików nie są obsługiwane.

- W przypadku wystąpienia zarządzanego można utworzyć kopię zapasową bazy danych wystąpienia tylko na koncie usługi Azure Blob Storage:
  - Obsługiwane są tylko `BACKUP TO URL`.
  - urządzenia `FILE`, `TAPE`i kopii zapasowych nie są obsługiwane.
- Większość ogólnych opcji `WITH` są obsługiwane.
  - `COPY_ONLY` jest obowiązkowy.
  - `FILE_SNAPSHOT` nie jest obsługiwana.
  - Opcje taśmy: `REWIND`, `NOREWIND`, `UNLOAD`i `NOUNLOAD` nie są obsługiwane.
  - Opcje specyficzne dla dziennika: `NORECOVERY`, `STANDBY`i `NO_TRUNCATE` nie są obsługiwane.

Ograniczenia: 

- Z wystąpieniem zarządzanym można utworzyć kopię zapasową bazy danych wystąpienia w usłudze z maksymalnie 32 pasków, co wystarcza dla baz danych o pojemności do 4 TB w przypadku użycia kompresji kopii zapasowej.
- Nie można wykonać `BACKUP DATABASE ... WITH COPY_ONLY` w bazie danych, która jest zaszyfrowana za pomocą Transparent Data Encryption zarządzanej przez usługę (TDE). TDE zarządzane przez usługę wymusza szyfrowanie kopii zapasowych przy użyciu wewnętrznego klucza TDE. Nie można wyeksportować klucza, dlatego nie można przywrócić kopii zapasowej. Użyj funkcji automatycznego tworzenia kopii zapasowych oraz przywracania do punktu w czasie lub zamiast tego użyj [zarządzanego przez klienta (BYOK) TDE](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) . Można również wyłączyć szyfrowanie bazy danych.
- Maksymalny rozmiar paska tworzenia kopii zapasowej przy użyciu polecenia `BACKUP` w wystąpieniu zarządzanym wynosi 195 GB, czyli maksymalny rozmiar obiektu BLOB. Zwiększ liczbę pasków w poleceniu kopii zapasowej, aby zmniejszyć rozmiar poszczególnych pasków i pozostać w tym limicie.

    > [!TIP]
    > Aby obejść to ograniczenie, podczas tworzenia kopii zapasowej bazy danych z poziomu SQL Server w środowisku lokalnym lub na maszynie wirtualnej można:
    >
    > - Utwórz kopię zapasową do `DISK` zamiast tworzenia kopii zapasowej w `URL`.
    > - Przekaż pliki kopii zapasowej do magazynu obiektów BLOB.
    > - Przywróć do wystąpienia zarządzanego.
    >
    > Polecenie `Restore` w wystąpieniu zarządzanym obsługuje większe rozmiary obiektów BLOB w plikach kopii zapasowej, ponieważ do przechowywania przekazanych plików kopii zapasowych jest używany inny typ obiektu BLOB.

Aby uzyskać informacje o kopiach zapasowych przy użyciu języka T-SQL, zobacz [kopia zapasowa](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Zabezpieczenia

### <a name="auditing"></a>Inspekcja

Kluczowe różnice między inspekcją w bazach danych w Azure SQL Database i bazach danych w SQL Server są następujące:

- Po wybraniu opcji wdrożenie wystąpienia zarządzanego w Azure SQL Database Inspekcja działa na poziomie serwera. Pliki dziennika `.xel` są przechowywane w usłudze Azure Blob Storage.
- Korzystając z opcji wdrażania pojedynczej bazy danych i elastycznej puli w Azure SQL Database, inspekcja działa na poziomie bazy danych.
- W SQL Server maszynach lokalnych lub wirtualnych Inspekcja działa na poziomie serwera. Zdarzenia są przechowywane w systemie plików lub w dziennikach zdarzeń systemu Windows.
 
Inspekcja systemu XEvent w wystąpieniu zarządzanym obsługuje cele usługi Azure Blob Storage. Dzienniki plików i okien nie są obsługiwane.

Kluczowe różnice w składni `CREATE AUDIT` na potrzeby inspekcji w usłudze Azure Blob Storage są następujące:

- Dostępna jest nowa `TO URL` składniowa, która umożliwia określenie adresu URL kontenera magazynu obiektów blob platformy Azure, w którym umieszczane są pliki `.xel`.
- Składnia `TO FILE` nie jest obsługiwana, ponieważ wystąpienie zarządzane nie może uzyskać dostępu do udziałów plików systemu Windows.

Aby uzyskać więcej informacji, zobacz: 

- [UTWÓRZ INSPEKCJĘ SERWERA](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Inspekcja](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certyfikaty

Wystąpienie zarządzane nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, więc obowiązują następujące ograniczenia:

- Plik `CREATE FROM`/`BACKUP TO` nie jest obsługiwany w przypadku certyfikatów.
- Certyfikat /`CREATE``BACKUP` z `FILE`/`ASSEMBLY` nie jest obsługiwany. Nie można używać plików kluczy prywatnych. 

Zobacz [Tworzenie certyfikatu](/sql/t-sql/statements/create-certificate-transact-sql) i tworzenie [kopii zapasowej certyfikatu](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Obejście**: zamiast tworzenia kopii zapasowej certyfikatu i przywracania kopii zapasowej [Pobierz zawartość binarną certyfikatu i klucz prywatny, Zapisz go jako plik. SQL i Utwórz na podstawie danych binarnych](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Poświadczenie

Obsługiwane są tylko tożsamości Azure Key Vault i `SHARED ACCESS SIGNATURE`. Użytkownicy systemu Windows nie są obsługiwani.

Zobacz [Tworzenie poświadczeń](/sql/t-sql/statements/create-credential-transact-sql) i [ALTER Credential](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Dostawcy usług kryptograficznych

Wystąpienie zarządzane nie może uzyskać dostępu do plików, więc nie można utworzyć dostawców usług kryptograficznych:

- `CREATE CRYPTOGRAPHIC PROVIDER` nie jest obsługiwana. Zobacz [Tworzenie dostawcy usług kryptograficznych](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` nie jest obsługiwana. Zobacz [ALTER Cryptographic Provider](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Nazwy logowania i użytkownicy

- Obsługiwane są nazwy logowania SQL utworzone za pomocą `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`i `FROM SID`. Zobacz [Tworzenie nazwy logowania](/sql/t-sql/statements/create-login-transact-sql).
- Obsługiwane są Azure Active Directory (nazwy główne serwera usługi Azure AD) utworzone przy użyciu składni [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) lub [Create User from Login (logowanie za pomocą usługi Azure AD](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) ). Te nazwy logowania są tworzone na poziomie serwera.

    Wystąpienie zarządzane obsługuje główne bazy danych usługi Azure AD przy użyciu składni `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Ta funkcja jest również znana jako użytkownicy zawartej bazy danych usługi Azure AD.

- Nazwy logowania systemu Windows utworzone za pomocą składni `CREATE LOGIN ... FROM WINDOWS` nie są obsługiwane. Użyj Azure Active Directory logowania i użytkowników.
- Użytkownik usługi Azure AD, który utworzył wystąpienie, ma [nieograniczone uprawnienia administratora](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Użytkownicy niebędący administratorami na poziomie bazy danych usługi Azure AD mogą być tworzone przy użyciu składni `CREATE USER ... FROM EXTERNAL PROVIDER`. Zobacz [Tworzenie użytkownika... OD dostawcy zewnętrznego](sql-database-manage-logins.md#non-administrator-users).
- Nazwy główne serwera usługi Azure AD obsługują funkcje SQL tylko w ramach jednego wystąpienia zarządzanego. Funkcje, które wymagają interakcji między wystąpieniami, niezależnie od tego, czy znajdują się w tej samej dzierżawie usługi Azure AD, czy w różnych dzierżawach, nie są obsługiwane dla użytkowników usługi Azure AD. Przykłady takich funkcji to:

  - Replikacja transakcyjna bazy danych SQL.
  - Połącz serwer.

- Ustawienie identyfikatora logowania usługi Azure AD zamapowanego na grupę usługi Azure AD jako właściciel bazy danych nie jest obsługiwane.
- Personifikacja podmiotów zabezpieczeń na poziomie serwera usługi Azure AD przy użyciu innych podmiotów zabezpieczeń usługi Azure AD jest obsługiwana, takich jak klauzula [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) . Ograniczenia wykonywania jako są następujące:

  - Wartość Uruchom jako użytkownik nie jest obsługiwana dla użytkowników usługi Azure AD, gdy nazwa jest inna niż nazwa logowania. Przykładem jest to, że użytkownik jest tworzony przy użyciu składni CREATE USER [myAadUser] FROM LOGIN [john@contoso.com], a Personifikacja jest podejmowana przy użyciu polecenia EXEC AS USER = _myAadUser_. Podczas tworzenia **użytkownika** z poziomu podmiotu zabezpieczeń serwera usługi Azure AD (login) Określ user_name jako ten sam Login_name z **nazwy logowania**.
  - Tylko podmioty zabezpieczeń na poziomie SQL Server (logowania) będące częścią roli `sysadmin` mogą wykonywać następujące operacje, które są przeznaczone dla podmiotów zabezpieczeń usługi Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Eksportowanie/Importowanie bazy danych przy użyciu plików BACPAC jest obsługiwane dla użytkowników usługi Azure AD w zarządzanym wystąpieniu przy użyciu programu [SSMS v 18.4 lub nowszego](/sql/ssms/download-sql-server-management-studio-ssms)lub pliku [sqlpackage. exe](/sql/tools/sqlpackage-download).
  - Następujące konfiguracje są obsługiwane za pomocą pliku BACPAC bazy danych: 
    - Eksportuj/importuj bazę danych między różnymi wystąpieniami zarządzania w ramach tej samej domeny usługi Azure AD.
    - Wyeksportuj bazę danych z wystąpienia zarządzanego i zaimportuj ją do SQL Database w ramach tej samej domeny usługi Azure AD. 
    - Wyeksportuj bazę danych z SQL Database i zaimportuj do wystąpienia zarządzanego w ramach tej samej domeny usługi Azure AD.
    - Wyeksportuj bazę danych z wystąpienia zarządzanego i zaimportuj do SQL Server (wersja 2012 lub nowsza).
      - W tej konfiguracji wszyscy użytkownicy usługi Azure AD są utworzeni jako podmioty zabezpieczeń bazy danych SQL (Użytkownicy) bez logowania. Typ użytkowników jest wymieniony jako SQL (widoczny jako SQL_USER w tabeli sys. database_principals). Ich uprawnienia i role pozostają w metadanych bazy danych SQL Server i mogą być używane do personifikacji. Nie można ich jednak używać do uzyskiwania dostępu do SQL Server i logowania się do nich przy użyciu swoich poświadczeń.

- Tylko główna nazwa logowania na poziomie serwera, która jest tworzona przez proces aprowizacji wystąpienia zarządzanego, członkowie ról serwera, takie jak `securityadmin` lub `sysadmin`, lub inne logowania z uprawnieniem ALTER ANY LOGIN na poziomie serwera mogą utworzyć serwer usługi Azure AD podmioty zabezpieczeń (Logins) w bazie danych Master dla wystąpienia zarządzanego.
- Jeśli nazwa logowania jest podmiotem SQL, tylko nazwy logowania należące do roli `sysadmin` mogą używać polecenia CREATE do tworzenia logowań dla konta usługi Azure AD.
- Identyfikator logowania usługi Azure AD musi być członkiem usługi Azure AD w tym samym katalogu, który jest używany na potrzeby Azure SQL Database wystąpienia zarządzanego.
- Nazwy główne (Logins) serwera usługi Azure AD są widoczne w Eksplorator obiektów rozpoczynające się od SQL Server Management Studio 18,0 wersja zapoznawcza 5.
- Mogą nakładać się nazwy główne (logowania) serwera usługi Azure AD z kontem administratora usługi Azure AD. Nazwy główne (Logins) serwera usługi Azure AD mają pierwszeństwo przed administratorem usługi Azure AD po rozwiązaniu podmiotu zabezpieczeń i zastosowaniu uprawnień do wystąpienia zarządzanego.
- Podczas uwierzytelniania następująca sekwencja jest stosowana w celu rozwiązania uwierzytelniania podmiotu zabezpieczeń:

    1. Jeśli konto usługi Azure AD istnieje jako bezpośrednio zamapowane do podmiotu zabezpieczeń serwera usługi Azure AD (login), który znajduje się w pliku sys. server_principals jako typ "E", Udziel dostępu i Zastosuj uprawnienia podmiotu zabezpieczeń serwera usługi Azure AD.
    2. Jeśli konto usługi Azure AD jest członkiem grupy usługi Azure AD mapowanej do podmiotu zabezpieczeń serwera usługi Azure AD (logowanie), która znajduje się w pliku sys. server_principals jako typ "X", Udziel dostępu i Zastosuj uprawnienia do logowania do grupy usługi Azure AD.
    3. Jeśli konto usługi Azure AD jest specjalnym skonfigurowanym przez portal administratorem usługi Azure AD dla wystąpienia zarządzanego, które nie istnieje w widokach systemu wystąpienia zarządzanego, Zastosuj specjalne uprawnienia do administratora usługi Azure AD dla wystąpienia zarządzanego (starsza wersja).
    4. Jeśli konto usługi Azure AD istnieje jako bezpośrednio zamapowane do użytkownika usługi Azure AD w bazie danych, która jest obecna w pliku sys. database_principals jako typ "E", Udziel dostępu i Zastosuj uprawnienia użytkownika usługi Azure AD Database.
    5. Jeśli konto usługi Azure AD jest członkiem grupy usługi Azure AD, która jest zamapowana do użytkownika usługi Azure AD w bazie danych, która jest obecna w tabeli sys. database_principals jako typ "X", Udziel dostępu i Zastosuj uprawnienia do logowania do grupy usługi Azure AD.
    6. W przypadku zamapowania identyfikatora logowania usługi Azure AD na konto użytkownika usługi Azure AD lub konto grupy usługi Azure AD, które jest rozpoznawane przez użytkownika, który uwierzytelnia się, zostaną zastosowane wszystkie uprawnienia z tej nazwy logowania usługi Azure AD.

### <a name="service-key-and-service-master-key"></a>Klucz usługi i klucz główny usługi

- [Tworzenie kopii zapasowej klucza głównego](/sql/t-sql/statements/backup-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).
- [Przywracanie klucza głównego](/sql/t-sql/statements/restore-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).
- [Tworzenie kopii zapasowej klucza głównego usługi](/sql/t-sql/statements/backup-service-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).
- [Przywracanie klucza głównego usługi](/sql/t-sql/statements/restore-service-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).

## <a name="configuration"></a>Konfiguracja

### <a name="buffer-pool-extension"></a>Rozszerzenie puli buforów

- [Rozszerzenie puli buforów](/sql/database-engine/configure-windows/buffer-pool-extension) nie jest obsługiwane.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` nie jest obsługiwana. Zobacz [ALTER Server Configuration](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Sortowanie

Domyślne sortowanie wystąpień jest `SQL_Latin1_General_CP1_CI_AS` i można je określić jako parametr tworzenia. Zobacz [sortowania](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Poziomy zgodności

- Obsługiwane poziomy zgodności to 100, 110, 120, 130, 140 i 150.
- Poziomy zgodności poniżej 100 nie są obsługiwane.
- Domyślny poziom zgodności dla nowych baz danych to 140. W przypadku przywróconych baz danych poziom zgodności pozostaje niezmieniony, jeśli jego wartość to 100 i nowsze.

Zobacz [ALTER DATABASE — poziom zgodności](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Dublowanie bazy danych

Funkcja dublowania baz danych nie jest obsługiwana.

- Opcje `ALTER DATABASE SET PARTNER` i `SET WITNESS` nie są obsługiwane.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` nie jest obsługiwana.

Aby uzyskać więcej informacji, zobacz [ALTER DATABASE partner i Set monitor](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) oraz [Create Endpoint... DLA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opcje bazy danych

- Wiele plików dziennika nie jest obsługiwanych.
- Obiekty w pamięci nie są obsługiwane w warstwie usług Ogólnego przeznaczenia. 
- Istnieje limit 280 plików na wystąpienie Ogólnego przeznaczenia, co oznacza maksymalnie 280 plików na bazę danych. Oba pliki danych i dziennika w warstwie Ogólnego przeznaczenia są wliczane do tego limitu. [Warstwa krytyczne dla działania firmy obsługuje 32 767 plików na bazę danych](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Baza danych nie może zawierać grup plików zawierających dane FILESTREAM. Przywracanie kończy się niepowodzeniem, jeśli. bak zawiera dane `FILESTREAM`. 
- Każdy plik jest umieszczany w usłudze Azure Blob Storage. We/wy i przepływność na plik zależą od rozmiaru poszczególnych plików.

#### <a name="create-database-statement"></a>Instrukcja CREATE DATABASE

Następujące ograniczenia mają zastosowanie do `CREATE DATABASE`:

- Nie można definiować plików i grup plików. 
- Opcja `CONTAINMENT` nie jest obsługiwana. 
- Opcje `WITH` nie są obsługiwane. 
   > [!TIP]
   > Aby obejść ten element, należy użyć `ALTER DATABASE` po `CREATE DATABASE` do ustawienia opcji bazy danych w celu dodania plików lub ustawienia zawierania. 

- Opcja `FOR ATTACH` nie jest obsługiwana.
- Opcja `AS SNAPSHOT OF` nie jest obsługiwana.

Aby uzyskać więcej informacji, zobacz [Tworzenie bazy danych](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrukcja ALTER DATABASE

Nie można ustawić lub zmienić niektórych właściwości pliku:

- Ścieżka pliku nie może być określona w instrukcji `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL. Usuń `FILENAME` ze skryptu, ponieważ wystąpienie zarządzane automatycznie umieszcza pliki. 
- Nie można zmienić nazwy pliku przy użyciu instrukcji `ALTER DATABASE`.

Następujące opcje są domyślnie ustawione i nie można ich zmienić:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Nie można modyfikować następujących opcji:

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

- Włączanie i wyłączanie agenta SQL Server nie jest obecnie obsługiwane w wystąpieniu zarządzanym. Agent SQL zawsze działa.
- Ustawienia agenta SQL Server są tylko do odczytu. Procedura `sp_set_agent_properties` nie jest obsługiwana w wystąpieniu zarządzanym. 
- Zadania
  - Obsługiwane są czynności zadania T-SQL.
  - Obsługiwane są następujące zadania replikacji:
    - Dziennik transakcji
    - Migawka
    - Dystrybutor
  - Obsługiwane są etapy zadania SSIS.
  - Inne typy kroków zadań nie są obecnie obsługiwane:
    - Etap zadania replikacji scalającej nie jest obsługiwany. 
    - Czytnik kolejki nie jest obsługiwany. 
    - Powłoka poleceń nie jest jeszcze obsługiwana.
  - Wystąpienia zarządzane nie mogą uzyskać dostępu do zasobów zewnętrznych, na przykład udziałów sieciowych za pośrednictwem Robocopy. 
  - SQL Server Analysis Services nie są obsługiwane.
- Powiadomienia są częściowo obsługiwane.
- Powiadomienie e-mail jest obsługiwane, chociaż wymaga skonfigurowania profilu Poczta bazy danych. Agent SQL Server może używać tylko jednego profilu Poczta bazy danych i musi mieć nazwę `AzureManagedInstance_dbmail_profile`. 
  - Moduł stronicowania nie jest obsługiwany.
  - Wysyłanie wiadomości nie jest obsługiwane.
  - Alerty nie są jeszcze obsługiwane.
  - Serwery proxy nie są obsługiwane.
- Dziennik zdarzeń nie jest obsługiwany.

Następujące funkcje agenta SQL nie są obecnie obsługiwane:

- Serwery proxy
- Planowanie zadań w bezczynnym procesorze CPU
- Włączanie lub wyłączanie agenta
- Alerty

Aby uzyskać informacje na temat agenta programu SQL Server, zobacz [SQL Server Agent (Agent programu SQL Server)](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabele

Następujące typy tabel nie są obsługiwane:

- [STRUMIENI](/sql/relational-databases/blob/filestream-sql-server)
- [OBIEKTU](/sql/relational-databases/blob/filetables-sql-server)
- [Tabela zewnętrzna](/sql/t-sql/statements/create-external-table-transact-sql) (baza Base)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (nieobsługiwane tylko w warstwie ogólnego przeznaczenia)

Aby uzyskać informacje o sposobach tworzenia i modyfikowania tabel, zobacz [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) i [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkcja

### <a name="bulk-insert--openrowset"></a>Wstawianie zbiorcze/OPENROWSET

Wystąpienie zarządzane nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, więc pliki muszą zostać zaimportowane z usługi Azure Blob Storage:

- `DATASOURCE` jest wymagane w poleceniu `BULK INSERT` podczas importowania plików z usługi Azure Blob Storage. Zobacz [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` jest wymagana w funkcji `OPENROWSET` podczas odczytywania zawartości pliku z usługi Azure Blob Storage. Zobacz [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` można używać do odczytywania danych z innych baz danych SQL Azure, wystąpień zarządzanych lub wystąpień SQL Server. Inne źródła, takie jak bazy danych Oracle lub pliki programu Excel, nie są obsługiwane.

### <a name="clr"></a>CLR

Wystąpienie zarządzane nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, więc obowiązują następujące ograniczenia:

- Obsługiwane są tylko `CREATE ASSEMBLY FROM BINARY`. Zobacz [Tworzenie ASSEM BLY na podstawie danych binarnych](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` nie jest obsługiwana. Zobacz [Tworzenie zestawu z pliku](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nie może odwoływać się do plików. Zobacz [ALTER Assembly](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Poczta bazy danych (db_mail)
 - `sp_send_dbmail` nie może wysłać załączników przy użyciu parametru @file_attachments. Nie ma dostępu do lokalnego systemu plików ani udziałów zewnętrznych ani platformy Azure Blob Storage.
 - Zapoznaj się z znanymi problemami dotyczącymi `@query` parametru i uwierzytelniania.
 
### <a name="dbcc"></a>DBCC

Nieudokumentowane instrukcje DBCC, które są włączone w SQL Server nie są obsługiwane w wystąpieniach zarządzanych.

- Obsługiwana jest tylko ograniczona liczba globalnych flag śledzenia. `Trace flags` na poziomie sesji nie są obsługiwane. Zobacz [flagi śledzenia](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [Polecenia DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) i [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) współpracują z ograniczoną liczbą globalnych flag śledzenia.
- [Polecenia DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) z opcjami REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST i REPAIR_REBUILD nie można użyć, ponieważ nie można ustawić bazy danych w trybie `SINGLE_USER` — Zobacz temat [ALTER DATABASE — różnice](#alter-database-statement). Potencjalne uszkodzenia bazy danych są obsługiwane przez zespół pomocy technicznej systemu Azure. Skontaktuj się z pomocą techniczną platformy Azure, jeśli obserwowanie się z uszkodzeniem bazy danych, które powinny zostać naprawione.

### <a name="distributed-transactions"></a>Transakcje rozproszone

Usługi MSDTC i [transakcje elastyczne](sql-database-elastic-transactions-overview.md) obecnie nie są obsługiwane w wystąpieniach zarządzanych.

### <a name="extended-events"></a>Rozszerzone zdarzenia

Niektóre elementy docelowe specyficzne dla systemu Windows dla zdarzeń rozszerzonych (XEvents) nie są obsługiwane:

- Element docelowy `etw_classic_sync` nie jest obsługiwany. Przechowywanie plików `.xel` w usłudze Azure Blob Storage. Zobacz [etw_classic_sync cel](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Element docelowy `event_file` nie jest obsługiwany. Przechowywanie plików `.xel` w usłudze Azure Blob Storage. Zobacz [event_file cel](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Biblioteki zewnętrzne

Biblioteki zewnętrzne w bazie danych R i Python nie są jeszcze obsługiwane. Zobacz [SQL Server Machine Learning Services](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FILESTREAM i FileTable

- Dane FILESTREAM nie są obsługiwane.
- Baza danych nie może zawierać grup plików z danymi `FILESTREAM`.
- `FILETABLE` nie jest obsługiwana.
- Tabele nie mogą mieć typów `FILESTREAM`.
- Następujące funkcje nie są obsługiwane:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Aby uzyskać więcej informacji, zobacz [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) i [FileTable](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Wyszukiwanie semantyczne pełnotekstowe

[Wyszukiwanie semantyczne](/sql/relational-databases/search/semantic-search-sql-server) nie jest obsługiwane.

### <a name="linked-servers"></a>Serwery połączone

Połączone serwery w wystąpieniach zarządzanych obsługują ograniczoną liczbę elementów docelowych:

- Obsługiwane elementy docelowe to wystąpienia zarządzane, pojedyncze bazy danych i wystąpienia SQL Server. 
- Połączone serwery nie obsługują dystrybuowanych transakcji zapisywalnych (MS DTC).
- Elementy docelowe, które nie są obsługiwane to pliki, Analysis Services i inne RDBMS. Spróbuj użyć natywnego importu CSV z platformy Azure Blob Storage przy użyciu `BULK INSERT` lub `OPENROWSET` jako alternatywy dla importu pliku.

Operacje

- Transakcje zapisu między wystąpieniami nie są obsługiwane.
- `sp_dropserver` jest obsługiwana w przypadku upuszczania połączonego serwera. Zobacz [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Funkcja `OPENROWSET` może służyć do wykonywania zapytań tylko w wystąpieniach SQL Server. Mogą być zarządzane, lokalne lub na maszynach wirtualnych. Zobacz [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- Funkcja `OPENDATASOURCE` może służyć do wykonywania zapytań tylko w wystąpieniach SQL Server. Mogą być zarządzane, lokalne lub na maszynach wirtualnych. Tylko wartości `SQLNCLI`, `SQLNCLI11`i `SQLOLEDB` są obsługiwane jako dostawcy. Może to być na przykład `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zobacz [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Połączonych serwerów nie można używać do odczytywania plików (Excel, CSV) z udziałów sieciowych. Spróbuj użyć [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) lub [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) , który odczytuje pliki CSV z usługi Azure Blob Storage. Śledź te żądania dla [elementu opinii o wystąpieniu zarządzanym](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Tabele zewnętrzne odwołujące się do plików w systemie HDFS lub Azure Blob Storage nie są obsługiwane. Aby uzyskać informacje o bazie danych Base, zobacz artykuł [Base](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikacja

- Obsługiwane są migawki i dwukierunkowe typy replikacji. Replikacja scalająca, replikacja równorzędna i aktualizowalne subskrypcje nie są obsługiwane.
- [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md) jest dostępna dla publicznej wersji zapoznawczej w wystąpieniu zarządzanym z pewnymi ograniczeniami:
    - Wszystkie typy uczestników replikacji (wydawcy, dystrybutora, subskrybent ściągania i wypychania) mogą być umieszczane w wystąpieniach zarządzanych, ale Wydawca i dystrybutor muszą być zarówno w chmurze, jak i w środowisku lokalnym.
    - Wystąpienia zarządzane mogą komunikować się z najnowszymi wersjami SQL Server. Zapoznaj się z obsługiwanymi wersjami [tutaj](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems).
    - Replikacja transakcyjna ma [dodatkowe wymagania dotyczące sieci](sql-database-managed-instance-transactional-replication.md#requirements).

Informacje o konfigurowaniu replikacji znajdują się w [samouczku](replication-with-sql-database-managed-instance.md)dotyczącym replikacji.


Jeśli replikacja jest włączona w bazie danych w [grupie trybu failover](sql-database-auto-failover-group.md), administrator wystąpienia zarządzanego musi oczyścić wszystkie publikacje na starym serwerze podstawowym i skonfigurować je ponownie na nowym serwerze podstawowym po przejściu w tryb failover. W tym scenariuszu są niezbędne następujące działania:

1. Zatrzymaj wszystkie zadania replikacji uruchomione w bazie danych, jeśli istnieją.
2. Porzuć metadane subskrypcji z wydawcą, uruchamiając następujący skrypt w bazie danych wydawcy:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Usuwanie metadanych subskrypcji z subskrybenta. Uruchom następujący skrypt w wystąpieniu bazy danych subskrypcji na subskrybencie:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Wymuszone porzucenie wszystkich obiektów replikacji z wydawcy przez uruchomienie następującego skryptu w opublikowanej bazie danych:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Wymuszone porzucanie starego dystrybutora z oryginalnego wystąpienia podstawowego (w przypadku powrotu po awarii do starego elementu głównego, który używa dystrybutora). Uruchom następujący skrypt w bazie danych Master w starym wystąpieniu zarządzanym dystrybutora:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

### <a name="restore-statement"></a>Instrukcja RESTORE 

- Obsługiwana składnia:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nieobsługiwana Składnia:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Źródło: 
  - `FROM URL` (usługa Azure Blob Storage) jest jedyną obsługiwaną opcją.
  - `FROM DISK`/`TAPE`/Backup urządzenie nie jest obsługiwane.
  - Zestawy kopii zapasowych nie są obsługiwane.
- Opcje `WITH` nie są obsługiwane, takie jak brak `DIFFERENTIAL` lub `STATS`.
- `ASYNC RESTORE`: Przywracanie jest kontynuowane nawet w przypadku przerwania połączenia z klientem. Jeśli połączenie zostało porzucone, można sprawdzić stan operacji przywracania w widoku `sys.dm_operation_status` oraz utworzyć i usunąć bazę danych. Zobacz sekcję [sys. dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Następujące opcje bazy danych są ustawiane lub zastępowane i nie można ich zmienić później: 

- `NEW_BROKER`, jeśli Broker nie jest włączony w pliku. bak. 
- `ENABLE_BROKER`, jeśli Broker nie jest włączony w pliku. bak. 
- `AUTO_CLOSE=OFF`, czy baza danych w pliku bak ma `AUTO_CLOSE=ON`. 
- `RECOVERY FULL`, czy baza danych w pliku bak ma tryb odzyskiwania `SIMPLE` lub `BULK_LOGGED`.
- Grupa plików zoptymalizowana pod kątem pamięci jest dodawana i nazywana XTP, jeśli nie została w pliku source. bak. 
- Wszystkie istniejące grupy plików zoptymalizowane pod kątem pamięci są zmieniane na XTP. 
- Opcje `SINGLE_USER` i `RESTRICTED_USER` są konwertowane na `MULTI_USER`.

Ograniczenia: 

- Kopie zapasowe uszkodzonych baz danych mogą zostać przywrócone w zależności od typu uszkodzenia, ale automatyczne kopie zapasowe nie będą pobierane do momentu, gdy uszkodzenie nie zostanie naprawione. Upewnij się, że uruchomiono `DBCC CHECKDB` w wystąpieniu źródłowym, i użyj `WITH CHECKSUM` kopii zapasowej, aby uniknąć tego problemu.
- Nie można przywrócić pliku `.BAK` bazy danych zawierającego wszelkie ograniczenia opisane w tym dokumencie (na przykład obiekty `FILESTREAM` lub `FILETABLE`) w wystąpieniu zarządzanym.
- nie można przywrócić plików `.BAK` zawierających wiele zestawów kopii zapasowych. 
- nie można przywrócić plików `.BAK` zawierających wiele plików dziennika.
- Kopie zapasowe, które zawierają bazy danych o rozmiarze większym niż 8 TB, aktywne obiekty OLTP w pamięci lub liczba plików, które przekroczą 280 plików na wystąpienie, nie mogą zostać przywrócone na wystąpienie Ogólnego przeznaczenia. 
- Kopie zapasowe, które zawierają bazy danych o rozmiarze większym niż 4 TB lub obiekty OLTP w pamięci o łącznym poziomie większym niż rozmiar opisany w [limitach zasobów](sql-database-managed-instance-resource-limits.md) , nie mogą zostać przywrócone w wystąpieniu krytyczne dla działania firmy.
Aby uzyskać informacje na temat przywracania instrukcji, zobacz [przywracanie instrukcji](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Te same ograniczenia dotyczą wbudowanej operacji przywracania do punktu w czasie. Przykładowo nie można przywrócić bazy danych Ogólnego przeznaczenia o pojemności większej niż 4 TB w wystąpieniu Krytyczne dla działania firmy. W wystąpieniu Ogólnego przeznaczenia nie można przywrócić bazy danych Krytyczne dla działania firmy z plikami OLTP w pamięci lub więcej niż 280 plików.

### <a name="service-broker"></a>Broker usług

Broker usług dla wielu wystąpień nie jest obsługiwany:

- `sys.routes`: jako warunek wstępny należy wybrać adres z wykazu sys. routes. Adres musi być lokalny dla każdej trasy. Zobacz sekcję [sys. routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: nie można użyć `CREATE ROUTE` z `ADDRESS` innym niż `LOCAL`. Zobacz [Tworzenie trasy](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: nie można użyć `ALTER ROUTE` z `ADDRESS` innym niż `LOCAL`. Zobacz [ALTER Route](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedury składowane, funkcje i wyzwalacze

- `NATIVE_COMPILATION` nie jest obsługiwana w warstwie Ogólnego przeznaczenia.
- Następujące opcje [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) nie są obsługiwane: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` nie jest obsługiwana. Zobacz [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` nie jest obsługiwana. Zobacz [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` nie są obsługiwane, w tym `sp_addextendedproc` i `sp_dropextendedproc`. Zobacz [rozszerzone procedury składowane](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`i `sp_detach_db` nie są obsługiwane. Zobacz [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)i [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Funkcje systemowe i zmienne

Następujące zmienne, funkcje i widoki zwracają różne wyniki:

- `SERVERPROPERTY('EngineEdition')` zwraca wartość 8. Ta właściwość jednoznacznie identyfikuje wystąpienie zarządzane. Zobacz [ServerProperty](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` zwraca wartość NULL, ponieważ koncepcja wystąpienia, która istnieje dla SQL Server nie ma zastosowania do wystąpienia zarządzanego. Zobacz [ServerProperty ("InstanceName")](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` zwraca pełną nazwę DNS "Connected", na przykład my-managed-instance.wcus17662feb9ce98.database.windows.net. Zobacz [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` zwraca pełną nazwę DNS "Connected", taką jak `myinstance.domain.database.windows.net` właściwości "name" i "data_source". Zobacz sekcję [sys. SERWERY](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` zwraca wartość NULL, ponieważ koncepcja usługi, która istnieje dla SQL Server, nie ma zastosowania do wystąpienia zarządzanego. Zobacz [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` jest obsługiwana. Zwraca wartość NULL, jeśli logowanie za pomocą usługi Azure AD nie znajduje się w pliku sys. syslogins. Zobacz [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` nie jest obsługiwana. Zwracane są błędne dane, które są tymczasowym znanym problemem. Zobacz [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Environment"></a>Ograniczenia środowiska

### <a name="subnet"></a>Subnet
-  Nie można umieścić żadnych innych zasobów (np. maszyn wirtualnych) w podsieci, w której wdrożono wystąpienie zarządzane. Wdróż te zasoby przy użyciu innej podsieci.
- Podsieć musi mieć wystarczającą liczbę dostępnych [adresów IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Wartość minimalna to 16, podczas gdy zalecenie ma mieć co najmniej 32 adresów IP w podsieci.
- [Punktów końcowych usługi nie można kojarzyć z podsiecią wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Upewnij się, że opcja punkty końcowe usługi jest wyłączona podczas tworzenia sieci wirtualnej.
- Liczba rdzeni wirtualnych i typy wystąpień, które można wdrożyć w regionie, mają pewne [ograniczenia i](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)ograniczenia.
- Istnieją pewne [reguły zabezpieczeń, które należy zastosować w podsieci](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>Sieć wirtualna
- Sieć wirtualną można wdrożyć przy użyciu modelu zasobów — model klasyczny dla sieci wirtualnej nie jest obsługiwany.
- Po utworzeniu wystąpienia zarządzanego przeniesienie wystąpienia zarządzanego lub sieci wirtualnej do innej grupy zasobów lub subskrypcji nie jest obsługiwane.
- Niektóre usługi, takie jak środowiska App Service, Aplikacje logiki i wystąpienia zarządzane (używane na potrzeby replikacji geograficznej, replikacji transakcyjnej lub połączonych serwerów) nie mogą uzyskać dostępu do wystąpień zarządzanych w różnych regionach, jeśli ich sieci wirtualnych są połączone przy użyciu [globalnego Komunikacja równorzędna](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Możesz połączyć się z tymi zasobami za pośrednictwem ExpressRoute lub sieci VNet-to-VNet za pośrednictwem bram sieci wirtualnej.

### <a name="tempdb"></a>TEMPDB

Maksymalny rozmiar pliku `tempdb` nie może być większy niż 24 GB na rdzeń w warstwie Ogólnego przeznaczenia. Maksymalny rozmiar `tempdb` w warstwie Krytyczne dla działania firmy jest ograniczony przez rozmiar magazynu wystąpień. rozmiar pliku dziennika `Tempdb` jest ograniczony do 120 GB na warstwie Ogólnego przeznaczenia. Niektóre zapytania mogą zwrócić błąd, jeśli potrzebują ponad 24 GB na rdzeń w `tempdb` lub jeśli wygenerują więcej niż 120 GB danych dziennika.

### <a name="error-logs"></a>Dzienniki błędów

Wystąpienie zarządzane umieszcza pełne informacje w dziennikach błędów. Istnieje wiele wewnętrznych zdarzeń systemowych, które są rejestrowane w dzienniku błędów. Użyj niestandardowej procedury, aby odczytać dzienniki błędów, które filtrują pewne nieistotne wpisy. Aby uzyskać więcej informacji, zobacz [wystąpienie zarządzane — sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) lub [rozszerzenie wystąpienia zarządzanego (wersja zapoznawcza)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) dla Azure Data Studio.

## <a name="Issues"></a>Znane problemy

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Limity pamięci OLTP w pamięci nie są stosowane

**Data:** 2019 października

Krytyczne dla działania firmy warstwa usług nie zastosuje prawidłowo [maksymalnych limitów pamięci dla obiektów zoptymalizowanych pod kątem pamięci](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) w niektórych przypadkach. Wystąpienie zarządzane może umożliwiać użycie większej ilości pamięci dla operacji OLTP w pamięci, co może mieć wpływ na dostępność i stabilność wystąpienia. Zapytania OLTP w pamięci, które zbliżają się do limitów, mogą kończyć się niepowodzeniem. Ten problem zostanie rozwiązany wkrótce. Zapytania, które używają więcej pamięci OLTP w pamięci, będą kończyć się niepowodzeniem, jeśli osiągnieją [limity](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Obejście:** [Monitoruj użycie magazynu OLTP w pamięci](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) za pomocą [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) , aby upewnić się, że obciążenie nie korzysta z więcej niż dostępnej pamięci. Zwiększ limity pamięci, które są zależne od liczby rdzeni wirtualnych, lub Zoptymalizuj obciążenie, aby użyć mniejszej ilości pamięci.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Podczas próby usunięcia pliku, który nie jest pusty, został zwrócony nieprawidłowy błąd

**Data:** 2019 października

Wystąpienie SQL Server/zarządzane [nie zezwala użytkownikowi na usuwanie niepustego pliku](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). W przypadku próby usunięcia niepustego pliku danych przy użyciu instrukcji `ALTER DATABASE REMOVE FILE` błąd `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` nie będzie natychmiast zwracany. Wystąpienie zarządzane będzie nadal próbowało porzucić plik i operacja zakończy się niepowodzeniem po fragmentach z `Internal server error`.

**Obejście**: Usuń zawartość pliku przy użyciu polecenia `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)`. Jeśli jest to jedyny plik w grupie plików, należy usunąć dane z tabeli lub partycji skojarzonej z tą grupą plików przed zmniejszeniem pliku i opcjonalnie załadować te dane do innej tabeli lub partycji.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Zmiana warstwy usługi i tworzenie wystąpienia są blokowane przez trwającą operację przywracania bazy danych

**Data:** Wrz 2019

Ciągła instrukcja `RESTORE`, proces migracji usługi migracji danych oraz wbudowane przywracanie do punktu w czasie zablokują aktualizację warstwy usług lub zmiany rozmiaru istniejącego wystąpienia i tworzy nowe wystąpienia do momentu zakończenia procesu przywracania. Proces przywracania spowoduje zablokowanie tych operacji na zarządzanych wystąpieniach i pulach wystąpień w tej samej podsieci, w której jest uruchomiony proces przywracania. Nie ma to żadnego oddziaływania na wystąpienia w pulach wystąpień. Operacje tworzenia lub zmiany warstwy usług nie będą kończyć się niepowodzeniem lub przekroczenia limitu czasu — będą one działać po ukończeniu lub anulowaniu procesu przywracania.

**Obejście**: Poczekaj na zakończenie procesu przywracania lub Anuluj proces przywracania, jeśli operacja tworzenia lub aktualizacji warstwy usługi ma wyższy priorytet.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Po przejściu w tryb failover może zajść konieczność ponownego skonfigurowania przyrządu zasobów na Krytyczne dla działania firmyej warstwie usług

**Data:** Wrz 2019

Funkcja [gubernatora zasobów](/sql/relational-databases/resource-governor/resource-governor) , która umożliwia ograniczenie zasobów przypisanych do obciążenia użytkownikami, może nieprawidłowo sklasyfikować pewne obciążenie użytkownika po przejściu w tryb failover lub w przypadku zmiany warstwy usługi (na przykład zmiany maksymalnego rdzeń wirtualny lub maksymalnego wystąpienia). Rozmiar magazynu).

**Obejście**: Uruchom `ALTER RESOURCE GOVERNOR RECONFIGURE` okresowo lub jako część zadania programu SQL Agent, które wykonuje zadanie SQL, gdy wystąpienie zostanie uruchomione, jeśli używasz [prezesa zasobów](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Okna dialogowe Service Broker między bazami danych muszą zostać zainicjowane po uaktualnieniu warstwy usług

**Data:** 2019 sie

Okna dialogowe Service Broker między bazami danych przestaną przekazanie komunikatów do usług w innych bazach danych po operacji zmiany warstwy usług. Komunikaty nie są **tracone** i znajdują się w kolejce nadawcy. Każda zmiana rozmiaru magazynu rdzeni wirtualnych lub wystąpienia w wystąpieniu zarządzanym spowoduje zmianę wartości `service_broke_guid` w widoku [sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) dla wszystkich baz danych. Wszystkie `DIALOG` utworzone przy użyciu instrukcji [BEGIN dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) , które odwołują się do brokerów usług w innej bazie danych, zatrzymają dostarczanie komunikatów do usługi docelowej.

**Obejście problemu:** Przed aktualizacją warstwy usług Zatrzymaj wszystkie działania, które używają konwersacji między bazami danych Service Broker. Jeśli pozostałe komunikaty są niedostarczone po zmianie warstwy usług, należy odczytać komunikaty z kolejki źródłowej i ponownie wysłać je do kolejki docelowej.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Impersonification typów logowania usługi Azure AD nie jest obsługiwana

**Data:** Lipiec 2019

Personifikacja przy użyciu `EXECUTE AS USER` lub `EXECUTE AS LOGIN` następujących podmiotów głównych usługi AAD nie jest obsługiwana:
-   Aliasy użytkowników usługi AAD. W tym przypadku jest zwracany następujący błąd `15517`.
- Nazwy logowania i użytkownicy usługi AAD w oparciu o aplikacje lub nazwy główne usług w usłudze AAD. W tym przypadku są zwracane następujące błędy `15517` i `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>parametr @query nie jest obsługiwany w sp_send_db_mail

**Data:** Kwiecień 2019

`@query` parametr w procedurze [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) nie działa.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Należy ponownie skonfigurować replikację transakcyjną po geograficznym przejściu do trybu failover

**Data:** Mar 2019

Jeśli replikacja transakcyjna jest włączona w bazie danych w grupie autotrybu failover, administrator wystąpienia zarządzanego musi oczyścić wszystkie publikacje na starym serwerze podstawowym i skonfigurować je ponownie na nowym serwerze podstawowym po przejściu w tryb failover do innego regionu. Aby uzyskać więcej informacji, zobacz [replikacja](#replication) .

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Nazwy logowania i użytkownicy usługi AAD nie są obsługiwane w programie SSDT

**Data:** Lis 2019

Narzędzia SQL Server Data Tools nie obsługują w pełni logowania i użytkowników usługi Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Tymczasowa baza danych jest używana podczas operacji przywracania

Gdy baza danych jest przywracana w wystąpieniu zarządzanym, usługa Restore najpierw utworzy pustą bazę danych o podanej nazwie w celu przydzielenia nazwy do wystąpienia. Po pewnym czasie ta baza danych zostanie porzucona, a przywrócenie rzeczywistej bazy danych zostanie rozpoczęte. Baza danych, która jest w stanie *przywracania* , będzie miała tymczasową wartość identyfikatora GUID, a nie nazwę. Nazwa tymczasowa zostanie zmieniona na żądaną nazwę określoną w instrukcji `RESTORE` po zakończeniu procesu przywracania. W fazie początkowej użytkownik może uzyskać dostęp do pustej bazy danych, a nawet utworzyć tabele lub załadować dane w tej bazie danych. Ta Tymczasowa baza danych zostanie porzucona, gdy usługa przywracania rozpocznie działanie drugiej fazy.

**Obejście**: nie należy uzyskiwać dostępu do przywracanej bazy danych, dopóki nie zobaczysz, że Przywracanie zostało zakończone.

### <a name="tempdb-structure-and-content-is-re-created"></a>Struktura i zawartość bazy danych TEMPDB są odtwarzane

Baza danych `tempdb` jest zawsze podzielona na 12 plików danych i nie można zmienić struktury pliku. Nie można zmienić maksymalnego rozmiaru pliku i nie można dodać do `tempdb`nowych plików. `Tempdb` jest zawsze tworzona jako pusta baza danych, gdy wystąpienie zostanie uruchomione lub działa w trybie failover, a wszelkie zmiany wprowadzone w `tempdb` nie zostaną zachowane.

### <a name="exceeding-storage-space-with-small-database-files"></a>Przekraczanie miejsca do magazynowania z małymi plikami bazy danych

instrukcje `CREATE DATABASE`, `ALTER DATABASE ADD FILE`i `RESTORE DATABASE` mogą się nie powieść, ponieważ wystąpienie może osiągnąć limit magazynu platformy Azure.

Każde Ogólnego przeznaczenia wystąpienia zarządzanego ma do 35 TB pamięci zarezerwowanej dla miejsca na dysku w warstwie Premium. Każdy plik bazy danych jest umieszczany na osobnym dysku fizycznym. Rozmiary dysków mogą być 128 GB, 256 GB, 512 GB, 1 TB lub 4 TB. Nieużywane miejsce na dysku jest nieobciążone, ale całkowita suma rozmiarów dysków w warstwie Premium platformy Azure nie może przekroczyć 35 TB. W niektórych przypadkach wystąpienie zarządzane, które nie wymaga 8 TB w sumie, może przekroczyć limit 35 TB platformy Azure dla rozmiaru magazynu z powodu wewnętrznej fragmentacji.

Na przykład wystąpienie zarządzane Ogólnego przeznaczenia może mieć jeden duży plik o rozmiarze 1,2 TB na dysku z 4 TBm. Może również mieć 248 plików o rozmiarze 1 GB, które są umieszczane na oddzielnych dyskach 128 GB. W tym przykładzie:

- Łączny rozmiar magazynu dyskowego to 1 x 4 TB + 248 x 128 GB = 35 TB.
- Całkowite zarezerwowane miejsce dla baz danych w wystąpieniu to 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

W tym przykładzie pokazano, że w pewnych okolicznościach, ze względu na określoną dystrybucję plików, wystąpienie zarządzane może osiągnąć limit 35-TB zarezerwowane dla dołączonego dysku Azure Premium, gdy użytkownik może nie oczekiwać.

W tym przykładzie istniejące bazy danych nadal pracują i mogą wzrosnąć bez żadnego problemu, o ile nie zostaną dodane nowe pliki. Nie można utworzyć ani przywrócić nowych baz danych, ponieważ nie ma wystarczającej ilości miejsca na nowe dyski, nawet jeśli całkowity rozmiar wszystkich baz danych nie osiągnie limitu rozmiaru wystąpienia. Błąd zwracany w tym przypadku nie jest wyczyszczony.

[Liczbę pozostałych plików można określić](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) za pomocą widoków systemu. Jeśli osiągnięto ten limit, spróbuj [opróżnić i usunąć niektóre z mniejszych plików przy użyciu instrukcji DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) lub przełączyć się do [warstwy krytyczne dla działania firmy, która nie ma tego ograniczenia](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Wyświetlane wartości identyfikatora GUID zamiast nazw baz danych

W kilku widokach systemu, licznikach wydajności, komunikatach o błędach, XEvents i dzienniku błędów są wyświetlane identyfikatory baz danych GUID zamiast rzeczywistej nazwy bazy danych. Nie należy polegać na tych identyfikatorach GUID, ponieważ są one zastępowane rzeczywistymi nazwami baz danych w przyszłości.

### <a name="error-logs-arent-persisted"></a>Dzienniki błędów nie są utrwalone

Dzienniki błędów dostępne w wystąpieniu zarządzanym nie są utrwalane, a ich rozmiar nie jest uwzględniony w maksymalnym limicie magazynu. Dzienniki błędów mogą być automatycznie wymazywane w przypadku przełączenia w tryb failover. W historii dzienników błędów mogą występować luki, ponieważ wystąpienie zarządzane zostało przeniesione kilka razy na kilka maszyn wirtualnych.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Zakres transakcji w dwóch bazach danych w tym samym wystąpieniu nie jest obsługiwany

Klasa `TransactionScope` w programie .NET nie działa, jeśli dwa zapytania są wysyłane do dwóch baz danych w tym samym wystąpieniu w ramach tego samego zakresu transakcji:

```csharp
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

Mimo że ten kod działa z danymi w tym samym wystąpieniu, wymagana jest usługa MSDTC.

**Obejście problemu:** Użyj elementu [SqlConnection. ChangeDatabase (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) , aby użyć innej bazy danych w kontekście połączenia zamiast korzystać z dwóch połączeń.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Moduły CLR i połączone serwery czasami nie mogą odwoływać się do lokalnego adresu IP

Moduły CLR umieszczane w wystąpieniu zarządzanym oraz połączone serwery lub zapytania rozproszone, które odwołują się do bieżącego wystąpienia czasami nie mogą rozpoznać adresu IP wystąpienia lokalnego. Ten błąd jest przejściowym problemem.

**Obejście problemu:** Jeśli to możliwe, Użyj połączeń kontekstu w module CLR.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji o wystąpieniach zarządzanych, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
- Aby poznać funkcje i listę porównania, zobacz [Azure SQL Database porównanie funkcji](sql-database-features.md).
- Aby zapoznać się z przewodnikiem Szybki Start, który pokazuje, jak utworzyć nowe wystąpienie zarządzane, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
