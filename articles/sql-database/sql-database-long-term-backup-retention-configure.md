---
title: 'Zarządzanie długoterminowym przechowywaniem kopii zapasowych Azure SQL Database '
description: Dowiedz się, jak przechowywać zautomatyzowane kopie zapasowe w usłudze SQL Azure Storage, a następnie przywracać je
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 08/21/2019
ms.openlocfilehash: 32eb021ecd584e0b1b734abb236f383a32b79131
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689553"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Zarządzanie długoterminowym przechowywaniem kopii zapasowych Azure SQL Database

W Azure SQL Database można skonfigurować jedną lub bazę danych w puli z [długoterminowymi zasadami przechowywania kopii zapasowych](sql-database-long-term-retention.md) (LTR), aby automatycznie zachować kopie zapasowe bazy danych w oddzielnych kontenerach usługi Azure Blob Storage przez maksymalnie 10 lat. Następnie można odzyskać bazę danych przy użyciu tych kopii zapasowych przy użyciu Azure Portal lub programu PowerShell.

> [!IMPORTANT]
> [Azure SQL Database wystąpienie zarządzane](sql-database-managed-instance.md) nie obsługuje obecnie długoterminowego przechowywania kopii zapasowych.

## <a name="use-the-azure-portal-to-manage-long-term-backups"></a>Używanie Azure Portal do zarządzania długoterminowymi kopiami zapasowymi

W poniższych sekcjach pokazano, jak za pomocą Azure Portal skonfigurować długoterminowe przechowywanie, wyświetlać kopie zapasowe w długoterminowym przechowywaniu oraz przywracać kopie zapasowe z długoterminowego przechowywania.

### <a name="configure-long-term-retention-policies"></a>Konfigurowanie zasad przechowywania długoterminowego

Można skonfigurować SQL Database, aby [zachować automatyczne kopie zapasowe](sql-database-long-term-retention.md) przez okres dłuższy niż okres przechowywania w warstwie usług. 

1. W Azure Portal wybierz serwer SQL, a następnie kliknij pozycję **Zarządzaj kopiami zapasowymi**. Na karcie **Konfigurowanie zasad** *zaznacz pole wyboru dla bazy danych, dla której chcesz ustawić lub zmodyfikować długoterminowe zasady przechowywania kopii zapasowych*. Jeśli pole wyboru obok bazy danych nie jest zaznaczone, zmiany zasad nie będą miały zastosowania do tej bazy danych.  

   ![Link zarządzania kopiami zapasowymi](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. W okienku **Konfigurowanie zasad** wybierz, czy chcesz zachować tygodniowe, miesięczne i roczne kopie zapasowe, a także określić okres przechowywania dla każdego z nich. 

   ![Konfigurowanie zasad](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Po zakończeniu kliknij przycisk **Zastosuj**.

> [!IMPORTANT]
> Włączenie długoterminowych zasad przechowywania kopii zapasowych może zająć do 7 dni, zanim pierwsza kopia zapasowa stanie się widoczna i będzie dostępna do przywrócenia. Aby uzyskać szczegółowe informacje dotyczące CADANCE kopii zapasowych LTR, zobacz [długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Wyświetlaj kopie zapasowe i przywracaj je przy użyciu Azure Portal

Wyświetl kopie zapasowe, które są zachowywane dla określonej bazy danych z zasadami LTR, i przywróć je z tych kopii zapasowych. 

1. W Azure Portal wybierz serwer SQL, a następnie kliknij pozycję **Zarządzaj kopiami zapasowymi**. Na karcie **dostępne kopie zapasowe** wybierz bazę danych, dla której chcesz wyświetlić dostępne kopie zapasowe.

   ![Wybierz bazę danych](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. Zapoznaj się z dostępnymi kopiami zapasowymi w okienku **dostępne kopie zapasowe** . 

   ![Wyświetl kopie zapasowe](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Wybierz kopię zapasową, z której chcesz przywrócić, a następnie określ nową nazwę bazy danych.

   ![Przywracanie](./media/sql-database-long-term-retention/ltr-restore.png)

5. Kliknij przycisk **OK** , aby przywrócić bazę danych z kopii zapasowej w usłudze Azure SQL Storage do nowej bazy danych.

6. Na pasku narzędzi kliknij ikonę powiadomienia, aby wyświetlić stan zadania przywracania.

   ![postęp zadania przywracania](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Po zakończeniu zadania przywracania Otwórz stronę **bazy danych SQL** , aby wyświetlić nowo przywróconą bazę danych.

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak [wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-manage-long-term-backups"></a>Zarządzanie długoterminowymi kopiami zapasowymi przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

W poniższych sekcjach pokazano, jak używać programu PowerShell do konfigurowania długoterminowego przechowywania kopii zapasowych, wyświetlania kopii zapasowych w usłudze Azure SQL Storage oraz przywracania danych z kopii zapasowej w usłudze Azure SQL Storage.


### <a name="rbac-roles-to-manage-long-term-retention"></a>Role RBAC do zarządzania długoterminowym przechowywaniem

W przypadku funkcji **Get-AzSqlDatabaseLongTermRetentionBackup** i **Restore-AzSqlDatabase**konieczne będzie posiadanie jednej z następujących ról:

- Rola właściciela subskrypcji lub
- SQL Server rolę współautor lub
- Rola niestandardowa z następującymi uprawnieniami:

   Microsoft. SQL/Locations/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/ longTermRetentionBackups/odczyt
 
W przypadku **usunięcia-AzSqlDatabaseLongTermRetentionBackup**należy mieć jedną z następujących ról:

- Rola właściciela subskrypcji lub
- Rola niestandardowa z następującymi uprawnieniami:

   Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete


> [!NOTE]
> Rola współautor SQL Server nie ma uprawnień do usuwania kopii zapasowych LTR.

Uprawnienia RBAC można udzielić w zakresie *subskrypcji* lub *grupy zasobów* . Aby jednak uzyskać dostęp do kopii zapasowych LTR należących do porzuconego serwera, należy przyznać uprawnienie w zakresie *subskrypcji* tego serwera.


### <a name="create-an-ltr-policy"></a>Tworzenie zasad odltr

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

# get the server
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Wyświetl zasady LTR
Ten przykład pokazuje, jak wyświetlić listę zasad LTR na serwerze

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Wyczyść zasady odltr
Ten przykład pokazuje, jak wyczyścić zasady LTR z bazy danych

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Wyświetl kopie zapasowe LTR

Ten przykład pokazuje, jak wyświetlić listę kopii zapasowych LTR na serwerze. 

```powershell
# List all LTR backups under the current subscription in a specific Azure region 
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# List the LTR backups under a specific resource group in a specific Azure region 
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup

# List the LTR backups under an existing server
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup -ServerName $serverName

# List the LTR backups for a specific database 
# The backups are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.  
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database under a server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Usuwanie kopii zapasowych LTR

Ten przykład pokazuje, jak usunąć kopię zapasową LTR z listy kopii zapasowych.

```powershell
# Remove the earliest backup from the list of backups
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> Usuwanie kopii zapasowej LTR jest nieodwracalne. Aby usunąć kopię zapasową LTR po usunięciu serwera, musisz mieć uprawnienia do zakresu subskrypcji. Można skonfigurować powiadomienia dotyczące każdego usunięcia w Azure Monitor przez filtrowanie operacji "usuwa długoterminową kopię zapasową przechowywania". Dziennik aktywności zawiera informacje o tym, kto i kiedy żądanie zostało zgłoszone. Szczegółowe instrukcje znajdują się w temacie [tworzenie alertów dziennika aktywności](../azure-monitor/platform/alerts-activity-log.md) .
>

### <a name="restore-from-ltr-backups"></a>Przywracanie z kopii zapasowych LTR
Ten przykład pokazuje, jak przywrócić z kopii zapasowej LTR. Zwróć uwagę, że ten interfejs nie został zmieniony, ale parametr identyfikatora zasobu wymaga teraz identyfikatora zasobu kopii zapasowej LTR. 

```powershell
# Restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup 
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Aby przywrócić z kopii zapasowej LTR po usunięciu serwera, należy mieć uprawnienia do zakresu subskrypcji serwera i subskrypcja musi być aktywna. Należy również pominąć opcjonalny parametr-ResourceGroupName.  
>

> [!NOTE]
> W tym miejscu można nawiązać połączenie z przywróconą bazą danych przy użyciu SQL Server Management Studio, aby wykonać potrzebne zadania, takie jak wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazy danych i zmiana nazwy przywróconej Baza danych na istniejącą nazwę bazy danych. Zobacz [przywracanie do punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych generowanych przez usługi, zobacz artykuł dotyczący [automatycznych kopii zapasowych](sql-database-automated-backups.md)
- Aby dowiedzieć się więcej o długoterminowym przechowywaniu kopii zapasowych, zobacz temat dotyczący [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md)
