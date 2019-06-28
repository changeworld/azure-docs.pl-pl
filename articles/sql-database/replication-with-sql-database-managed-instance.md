---
title: Konfigurowanie replikacji w bazie danych wystąpienia zarządzanego usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Informacje na temat konfigurowania replikacji transakcyjnej w bazie danych wystąpienia zarządzanego usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: e4d056aacf8f3969b645747e2303574f3fea3bda
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357122"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurowanie replikacji w bazie danych wystąpienia zarządzanego usługi Azure SQL Database

Replikacja transakcyjna umożliwia replikowanie danych do wystąpienia zarządzanego usługi Azure SQL Database bazy danych z bazy danych programu SQL Server lub innego wystąpienia bazy danych. 

Replikacja transakcyjna umożliwia również wypchnąć zmiany wprowadzone w wystąpieniu bazy danych w wystąpieniu zarządzanym usługi Azure SQL Database do:

- W przypadku bazy danych programu SQL Server.
- Pojedynczej bazy danych w usłudze Azure SQL Database.
- Bazy danych w puli w puli elastycznej bazy danych SQL Azure.
 
Replikacja transakcyjna jest w publicznej wersji zapoznawczej na [wystąpienie zarządzane usługi Azure SQL Database](sql-database-managed-instance.md). Wystąpienie zarządzane umożliwia hostowanie bazy danych wydawcy dystrybutora i subskrybenta. Zobacz [konfiguracji replikacji transakcyjnej](sql-database-managed-instance-transactional-replication.md#common-configurations) dla dostępnymi konfiguracjami.

  > [!NOTE]
  > Ten artykuł jest przeznaczony do przewodnika użytkownika podczas konfigurowania replikacji za pomocą usługi Azure Database wystąpienie zarządzane od końca do końca, począwszy od tworzenia grupy zasobów. Jeśli już zarządzany wystąpieniach wdrożonych, przejdź do sekcji [kroku 4](#4---create-a-publisher-database) do utworzenia bazy danych wydawcy lub [kroku 6](#6---configure-distribution) Jeśli już masz bazę danych wydawcą a transakcyjnym subskrybentem, a gotowe do rozpoczęcia Konfigurowanie replikacji.  

## <a name="requirements"></a>Wymagania

Konfigurowanie wystąpienia zarządzanego do działania jako wydawca i/lub dystrybutora wymaga:

- Czy wystąpienia zarządzanego jest aktualnie nie uczestniczy w relacji replikacji geograficznej.
- Wystąpienie zarządzane wydawcy znajduje się w tej samej sieci wirtualnej jako dystrybutora i subskrybenta lub [komunikacja równorzędna sieci wirtualnych](../virtual-network/tutorial-connect-virtual-networks-powershell.md) została ustanowiona między sieciami wirtualnymi wszystkich trzech jednostek. 
- Połączenie korzysta z uwierzytelniania SQL między uczestnikami replikacji.
- Udział konta usługi Azure Storage dla replikacji katalogu roboczego.
- Port 445 (ruch wychodzący protokołu TCP) jest otwarty w reguły zabezpieczeń sieciowej grupy zabezpieczeń dla wystąpienia zarządzanego dostępu do udziału plików platformy Azure. 


 > [!NOTE]
 > Pojedynczych baz danych i baz danych w puli usługi Azure SQL Database może być tylko subskrybentów. 


## <a name="features"></a>Funkcje

Obsługuje:

- Replikacji transakcyjnej i migawki różnych lokalnego programu SQL Server i wystąpienia zarządzanego usługi Azure SQL Database.
- Subskrybenci mogą znajdować się w lokalnych baz danych programu SQL Server, pojedynczej bazy danych/wystąpienia zarządzane przez usługę Azure SQL Database lub bazy danych w puli w elastycznych pulach usługi Azure SQL Database.
- Jednokierunkowa lub replikację dwukierunkową.

Następujące funkcje nie są obsługiwane w wystąpieniu zarządzanym usługi Azure SQL Database:

- [Aktualizowalne subskrypcje](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Replikacji dla aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) i [automatyczny tryb failover grupy](sql-database-auto-failover-group.md) nie powinny być używane, jeśli skonfigurowano replikacji transakcyjnej.
 
## <a name="1---create-a-resource-group"></a>1 — Tworzenie grupy zasobów

Użyj [witryny Azure portal](https://portal.azure.com) do utworzenia grupy zasobów o nazwie `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 — Tworzenie wystąpienia zarządzanego

Użyj [witryny Azure portal](https://portal.azure.com) utworzyć dwa [wystąpienia zarządzane](sql-database-managed-instance-create-tutorial-portal.md) na tej samej sieci wirtualnej i podsieci. Powinno się nazywać na dwa wystąpienia zarządzanego:

- `sql-mi-pub`
- `sql-mi-sub`

Będzie trzeba będzie również [skonfigurować Maszynę wirtualną platformy Azure do łączenia z](sql-database-managed-instance-configure-vm.md) wystąpienia zarządzane usługi Azure SQL Database. 

## <a name="3---create-azure-storage-account"></a>3 — Tworzenie konta usługi Azure Storage

[Tworzenie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) dla katalogu roboczego, a następnie utwórz [udziału plików](../storage/files/storage-how-to-create-file-share.md) w ramach konta magazynu. 

Skopiuj ścieżkę udziału pliku w formacie: `\\storage-account-name.file.core.windows.net\file-share-name`

Kopiowanie kluczy dostępu do magazynu w formacie: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 Aby uzyskać więcej informacji, zobacz temat [View and copy storage access keys](../storage/common/storage-account-manage.md#access-keys) (Wyświetlanie i kopiowanie kluczy dostępu kopiowania). 

## <a name="4---create-a-publisher-database"></a>4 — tworzenie bazy danych wydawcy

Połączyć się z Twojego `sql-mi-pub` zarządzane wystąpienia przy użyciu programu SQL Server Management Studio i uruchom następujący kod języka Transact-SQL (T-SQL), aby utworzyć bazę danych wydawcy:

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

## <a name="5---create-a-subscriber-database"></a>5 — Tworzenie bazy danych subskrybenta

Połączyć się z Twojego `sql-mi-sub` zarządzane wystąpienia przy użyciu programu SQL Server Management Studio i uruchom następujący kod T-SQL, aby utworzyć bazę danych subskrybenta pusta:

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

## <a name="6---configure-distribution"></a>6 — Konfigurowanie dystrybucji

Połączyć się z Twojego `sql-mi-pub` zarządzane wystąpienia przy użyciu programu SQL Server Management Studio i uruchom poniższy kod T-SQL, aby skonfigurować bazę danych dystrybucji. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 — Konfigurowanie wydawcy dystrybutora używać 

Wystąpienie zarządzane wydawcy `sql-mi-pub`, wykonanie zapytania, aby zmienić [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) trybie i uruchomieniu następujący kod, aby zarejestrować nowe dystrybutorze z wydawcy. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


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

Ten skrypt umożliwia skonfigurowanie lokalnego wydawcy na wystąpieniu zarządzanym, dodaje połączonego serwera i tworzy zbiór zadań dla programu SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 — Tworzenie publikacji i subskrybenta

Za pomocą [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) trybu, uruchom następujący skrypt języka T-SQL, aby włączyć replikację dla bazy danych i skonfigurować replikację między wydawcy, dystrybutora i subskrybenta. 

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
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 — modyfikowanie parametrów agenta

Wystąpienie zarządzane usługi Azure SQL Database jest obecnie problemy niektóre wewnętrznej bazy danych z łącznością z agentów replikacji. Chociaż ten problem nie zostanie skierowane opisany, obejście, aby zwiększyć wartość limitu czasu logowania dla agentów replikacji. 

Uruchom następujące polecenie języka T-SQL na wydawcy, aby zwiększyć limit czasu logowania: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Uruchom następujące polecenie języka T-SQL ponownie, aby ustawić limit czasu logowania powrót do wartości domyślnej, należy należy to zrobić:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Ponowne uruchomienie wszystkich trzech agentów, aby zastosować te zmiany. 

## <a name="10---test-replication"></a>10 — Testuj replikację

Po skonfigurowaniu replikacji można ją przetestować, wstawianie nowych elementów na wydawcy i obejrzeć zmiany są propagowane do subskrybenta. 

Uruchom poniższy fragment kodu języka T-SQL, aby wyświetlić wiersze na subskrybencie:

```sql
select * from dbo.ReplTest
```

Uruchom poniższy fragment kodu języka T-SQL, aby wstawić dodatkowe wiersze na wydawcy, a następnie sprawdź wiersze na subskrybencie. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby porzucić publikację, uruchom następujące polecenie języka T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Aby usunąć opcji replikacji z bazy danych, uruchom następujące polecenie języka T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Aby wyłączyć publikowanie i dystrybucję, uruchom następujące polecenie języka T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Możesz wyczyścić zasoby platformy Azure przez [usuwanie zasobów wystąpienia zarządzanego z grupy zasobów](../azure-resource-manager/manage-resources-portal.md#delete-resources) , a następnie usunięcie grupy zasobów `SQLMI-Repl`. 

   
## <a name="see-also"></a>Zobacz też

- [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md)
- [Co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
