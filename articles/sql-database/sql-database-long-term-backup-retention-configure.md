---
title: Zarządzanie długoterminowego przechowywania kopii zapasowych bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie przechowywania automatycznych kopii zapasowych w usłudze SQL Azure storage, a następnie przywróci je
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: f7125a18aa2496ebe8367443a67502a7a7dbac02
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128518"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Zarządzanie długoterminowego przechowywania kopii zapasowych bazy danych SQL Azure

Można skonfigurować bazy danych Azure SQL z [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md) (od lewej do prawej) automatycznie przechowywania kopii zapasowych w magazynie obiektów blob platformy Azure przez maksymalnie 10 lat zasad. Można odzyskać bazy danych przy użyciu tych kopii zapasowych za pomocą portalu Azure lub programu PowerShell.

> [!NOTE]
> W ramach początkowa wersja Preview tę funkcję w października 2016 kopie zapasowe są przechowywane w magazynie usług odzyskiwania usług Azure. Ta aktualizacja usuwa to zależność, ale dla zgodności z poprzednimi wersjami oryginalnego interfejsu API jest obsługiwane do 31 maja 2018. Jeśli potrzebujesz wchodzić w interakcje z kopii zapasowych w magazynie Azure Services Recovery, zobacz [długoterminowego przechowywania kopii zapasowych za pomocą usługi Azure Recovery Services vault](sql-database-long-term-backup-retention-configure-vault.md). 

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Użyj portalu Azure do konfigurowania zasad przechowywania długoterminowego i przywracania kopii zapasowych

Poniższe sekcje pokazują, jak korzystać z portalu Azure do skonfigurowania długoterminowego przechowywania, wyświetlić w długoterminowego przechowywania kopii zapasowych i przywracanie kopii zapasowej z długoterminowego przechowywania.

### <a name="configure-long-term-retention-policies"></a>Konfigurowanie zasad przechowywania długoterminowego

Można skonfigurować bazy danych SQL do [przechowywania automatycznych kopii zapasowych](sql-database-long-term-retention.md) przez okres dłuższy niż okres przechowywania warstwę usług. 

1. W portalu Azure, wybierz program SQL server, a następnie kliknij przycisk **długoterminowego przechowywania kopii zapasowych**.

   ![link długoterminowego przechowywania kopii zapasowych](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Na **Konfigurowanie zasad** , a następnie wybierz bazę danych, na którym chcesz ustawić lub zmodyfikować zasady przechowywania kopii zapasowych długoterminowej.

   ![Wybierz bazę danych](./media/sql-database-long-term-retention/ltr-configure-select-database.png)

3. W **Konfigurowanie zasad** okienka, określ, czy chcesz zachować co tydzień, miesięcznego lub rocznego kopii zapasowych i określ okres przechowywania dla każdego. 

   ![Konfigurowanie zasad](./media/sql-database-long-term-retention/ltr-configure-policies.png)

4. Po zakończeniu kliknij przycisk **Zastosuj**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Wyświetlanie kopii zapasowych i przywracanie z kopii zapasowej przy użyciu portalu Azure

Wyświetl kopie zapasowe, które zostaną zachowane dla określonej bazy danych z zasad od lewej do prawej i przywrócenie z tych kopii zapasowych. 

1. W portalu Azure, wybierz program SQL server, a następnie kliknij przycisk **długoterminowego przechowywania kopii zapasowych**.

   ![link długoterminowego przechowywania kopii zapasowych](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Na **dostępnych kopii zapasowych** , a następnie wybierz bazę danych, dla którego chcesz wyświetlić dostępnych kopii zapasowych.

   ![Wybierz bazę danych](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. W **dostępnych kopii zapasowych** okienku przejrzyj dostępnych kopii zapasowych. 

   ![Wyświetl kopie zapasowe](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Wybierz kopię zapasową, z którego chcesz przywrócić, a następnie określ nazwę nowej bazy danych.

   ![Przywracanie](./media/sql-database-long-term-retention/ltr-restore.png)

5. Kliknij przycisk **OK** do przywrócenia bazy danych z kopii zapasowej w magazynie Azure SQL z bazą danych.

6. Na pasku narzędzi kliknij ikonę powiadomienia, aby wyświetlić stan zadania przywracania.

   ![postęp zadania przywracania z magazynu](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Po zakończeniu zadania przywracania, otwórz **baz danych SQL** strony w celu wyświetlenia nowo przywróconej bazy danych.

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak [wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Za pomocą programu PowerShell do konfigurowania zasad przechowywania długoterminowego i przywracania kopii zapasowych

Poniższe sekcje pokazują, jak skonfigurować długoterminowego przechowywania kopii zapasowych, wyświetlić kopii zapasowych magazynu Azure SQL i przywracanie z kopii zapasowej w magazynie Azure SQL przy użyciu programu PowerShell.

> [!IMPORTANT]
> Interfejs API V2 od lewej do prawej jest obsługiwany w następujących wersjach środowiska PowerShell:
- [AzureRM.Sql 4.5.0](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0) lub nowsza
- [AzureRM 6.1.0](https://www.powershellgallery.com/packages/AzureRM/6.1.0) lub nowsza
> 

### <a name="create-an-ltr-policy"></a>Tworzenie zasad od lewej do prawej

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Wyświetl zasady od lewej do prawej
W tym przykładzie pokazano, jak wyświetlić listę zasad od lewej do prawej w ramach serwera

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Czyszczenie zasad od lewej do prawej
W tym przykładzie pokazano, jak czyszczenie zasad od lewej do prawej z bazy danych

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Wyświetl kopie zapasowe od lewej do prawej

W tym przykładzie przedstawiono sposób listę kopii zapasowych od lewej do prawej w ramach serwera. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Usunąć kopie zapasowe od lewej do prawej

Ten przykład przedstawia sposób usuwania od lewej do prawej kopii zapasowej z listy kopii zapasowych.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Przywracanie z kopii zapasowych od lewej do prawej
Ten przykład przedstawia sposób przywracania z kopii zapasowej od lewej do prawej. Należy pamiętać, ten interfejs nie został zmieniony, ale parametr identyfikator zasobu wymaga teraz identyfikator zasobu kopii zapasowej od lewej do prawej. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> W tym miejscu możesz nawiązać przywróconej bazy danych przy użyciu programu SQL Server Management Studio do wykonywania wymaganych zadań, np. wyodrębnienie bit danych z przywróconej bazy danych ma zostać skopiowany do istniejącej bazy danych lub usuń istniejącą bazę danych i zmienić nazwę przywróconej Baza danych do nazwy istniejącej bazy danych. Zobacz [punktu w czasie przywracania](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych generowanych przez usługi, zobacz artykuł dotyczący [automatycznych kopii zapasowych](sql-database-automated-backups.md)
- Aby dowiedzieć się więcej o długoterminowym przechowywaniu kopii zapasowych, zobacz temat dotyczący [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md)
