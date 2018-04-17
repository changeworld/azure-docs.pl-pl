---
title: Baza danych Azure SQL zarządzane wystąpienia T-SQL różnice | Dokumentacja firmy Microsoft
description: W tym artykule opisano różnice T-SQL wystąpienia zarządzane bazy danych SQL Azure i SQL Server.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab, bonova
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: 0fe0b5d3120833b8472fcdaee366a5d1f2818600
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL bazy danych zarządzanych wystąpienia T-SQL różnice z programu SQL Server 

Azure wystąpienia bazy danych SQL zarządzane (wersja zapoznawcza) zapewnia wysoką zgodność z lokalnymi aparatu bazy danych programu SQL Server. Większość funkcji aparatu bazy danych programu SQL Server są obsługiwane w przypadku zarządzanych. Ponieważ nadal istnieją pewne różnice w składni i zachowanie, w tym artykule przedstawiono i wyjaśniono różnice te.
 - [Różnice T-SQL i nieobsługiwanych funkcji](#Differences)
 - [Funkcje, które mają inaczej w przypadku zarządzanych](#Changes)
 - [Czasowe ograniczenia i znane problemy](#Issues)

## <a name="Differences"></a> Różnice T-SQL z programu SQL Server 

Ta sekcja zawiera podsumowanie podstawowych różnic w składni T-SQL i zachowanie między zarządzane wystąpienia i lokalnymi aparatu bazy danych programu SQL Server, a także nieobsługiwane funkcje.

### <a name="always-on-availability"></a>Zawsze włączone dostępności

[Wysoka dostępność](sql-database-high-availability.md) jest wbudowana w wystąpienia zarządzane i nie mogą być kontrolowane przez użytkowników. Poniższe instrukcje nie są obsługiwane:
 - [TWORZENIE PUNKTU KOŃCOWEGO... DLA DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
 - [UTWORZENIE GRUPY DOSTĘPNOŚCI](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
 - [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
 - [USUŃ GRUPY DOSTĘPNOŚCI](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
 - [Ustaw HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) klauzuli [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) — instrukcja

### <a name="auditing"></a>Inspekcja 
 
Podstawowe różnice między inspekcji SQL zarządzane wystąpienia bazy danych SQL Azure i SQL Server w lokalnym programie są:
- W przypadku zarządzanych inspekcji SQL działa na poziomie serwera i magazyny `.xel` kontem magazynu obiektów blob plików na platformie Azure.  
- W bazie danych SQL Azure inspekcji SQL działa na poziomie bazy danych.
- W lokalnej instalacji programu SQL Server / maszyny wirtualnej, inspekcji SQL działa na poziomie serwera, ale przechowuje zdarzenia na pliki systemu/dzienniki zdarzeń systemu windows.  
  
Zarządzane wystąpienia inspekcji systemu XEvent obsługuje obiekty docelowe magazynu obiektów blob platformy Azure. Dzienniki plików i systemu windows nie są obsługiwane.    
 
Klucz różnice w `CREATE AUDIT` składnię inspekcji do magazynu obiektów blob platformy Azure są:
- Nowej składni `TO URL` podano i umożliwia określenie adresu URL kontenera magazynu obiektów blob platformy Azure gdzie `.xel` zostaną umieszczone pliki 
- Składnia `TO FILE` nie jest obsługiwana, ponieważ wystąpienie zarządzane nie ma dostępu do udziałów plików systemu Windows. 
 
Aby uzyskać więcej informacji, zobacz:  
- [TWORZENIE INSPEKCJI SERWERA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [INSTRUKCJA ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql) 
- [Inspekcja](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)     

### <a name="backup"></a>Backup 

Zarządzane wystąpienia ma automatycznego tworzenia kopii zapasowych i umożliwia użytkownikom tworzenie pełnej bazy danych `COPY_ONLY` kopii zapasowych. Różnice, log oraz kopii zapasowych migawki pliku nie są obsługiwane.  
- Zarządzane wystąpienia można kopię zapasową bazy danych tylko konta magazynu obiektów Blob Azure: 
 - Tylko `BACKUP TO URL` jest obsługiwane 
 - `FILE`, `TAPE`, i urządzenia kopii zapasowej nie są obsługiwane.  
- Większość ogólne `WITH` opcje są obsługiwane. 
 - `COPY_ONLY` jest wymagane
 - `FILE_SNAPSHOT` Nieobsługiwane  
 - Opcje taśmy: `REWIND`, `NOREWIND`, `UNLOAD`, i `NOUNLOAD` nie są obsługiwane 
 - Opcje dziennika: `NORECOVERY`, `STANDBY`, i `NO_TRUNCATE` nie są obsługiwane 
 
Ograniczenia:  
- Zarządzane wystąpienie można utworzyć kopię zapasową bazy danych do kopii zapasowej z maksymalnie 32 rozkłada, której jest wystarczająca dla baz danych do 4 TB, jeśli kompresja kopii zapasowej jest używany.
- Maksymalny rozmiar kopii zapasowej stripe jest 195 GB (rozmiar maksymalny obiektów blob). Zwiększ liczbę rozkłada w poleceniu kopii zapasowej, aby zmniejszyć rozmiar poszczególnych stripe i pozostać w ten limit. 

> [!TIP]
> Aby obejść to ograniczenie lokalnymi, tworzenie kopii zapasowej na `DISK` zamiast kopii zapasowej `URL`, Przekaż plik kopii zapasowej do obiektu blob, a następnie przywrócić. Przywrócić pliki większe obsługuje, ponieważ jest używany typ inny obiektu blob.  

Informacje o kopii zapasowych przy użyciu T-SQL, zobacz [kopii zapasowej](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

### <a name="buffer-pool-extension"></a>Rozszerzenie puli buforów 
 
- [Rozszerzenie puli bufora](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nie jest obsługiwane.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` nie jest obsługiwane. Zobacz [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql). 
 
### <a name="bulk-insert--openrowset"></a>Wstawiania zbiorczego / openrowset

Zarządzanego wystąpienia nie może uzyskać dostępu udziały plików i folderów systemu Windows, więc musi zostać zaimportowane pliki z magazynu obiektów blob platformy Azure.
- `DATASOURCE` jest wymagany w `BULK INSERT` poleceń podczas importowania plików z magazynu obiektów blob platformy Azure. Zobacz [WSTAWIANIA ZBIORCZEGO](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` jest wymagany w `OPENROWSET` działania podczas odczytu zawartości pliku z magazynu obiektów blob platformy Azure. Zobacz [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
 
### <a name="certificates"></a>Certyfikaty 

Zarządzane wystąpienia nie może uzyskać dostępu udziały plików i folderów systemu Windows, obowiązują następujące ograniczenia: 
- `CREATE FROM`/`BACKUP TO` plik nie jest obsługiwany w przypadku certyfikatów
- `CREATE`/`BACKUP` certyfikat od `FILE` / `ASSEMBLY` nie jest obsługiwane. Nie można użyć plików kluczy prywatnych.  
 
Zobacz [Utwórz certyfikat](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) i [kopii zapasowej certyfikatu](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
> [!TIP]
> Obejście problemu: klucz prywatny certyfikatu/script, przechowywane jako pliki SQL i utworzyć binarnej: 
> 
> ``` 
CREATE CERTIFICATE  
 FROM BINARY = asn_encoded_certificate    
WITH PRIVATE KEY ( <private_key_options> ) 
>```   
 
### <a name="clr"></a>CLR 

Zarządzane wystąpienia nie może uzyskać dostępu udziały plików i folderów systemu Windows, obowiązują następujące ograniczenia: 
- Tylko `CREATE ASSEMBLY FROM BINARY` jest obsługiwana. Zobacz [Utwórz zestaw z BINARNEJ](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` nie jest obsługiwane. Zobacz [tworzenie zestawów z pliku](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` Nie można odwoływać się do plików. Zobacz [zmiana zestawu](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).
 
### <a name="compatibility-levels"></a>Poziomy zgodności 
 
- Poziomy zgodności obsługiwane są: 100, 110, 120, 130, 140  
- Nie są obsługiwane poziomy zgodności poniżej 100. 
- Domyślny poziom zgodności dla nowych baz danych jest 140. Przywróconych baz danych poziom zgodności nie zostaną zmienione, jeśli był 100 lub nowszym.

Zobacz [poziom zgodności bazy danych ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
 
### <a name="credential"></a>Poświadczenie 
 
Tylko usługi Azure Key Vault i `SHARED ACCESS SIGNATURE` tożsamości są obsługiwane. Użytkownicy systemu Windows nie są obsługiwane.
 
Zobacz [poświadczenie tworzenia](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) i [POŚWIADCZEŃ ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql). 
 
### <a name="cryptographic-providers"></a>Dostawców usług kryptograficznych.

Zarządzane wystąpienia nie może uzyskać dostępu pliki, więc nie można utworzyć dostawcy usług kryptograficznych:
- `CREATE CRYPTOGRAPHIC PROVIDER` nie jest obsługiwane. Zobacz [Tworzenie dostawcy usług KRYPTOGRAFICZNYCH](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` nie jest obsługiwane. Zobacz [dostawcy usług KRYPTOGRAFICZNYCH ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql). 

### <a name="collation"></a>Sortowanie 
 
Sortowania serwera jest `SQL_Latin1_General_CP1_CI_AS` i nie można zmienić. Zobacz [sortowania](https://docs.microsoft.com/sql/t-sql/statements/collations).
 
### <a name="database-options"></a>Opcje bazy danych 
 
- Wiele plików dziennika nie są obsługiwane. 
- Obiekty w pamięci nie są obsługiwane w warstwie usług ogólnego przeznaczenia.  
- Ma limitu 280 plików dla każdego wystąpienia, co oznacza maksymalny 280 plików na bazę danych. Plików dziennika i dane są wliczane do tego limitu.  
- Baza danych nie może zawierać grup plików zawierających dane filestream.  Przywracanie zakończy się niepowodzeniem, jeśli zawiera bak `FILESTREAM` danych.  
- Każdy plik znajduje się w magazynie Azure Premium. We/Wy i przepływności na plik są zależne od rozmiar każdego pliku, w taki sam sposób jak w przypadku dysków Azure Premium Storage. Zobacz [wydajności dysków Azure — warstwa Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)  
 
#### <a name="create-database-statement"></a>Instrukcja CREATE DATABASE

Poniżej przedstawiono `CREATE DATABASE` ograniczenia: 
- Nie można zdefiniować plików i grup plików.  
- `CONTAINMENT` Opcja nie jest obsługiwana.  
- `WITH`Opcje nie są obsługiwane.  
   > [!TIP]
   > Jako obejście, za pomocą `ALTER DATABASE` po `CREATE DATABASE` można ustawić opcji bazy danych, aby dodać pliki lub ustawienie zawierania.  

- `FOR ATTACH` opcja nie jest obsługiwana. 
- `AS SNAPSHOT OF` opcja nie jest obsługiwana. 

Aby uzyskać więcej informacji, zobacz [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrukcja ALTER DATABASE

Niektóre właściwości pliku nie można ustawić lub zmienić:
- Nie można określić ścieżki pliku `ALTER DATABASE ADD FILE (FILENAME='path')` instrukcji T-SQL. Usuń `FILENAME` ze skryptu, ponieważ kod zarządzany wystąpienie automatycznie umieszcza pliki.  
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

Modyfikowanie nazwa nie jest obsługiwana.

Aby uzyskać więcej informacji, zobacz [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="database-mirroring"></a>Dublowanie bazy danych

Funkcja dublowania bazy danych nie jest obsługiwane.
 - `ALTER DATABASE SET PARTNER` i `SET WITNESS` opcje nie są obsługiwane.
 - `CREATE ENDPOINT … FOR DATABASE_MIRRORING` nie jest obsługiwane.

Aby uzyskać więcej informacji, zobacz [ALTER DATABASE SET PARTNER i SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) i [CREATE ENDPOINT... DLA DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="dbcc"></a>DBCC 
 
Nieudokumentowanej instrukcji DBCC, które są włączone w programie SQL Server nie są obsługiwane w przypadku zarządzanych.
- `Trace Flags` nie są obsługiwane. Zobacz [flagi śledzenia](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` nie jest obsługiwane. Zobacz [polecenia DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` nie jest obsługiwane. Zobacz [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="extended-events"></a>Rozszerzone zdarzenia 

Niektóre elementy docelowe właściwe dla systemu Windows dla systemu XEvents nie są obsługiwane:
- `etw_classic_sync target` nie jest obsługiwane. Magazyn `.xel` magazynu obiektów blob plików na platformie Azure. Zobacz [docelowej etw_classic_sync](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etwclassicsynctarget-target). 
- `event_file target`nie jest obsługiwane. Magazyn `.xel` magazynu obiektów blob plików na platformie Azure. Zobacz [docelowej event_file](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#eventfile-target).

### <a name="external-libraries"></a>Zewnętrznej biblioteki

W — baza danych R i Python zewnętrznej biblioteki nie są jeszcze obsługiwane. Zobacz [komputera serwera SQL szkoleniowe dotyczące usługi](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FILESTREAM i Filetable

- danych FILESTREAM nie jest obsługiwane. 
- Baza danych nie może zawierać grupy plików z `FILESTREAM` danych
- `FILETABLE` nie jest obsługiwane
- Tabele nie mogą mieć `FILESTREAM` typów
- Nie są obsługiwane następujące funkcje:
 - `GetPathLocator()` 
 - `GET_FILESTREAM_TRANSACTION_CONTEXT()` 
 - `PathName()` 
 - `GetFileNamespacePath()` 
 - `FileTableRootPath()` 

Aby uzyskać więcej informacji, zobacz [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) i [Filetable](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Semantycznego wyszukiwania pełnotekstowego

[Wyszukiwanie semantycznego](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) nie jest obsługiwane.

### <a name="linked-servers"></a>Serwery połączone
 
Połączone serwery w wystąpieniu zarządzane obsługują ograniczoną liczbę elementów docelowych: 
- Obsługiwane elementy docelowe: SQL Server i bazy danych SQL
- Nieobsługiwane elementy docelowe: pliki usług Analysis Services i innych RDBMS.

Operacje

- Wystąpienie między zapisu transakcje nie są obsługiwane.
- `sp_dropserver` jest obsługiwana przez usunięcie połączonego serwera. Zobacz [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` funkcja może służyć do wykonywania zapytań tylko w wystąpieniach programu SQL Server (albo zarządzane lokalnie, lub w przypadku maszyn wirtualnych). Zobacz [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` funkcja może służyć do wykonywania zapytań tylko w wystąpieniach programu SQL Server (albo zarządzane lokalnie, lub w przypadku maszyn wirtualnych). Tylko `SQLNCLI`, `SQLNCLI11`, i `SQLOLEDB` wartości są obsługiwane jako dostawcy. Na przykład: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zobacz [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
 
### <a name="logins--users"></a>Logowania / użytkownicy 

- Utworzyć kont logowania SQL `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, i `FROM SID` są obsługiwane. Zobacz [logowania Utwórz](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Nazwy logowania systemu Windows utworzonych za pomocą `CREATE LOGIN ... FROM WINDOWS` składni nie są obsługiwane.
- Azure użytkownika usługi Active Directory (Azure AD), który utworzył wystąpienie ma [nieograniczonych uprawnień administratora](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#unrestricted-administrative-accounts).
- Użytkownicy inni niż administrator usługi Azure Active Directory (Azure AD) poziom bazy danych użytkowników mogą być tworzone przy użyciu `CREATE USER ... FROM EXTERNAL PROVIDER` składni. Zobacz [tworzenia użytkownika... Z ZEWNĘTRZNEGO DOSTAWCY](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users)
 
### <a name="polybase"></a>Program Polybase

Tabele zewnętrzne odwołujące się do plików w systemie plików HDFS lub Azure magazynu obiektów blob nie są obsługiwane. Informacje o programie Polybase, zobacz [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikacja 
 
Replikacja nie jest jeszcze obsługiwana. Aby uzyskać informacje o replikacji, zobacz [replikacji programu SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication).
 
### <a name="restore-statement"></a>Przywróć — instrukcja 
 
- Obsługiwanych składni  
   - `RESTORE DATABASE` 
   - `RESTORE FILELISTONLY ONLY` 
   - `RESTORE HEADER ONLY` 
   - `RESTORE LABELONLY ONLY` 
   - `RESTORE VERIFYONLY ONLY` 
- Nieobsługiwana składnia 
   - `RESTORE LOG ONLY` 
   - `RESTORE REWINDONLY ONLY`
- Element źródłowy  
 - `FROM URL` (Magazyn obiektów blob platformy azure) jest tylko obsługiwaną opcją.
 - `FROM DISK`/`TAPE`/ urządzenie kopii zapasowej nie jest obsługiwane.
 - Zestawy kopii zapasowych nie są obsługiwane. 
- `WITH` Opcje nie są obsługiwane (nr `DIFFERENTIAL`, `STATS`itp.)     
- `ASYNC RESTORE` -Restore będzie kontynuowane, nawet w przypadku przerwania połączenia klienta. Jeśli połączenie zostanie porzucone, można sprawdzić `sys.dm_operation_status` widok stanu operacji przywracania (a także tworzenie i PORZUCENIA bazy danych). Zobacz [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  
 
Następujące opcje bazy danych są zastąpione/zestawu i nie można zmienić później:  
- `NEW_BROKER` (Jeśli plik bak nie jest włączony broker)  
- `ENABLE_BROKER` (Jeśli plik bak nie jest włączony broker)  
- `AUTO_CLOSE=OFF` (jeśli bazy danych w pliku bak jest `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (jeśli bazy danych w pliku bak jest `SIMPLE` lub `BULK_LOGGED` trybie odzyskiwania)
- Grupa plików zoptymalizowanych pod kątem pamięci zostanie dodany i wywołuje XTP, jeśli nie był plik bak źródła  
- Wszystkie istniejące grupy plików zoptymalizowanych pod kątem pamięci jest zmieniana na XTP  
- `SINGLE_USER` i `RESTRICTED_USER` opcje są konwertowane na `MULTI_USER`   
Ograniczenia:  
- `.BAK` Nie można przywrócić pliki zawierające wiele zestawów kopii zapasowych. 
- `.BAK` Nie można przywrócić pliki zawierające wiele plików dziennika. 
- Przywracanie zakończy się niepowodzeniem, jeśli zawiera bak `FILESTREAM` danych.
- Obecnie nie można przywrócić kopii zapasowych zawierający baz danych, które obiekty były aktywne w pamięci.  
- Kopie zapasowe baz danych, gdzie w pewnym momencie obiektów w pamięci istniał zawierający obecnie nie można przywrócić.   
- Obecnie nie można przywrócić kopii zapasowych zawierający baz danych w trybie tylko do odczytu. Wkrótce zostaną usunięte tego ograniczenia.   
 
Aby uzyskać informacje na temat instrukcji Restore, zobacz [PRZYWRÓCIĆ instrukcje](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Usługa Service broker 
 
- Broker usług między wystąpienia nie jest obsługiwane. 
 - `sys.routes` -Wymaganie wstępne: Wybierz adres z sys.routes. Adres musi być lokalny na każdej trasy. Zobacz [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
 - `CREATE ROUTE` -Nie można wykonać `CREATE ROUTE` z `ADDRESS` innych niż `LOCAL`. Zobacz [Utwórz TRASĘ](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
 - `ALTER ROUTE` Nie można `ALTER ROUTE` z `ADDRESS` innych niż `LOCAL`. Zobacz [trasy ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  
 
### <a name="service-key-and-service-master-key"></a>Usługa klucza głównego klucza i usługi 
 
- [Kopia zapasowa klucza głównego](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database) 
- [Przywróć klucz główny](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database) 
- [Kopia zapasowa klucza głównego usługi](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database) 
- [Przywróć klucz główny usługi](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database) 
 
### <a name="stored-procedures-functions-triggers"></a>Funkcje procedur składowanych, wyzwalaczy 
 
- `NATIVE_COMPILATION` obecnie nie jest obsługiwana. 
- Następujące [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) opcje nie są obsługiwane: 
 - `allow polybase export` 
 - `allow updates` 
 - `filestream_access_level` 
 - `max text repl size` 
 - `remote data archive` 
 - `remote proc trans` 
- `sp_execute_external_scripts` nie jest obsługiwane. Zobacz [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` nie jest obsługiwane. Zobacz [procedury xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` nie są obsługiwane, w tym `sp_addextendedproc` i `sp_dropextendedproc`. Zobacz [rozszerzone procedury składowane](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db`, `sp_attach_single_file_db`, i `sp_detach_db` nie są obsługiwane. Zobacz [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), i [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).
- `sp_renamedb` nie jest obsługiwane. Zobacz [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql).

### <a name="sql-server-agent"></a>Program SQL Server Agent 
 
- Ustawienia agenta SQL jest tylko do odczytu. Procedura `sp_set_agent_properties` nie jest obsługiwana w przypadku zarządzanych.  
- Zadania — tylko kroki zadania T-SQL są obecnie obsługiwane (kolejny krok nie zostanie dodany w publicznej wersji zapoznawczej).
 - SSIS nie jest jeszcze obsługiwany. 
 - Replikacja nie jest jeszcze obsługiwana.  
  - Czytnik dziennika transakcji nie jest jeszcze obsługiwany.  
  - Migawki nie jest jeszcze obsługiwany.  
  - Dystrybutor nie jest jeszcze obsługiwany.  
  - Scalanie nie jest obsługiwane.  
  - Czytnik kolejki nie jest obsługiwane.  
 - Powłoka poleceń nie jest jeszcze obsługiwany. 
  - Zarządzane wystąpienia Brak dostępu do zasobów zewnętrznych (na przykład udziałów sieciowych za pośrednictwem robocopy).  
 - PowerShell nie jest jeszcze obsługiwany.
 - Usługi Analysis Services nie są obsługiwane.  
- Powiadomienia są obsługiwane częściowo.
 - Powiadomienia e-mail jest obsługiwany, wymaga skonfigurowania profil poczty bazy danych. Może istnieć tylko jedna baza danych profilu poczty i musi zostać wywołana `AzureManagedInstance_dbmail_profile` w publicznej wersji zapoznawczej (tymczasowe ograniczenie).  
 - Pagera nie jest obsługiwane.  
 - NetSend nie jest obsługiwane. 
 - Alerty nie jeszcze nie są obsługiwane.
 - Serwery proxy nie są obsługiwane.  
- Dziennik zdarzeń nie jest obsługiwane. 
 
Następujące funkcje nie są obecnie obsługiwane, ale będzie można w przyszłości:  
- Serwery proxy
- Planowanie zadań na bezczynności Procesora 
- Włączanie i wyłączanie agenta
- Alerty

Informacje dla agenta programu SQL Server, zobacz [programu SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).
 
### <a name="tables"></a>Tabele 

Następujące nie są obsługiwane: 
- `FILESTREAM` 
- `FILETABLE` 
- `EXTERNAL TABLE` 
- `MEMORY_OPTIMIZED`  

Aby uzyskać informacje dotyczące tworzenia i modyfikowania tabel, zobacz [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) i [instrukcji ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).
 
## <a name="Changes"></a> Zmiany sposobu działania 
 
Następujące zmienne, funkcje i widoki zwraca różne wyniki:  
- `SERVERPROPERTY('EngineEdition')` Zwraca wartość 8. Ta właściwość jednoznacznie identyfikuje wystąpienie zarządzane. Zobacz [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Zwraca nazwę krótką wystąpienia, na przykład "MójSerwer". Zobacz [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Zwraca pełną "składnika" nazwą DNS, na przykład Moje instance.wcus17662feb9ce98.database.windows.net zarządzane. Zobacz [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -Zwraca pełną "składnika" nazwą DNS, takich jak `myinstance.domain.database.windows.net` dla właściwości "name" i "data_source". Zobacz [SYS. SERWERY](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVERNAME` Zwraca pełną "składnika" nazwą DNS, takich jak `my-managed-instance.wcus17662feb9ce98.database.windows.net`. Zobacz [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -Zwraca pełną "składnika" nazwą DNS, takich jak `myinstance.domain.database.windows.net` dla właściwości "name" i "data_source". Zobacz [SYS. SERWERY](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVICENAME` Zwraca wartość NULL, ponieważ nie ma sensu w środowisku zarządzane wystąpienia. Zobacz [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).   
- `SUSER_ID` jest obsługiwana. Zwraca wartość NULL, jeśli logowanie usługi AAD nie jest sys.syslogins. Zobacz [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` nie jest obsługiwane. Zwraca nieodpowiedniego (tymczasowego znany problem). Zobacz [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 
- `GETDATE()` i inne funkcje wbudowane daty/godziny zawsze zwraca czas w strefie czasowej UTC. Zobacz [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Znane problemy i ograniczenia

### <a name="tempdb-size"></a>Rozmiar bazy danych TEMPDB

`tempdb` dzieli się na 12 pliki z maksymalny rozmiar 14 GB na plik. Nie można zmienić tego maksymalny rozmiar każdego pliku i nie można dodać nowe pliki do `tempdb`. Wkrótce zostaną usunięte tego ograniczenia. Niektórych kwerend może zwrócić błąd, jeśli potrzebna jest więcej niż 168 GB `tempdb`.

### <a name="exceeding-storage-space-with-small-database-files"></a>Powyżej miejsca do magazynowania z plikami małych baz danych

Każde wystąpienie zarządzania ma do 35 TB magazynu zarezerwowane dla miejsca na dysku Premium Azure i wszystkich plików bazy danych znajduje się na innym dysku fizycznym. Rozmiary dysków może być 128 GB, 256 GB, 512 GB, 1 TB lub 4 TB. Nieużywane miejsce na dysku nie jest pobierana, ale suma rozmiarów dysków Premium Azure nie może przekraczać 35 TB. W niektórych przypadkach zarządzane wystąpienia, które nie wymagają 8 TB w sumie może przekroczyć 35 TB Azure limit rozmiaru magazynu z powodu wewnętrznego fragmentacji. 

Na przykład wystąpieniem zarządzane może mieć jeden plik o rozmiarze 1,2 TB, który używa dysku 4 TB i 248 pliki z 1 GB każda są umieszczane na dyskach 248 o rozmiarze 128 GB. W tym przykładzie rozmiar całkowitą dysku magazynu jest 1 x 4 TB + 248 x 128 GB = 35 TB. Jednakże rozmiar całkowitą wystąpienia zarezerwowana dla baz danych jest 1 x 1.2 TB + 248 x 1 GB = 1,4 TB. Potwierdza to, że w pewnych okolicznościach, ze względu na bardzo szczegółowej rozkład plików, wystąpienie usługi zarządzania może osiągnąć limit magazynu Azure Premium dysku, gdzie może nie oczekujesz. 

Nie byłoby błąd braku na istniejących baz danych i może zwiększyć bez żadnych problemów, jeśli nie są dodawane nowe pliki, ale nie można utworzyć ani przywrócić, ponieważ nie jest wystarczająco dużo miejsca na nowe dyski twarde, nawet jeśli całkowity rozmiar wszystkich baz danych nie osiąga t nowych baz danych limit rozmiaru jego wystąpienia. Błąd, który jest zwracany nie jest w tym przypadku Wyczyść.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Przywróć niepoprawnej konfiguracji klucza sygnatury dostępu Współdzielonego w bazie danych

`RESTORE DATABASE` które odczytuje plik bak może być stale ponawianie próby odczytać plik bak i zwróciła błąd od dłuższego czasu, jeśli sygnatura dostępu współdzielonego w `CREDENTIAL` jest niepoprawny. Wykonaj Przywracanie HEADERONLY przed przywróceniem bazy danych należy upewnić się, że klucz sygnatury dostępu Współdzielonego jest poprawna.
Upewnij się, że po usunięciu wiodące `?` z klucza sygnatury dostępu Współdzielonego wygenerowanych przy użyciu portalu Azure.

### <a name="tooling"></a>Narzędzia

SQL Server Management Studio i SQL Server Data Tools może być pewne problemy podczas uzyskiwania dostępu do wystąpienia zarządzane. Wszystkie narzędzia problemy zostaną rozwiązane przed ogólnej dostępności.

### <a name="incorrect-database-names"></a>Nazwy niepoprawne bazy danych

Wystąpienia zarządzane mogą być wyświetlane wartości identyfikatora guid zamiast nazwy bazy danych podczas przywracania lub komunikaty o błędach. Te problemy zostaną poprawione przed ogólnej dostępności.

### <a name="database-mail-profile"></a>Profil poczty bazy danych
Może istnieć tylko jedna baza danych profilu poczty i musi zostać wywołana `AzureManagedInstance_dbmail_profile`. Jest to tymczasowe ograniczenie, które będą wkrótce usunięte.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji o zarządzanych wystąpienia, zobacz [co to jest wystąpieniem zarządzane?](sql-database-managed-instance.md)
- Dla funkcji i listy porównanie, zobacz [wspólne funkcje SQL](sql-database-features.md).
- Samouczek pokazuje sposób tworzenia nowego wystąpienia zarządzane, zobacz [Tworzenie wystąpienia zarządzane](sql-database-managed-instance-create-tutorial-portal.md).
