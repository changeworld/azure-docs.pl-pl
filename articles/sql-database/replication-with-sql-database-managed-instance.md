---
title: Konfigurowanie replikacji w bazie danych wystąpienia zarządzanego
description: Dowiedz się, jak skonfigurować replikację transakcyjną między wydawcą/dystrybutorem zarządzanym wystąpieniem usługi Azure SQL Database a subskrybentem wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 9af7b471210ca3cc69428e68aef4aafaee159344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299077"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurowanie replikacji w bazie danych wystąpienia zarządzanego usługi Azure SQL Database

Replikacja transakcyjna umożliwia replikowanie danych do bazy danych wystąpienia zarządzanego usługi Azure SQL Database z bazy danych programu SQL Server lub innej bazy danych wystąpień. 

W tym artykule pokazano, jak skonfigurować replikację między wydawcą/dystrybutorem wystąpienia zarządzanego a subskrybentem wystąpienia zarządzanego. 

![Replikacja między dwoma wystąpieniami zarządzanymi](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

Replikacja transakcyjna służy również do wypychania zmian wprowadzonych w bazie danych wystąpień w wystąpieniu zarządzanym usługi Azure SQL Database w celu:

- Baza danych programu SQL Server.
- Pojedyncza baza danych w bazie danych SQL usługi Azure.
- Puli bazy danych w puli elastycznej bazy danych SQL platformy Azure.
 
Replikacja transakcyjna jest w publicznej wersji zapoznawczej w [wystąpieniu zarządzanym usługi Azure SQL Database](sql-database-managed-instance.md). Wystąpienie zarządzane może obsługiwać bazy danych wydawców, dystrybutorów i subskrybentów. Zobacz [konfiguracje replikacji transakcyjnej](sql-database-managed-instance-transactional-replication.md#common-configurations) dla dostępnych konfiguracji.

  > [!NOTE]
  > - Ten artykuł jest przeznaczony do prowadzenia użytkownika w konfigurowaniu replikacji z wystąpieniem zarządzanym usługi Azure Database od końca do końca, począwszy od tworzenia grupy zasobów. Jeśli masz już wdrożone wystąpienia zarządzane, przejdź do [kroku 4,](#4---create-a-publisher-database) aby utworzyć bazę danych wydawcy, lub [krok 6,](#6---configure-distribution) jeśli masz już bazę danych wydawcy i subskrybenta i możesz rozpocząć konfigurowanie replikacji.  
  > - Ten artykuł konfiguruje wydawcę i dystrybutora w tym samym wystąpieniu zarządzanym. Aby umieścić dystrybutora na osobnym wystąpieniu manged, zobacz samouczek [Konfigurowanie replikacji między wydawcą MI a dystrybutorem MI](sql-database-managed-instance-configure-replication-tutorial.md). 

## <a name="requirements"></a>Wymagania

Skonfigurowanie wystąpienia zarządzanego w celu pełnienia funkcji wydawcy i/lub dystrybutora wymaga:

- Że wystąpienie zarządzane przez wydawcę znajduje się w tej samej sieci wirtualnej co dystrybutor i subskrybent lub [równorzędne sieci wirtualnej](../virtual-network/tutorial-connect-virtual-networks-powershell.md) zostało ustanowione między sieciami wirtualnymi wszystkich trzech jednostek. 
- Połączenie korzysta z uwierzytelniania SQL między uczestnikami replikacji.
- Udział konta usługi Azure Storage dla katalogu roboczego replikacji.
- Port 445 (TCP wychodzących) jest otwarty w regułach zabezpieczeń sieciowej grupy zabezpieczeń dla wystąpień zarządzanych, aby uzyskać dostęp do udziału plików platformy Azure.  Jeśli wystąpi błąd "nie można połączyć \<się z nazwą konta magazynu azure> z os błąd 53", należy dodać regułę ruchu wychodzącego do sieciowej sieciowej odpowiedniej podsieci wystąpienia zarządzanego SQL.


 > [!NOTE]
 > Pojedyncze bazy danych i buforowane bazy danych w usłudze Azure SQL Database mogą być tylko subskrybentami. 


## <a name="features"></a>Funkcje

Obsługuje:

- Transakcyjna i migawka replikacji mieszaniny SQL Server lokalnie i wystąpienia zarządzane w usłudze Azure SQL Database.
- Subskrybenci mogą znajdować się w lokalnych bazach danych programu SQL Server, pojedynczych bazach danych/wystąpieniach zarządzanych w usłudze Azure SQL Database lub w puli danych elastycznych bazy danych Azure SQL Database.
- Replikacja jednokierunkowa lub dwukierunkowa.

Następujące funkcje nie są obsługiwane w wystąpieniu zarządzanym w usłudze Azure SQL Database:

- [Subskrypcje możliwe do aktualizacji](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Aktywna replikacja geograficzna](sql-database-active-geo-replication.md) z replikacją transakcyjną. Zamiast aktywnej replikacji geograficznej należy użyć [grup auto-trybu failover](sql-database-auto-failover-group.md), ale należy pamiętać, że publikacja musi zostać [ręcznie usunięta](sql-database-managed-instance-transact-sql-information.md#replication) z podstawowego wystąpienia zarządzanego i odtworzona w pomocniczym wystąpieniu zarządzanym po przekształceniu w tryb failover.  
 
## <a name="1---create-a-resource-group"></a>1 - Tworzenie grupy zasobów

Użyj [witryny Azure Portal,](https://portal.azure.com) aby `SQLMI-Repl`utworzyć grupę zasobów o nazwie .  

## <a name="2---create-managed-instances"></a>2 - Tworzenie wystąpień zarządzanych

Użyj [witryny Azure Portal,](https://portal.azure.com) aby utworzyć dwa wystąpienia zarządzane w tej samej sieci wirtualnej i [podsieci.](sql-database-managed-instance-create-tutorial-portal.md) Na przykład nazwij dwa wystąpienia zarządzane:

- `sql-mi-pub`(wraz z niektórymi znakami do randomizacji)
- `sql-mi-sub`(wraz z niektórymi znakami do randomizacji)

Należy również [skonfigurować maszynę wirtualną platformy Azure, aby połączyć się z](sql-database-managed-instance-configure-vm.md) wystąpieniami zarządzanymi usługi Azure SQL Database. 

## <a name="3---create-azure-storage-account"></a>3 - Tworzenie konta usługi Azure Storage

[Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) dla katalogu roboczego, a następnie utwórz udział [plików](../storage/files/storage-how-to-create-file-share.md) na koncie magazynu. 

Skopiuj ścieżkę udziału plików w formacie:`\\storage-account-name.file.core.windows.net\file-share-name`

Przykład: `\\replstorage.file.core.windows.net\replshare`

Skopiuj klucze dostępu do magazynu w formacie:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Przykład: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 - Tworzenie bazy danych wydawców

Połącz `sql-mi-pub` się z wystąpieniem zarządzanym przy użyciu programu SQL Server Management Studio i uruchom następujący kod Transact-SQL (T-SQL), aby utworzyć bazę danych wydawcy:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 - Tworzenie bazy danych subskrybentów

Połącz `sql-mi-sub` się z wystąpieniem zarządzanym przy użyciu programu SQL Server Management Studio i uruchom następujący kod T-SQL, aby utworzyć pustą bazę subskrybentów:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - Konfigurowanie dystrybucji

Połącz `sql-mi-pub` się z wystąpieniem zarządzanym przy użyciu programu SQL Server Management Studio i uruchom następujący kod T-SQL, aby skonfigurować bazę danych dystrybucji. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Konfigurowanie wydawcy do korzystania z dystrybutora 

W wystąpieniu `sql-mi-pub`zarządzanym wydawcy zmień wykonanie kwerendy na tryb [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) i uruchom następujący kod, aby zarejestrować nowego dystrybutora u wydawcy. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Pamiętaj, aby użyć tylko ukośników odwrotnych (`\`) dla parametru file_storage. Użycie ukośnika do przodu (`/`) może spowodować błąd podczas łączenia się z udziałem plików. 

Ten skrypt konfiguruje lokalnego wydawcę w wystąpieniu zarządzanym, dodaje połączony serwer i tworzy zestaw zadań dla agenta programu SQL Server. 

## <a name="8---create-publication-and-subscriber"></a>8 - Tworzenie publikacji i subskrybentów

Korzystając z trybu [SQLCMD,](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) uruchom następujący skrypt T-SQL, aby włączyć replikację bazy danych i skonfiguruj replikację między wydawcą, dystrybutorem i subskrybentem. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - Modyfikowanie parametrów agenta

Wystąpienie zarządzanej usługi Azure SQL Database obecnie występują pewne problemy wewnętrznej bazy danych z łącznością z agentami replikacji. Gdy ten problem jest rozwiązany, obejście, aby zwiększyć wartość limit czasu logowania dla agentów replikacji. 

Uruchom następujące polecenie T-SQL na wydawcy, aby zwiększyć limit czasu logowania: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Uruchom ponownie następujące polecenie T-SQL, aby ustawić limit czasu logowania z powrotem do wartości domyślnej, jeśli jest to konieczne:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Uruchom ponownie wszystkich trzech agentów, aby zastosować te zmiany. 

## <a name="10---test-replication"></a>10 - Replikacja testowa

Po skonfigurowaniu replikacji można ją przetestować, wstawiając nowe elementy do wydawcy i obserwując, jak zmiany są propagowane przez subskrybenta. 

Uruchom następujący fragment kodu T-SQL, aby wyświetlić wiersze na subskrybenta:

```sql
select * from dbo.ReplTest
```

Uruchom następujący fragment kodu T-SQL, aby wstawić dodatkowe wiersze w wydawcy, a następnie ponownie sprawdź wiersze subskrybenta. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć publikację, uruchom następujące polecenie T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Aby usunąć opcję replikacji z bazy danych, uruchom następujące polecenie T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Aby wyłączyć publikowanie i dystrybucję, uruchom następujące polecenie T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Zasoby platformy Azure można oczyścić, [usuwając zasoby wystąpienia zarządzanego z grupy zasobów,](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) a następnie usuwając grupę `SQLMI-Repl`zasobów . 

   
## <a name="see-also"></a>Zobacz też

- [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md)
- [Samouczek: Konfigurowanie replikacji transakcyjnej między wydawcą MI a subskrybentem programu SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
