---
title: Zarządzanie długotrwałym przechowywaniem kopii zapasowych usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Informacje o sposobie przechowywania automatycznych kopii zapasowych w magazynie usługi Azure SQL, a następnie przywróć je
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 24d453dc705eb2d0ee7cb77f2ec247845247d0a8
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113635"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Zarządzanie długotrwałym przechowywaniem kopii zapasowych usługi Azure SQL Database

Można skonfigurować usługi Azure SQL database za pomocą [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md) zasad (od lewej do prawej) automatycznie przechowywania kopii zapasowych w magazynie obiektów blob platformy Azure przez maksymalnie 10 lat. Można odzyskać bazę danych przy użyciu tych kopii zapasowych przy użyciu witryny Azure portal lub programu PowerShell.

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Użyj witryny Azure portal, aby skonfigurować długoterminowe zasady przechowywania i przywracanie kopii zapasowych

Poniższe sekcje pokazują, jak skonfigurować długoterminowe przechowywanie, wyświetlanie kopii zapasowych podlegających długoterminowemu przechowywaniu i długoterminowym przechowywaniu kopii zapasowej za pomocą witryny Azure portal.

### <a name="configure-long-term-retention-policies"></a>Skonfiguruj zasady przechowywania długoterminowego

Można skonfigurować bazy danych SQL [przechowywania automatycznych kopii zapasowych](sql-database-long-term-retention.md) przez okres dłuższy niż okres przechowywania w warstwie usługi. 

1. W witrynie Azure portal, wybierz swój serwer SQL, a następnie kliknij przycisk **Zarządzanie kopiami zapasowymi**. Na **konfigurowania zasad** , a następnie wybierz bazę danych, na którym chcesz ustawić lub zmodyfikować długoterminowe zasady przechowywania kopii zapasowych.

   ![Zarządzanie łącze kopii zapasowych](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. W **konfigurowania zasad** okienko, określ, czy zachować co tydzień, miesięczny lub roczny kopii zapasowych i określ okres przechowywania dla każdego. 

   ![Konfigurowanie zasad](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Po zakończeniu kliknij przycisk **Zastosuj**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Wyświetlanie kopii zapasowych i przywracanie z kopii zapasowej za pomocą witryny Azure portal

Wyświetlanie kopii zapasowych, które są zachowywane dla konkretnej bazy danych za pomocą zasad pisowni LTR i przywracania z tych kopii zapasowych. 

1. W witrynie Azure portal, wybierz swój serwer SQL, a następnie kliknij przycisk **Zarządzanie kopiami zapasowymi**. Na **dostępnych kopii zapasowych** , a następnie wybierz bazę danych, dla którego chcesz wyświetlić dostępnych kopii zapasowych.

   ![Wybierz bazę danych](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. W **dostępnych kopii zapasowych** okienku przejrzyj dostępnych kopii zapasowych. 

   ![Wyświetlanie kopii zapasowych](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Wybieranie kopii zapasowej, z której chcesz przywrócić, a następnie określ nową nazwę bazy danych.

   ![Przywracanie](./media/sql-database-long-term-retention/ltr-restore.png)

5. Kliknij przycisk **OK** do przywrócenia bazy danych z kopii zapasowej w magazynie usługi Azure SQL z bazą danych.

6. Na pasku narzędzi kliknij ikonę powiadomienia, aby wyświetlić stan zadania przywracania.

   ![postęp zadania przywracania z magazynu](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Po zakończeniu zadania przywracania Otwórz **baz danych SQL** strony, aby wyświetlić nowo przywróconą bazę danych.

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak [wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Aby skonfigurować długoterminowe zasady przechowywania i przywracania kopii zapasowych przy użyciu programu PowerShell

Poniższe sekcje pokazują, jak skonfigurować długoterminowe przechowywanie kopii zapasowych, wyświetlić kopie zapasowe magazynu usługi Azure SQL i przywracania z kopii zapasowej w magazynie usługi Azure SQL przy użyciu programu PowerShell.

> [!IMPORTANT]
> Interfejsy API wersji 2 od lewej do prawej jest obsługiwane w następujących wersji programu PowerShell:
- [Elementu AzureRM.Sql 4.5.0](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0) lub nowszej
- [AzureRM 6.1.0](https://www.powershellgallery.com/packages/AzureRM/6.1.0) lub nowszej
> 

### <a name="create-an-ltr-policy"></a>Tworzenie zasad LTR

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

### <a name="view-ltr-policies"></a>Wyświetl zasady LTR
Ten przykład ilustruje sposób wyświetlenia listy zasady LTR na serwerze

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Czyszczenie zasad LTR
W tym przykładzie pokazano, jak Wyczyść zasady LTR na podstawie bazy danych

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Wyświetlanie kopii zapasowych z od lewej do prawej

Ten przykład ilustruje sposób wyświetlenia listy kopii zapasowych LTR na serwerze. 

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

### <a name="delete-ltr-backups"></a>Usuwanie kopii zapasowych LTR

W tym przykładzie pokazano, jak usunąć od lewej do prawej kopii zapasowej z listy kopii zapasowych.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Przywracanie z kopii zapasowych LTR
W tym przykładzie pokazano, jak przywrócić z kopii zapasowej od lewej do prawej. Należy pamiętać, ten interfejs nie został zmieniony, ale parametr identyfikatora zasobu teraz wymaga identyfikatora zasobu kopii zapasowych LTR. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> W tym miejscu możesz nawiązać przywróconej bazy danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, np. w przypadku wyodrębnienie bitu danych z przywróconej bazy danych do skopiowania do istniejącej bazy danych lub usuń istniejącą bazę danych i zmiana nazwy przywróconej bazy danych do istniejącej nazwy bazy danych. Zobacz [punktu w czasie przywracania](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych generowanych przez usługi, zobacz artykuł dotyczący [automatycznych kopii zapasowych](sql-database-automated-backups.md)
- Aby dowiedzieć się więcej o długoterminowym przechowywaniu kopii zapasowych, zobacz temat dotyczący [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md)
