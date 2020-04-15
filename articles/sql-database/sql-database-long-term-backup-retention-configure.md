---
title: 'Pojedyncza baza danych: zarządzanie długoterminową przechowywaniem kopii zapasowych'
description: Dowiedz się, jak przechowywać i przywracać automatyczne kopie zapasowe dla pojedynczej lub puli bazy danych usługi Azure SQL Database w magazynie platformy Azure (przez okres do 10 lat) przy użyciu usługi Azure Portal i programu PowerShell
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
ms.date: 04/14/2020
ms.openlocfilehash: 2564fd0ffd980dae4ca1835f4211fee0a0cf040c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380932"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Zarządzanie długoterminową przechowywaniem kopii zapasowych usługi Azure SQL Database

W usłudze Azure SQL Database można skonfigurować pojedynczą lub pulową bazę danych za pomocą długoterminowych zasad [przechowywania kopii zapasowych](sql-database-long-term-retention.md) (LTR), aby automatycznie przechowywać kopie zapasowe bazy danych w oddzielnych kontenerach magazynu obiektów Blob platformy Azure przez okres do 10 lat. Następnie można odzyskać bazę danych przy użyciu tych kopii zapasowych przy użyciu witryny Azure portal lub programu PowerShell.

> [!IMPORTANT]
> [Wystąpienie zarządzanej bazy danych SQL platformy Azure](sql-database-managed-instance.md) nie obsługuje obecnie długoterminowego przechowywania kopii zapasowych.

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

W poniższych sekcjach pokazano, jak korzystać z witryny Azure portal, aby skonfigurować długoterminowe przechowywanie, wyświetlić kopie zapasowe w długotrwałym przechowywania i przywrócić kopię zapasową z długoterminowego przechowywania.

### <a name="configure-long-term-retention-policies"></a>Konfigurowanie długoterminowych zasad przechowywania

Można skonfigurować bazę danych SQL, aby [zachować automatyczne kopie zapasowe](sql-database-long-term-retention.md) przez okres dłuższy niż okres przechowywania dla warstwy usług.

1. W witrynie Azure portal wybierz serwer SQL, a następnie kliknij pozycję **Zarządzaj kopiami zapasowymi**. Na karcie **Konfigurowanie zasad** zaznacz pole wyboru bazy danych, w której chcesz ustawić lub zmodyfikować długoterminowe zasady przechowywania kopii zapasowych. Jeśli pole wyboru obok bazy danych nie jest zaznaczone, zmiany zasad nie będą miały zastosowania do tej bazy danych.  

   ![zarządzanie łączem kopii zapasowych](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. W okienku **Konfigurowanie zasad** wybierz, jeśli chcesz zachować kopie zapasowe tygodniowe, miesięczne lub roczne, i określ okres przechowywania dla każdego z nich.

   ![konfigurowanie zasad](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Po zakończeniu kliknij przycisk **Zastosuj**.

> [!IMPORTANT]
> Po włączeniu długoterminowych zasad przechowywania kopii zapasowych może upłynąć do 7 dni, aby pierwsza kopia zapasowa stała się widoczna i dostępna do przywrócenia. Aby uzyskać szczegółowe informacje na temat kopii zapasowej LTR, zobacz [długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Wyświetlanie kopii zapasowych i przywracanie z kopii zapasowej

Służy do wyświetlania kopii zapasowych zachowanych dla określonej bazy danych z zasadami LTR i przywracania z tych kopii zapasowych.

1. W witrynie Azure portal wybierz serwer SQL, a następnie kliknij pozycję **Zarządzaj kopiami zapasowymi**. Na karcie **Dostępne kopie zapasowe** wybierz bazę danych, dla której chcesz wyświetlić dostępne kopie zapasowe.

   ![wybierz bazę danych](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

1. W okienku **Dostępne kopie zapasowe** przejrzyj dostępne kopie zapasowe.

   ![wyświetlanie kopii zapasowych](./media/sql-database-long-term-retention/ltr-available-backups.png)

1. Wybierz kopię zapasową, z której chcesz przywrócić, a następnie określ nową nazwę bazy danych.

   ![Przywracanie](./media/sql-database-long-term-retention/ltr-restore.png)

1. Kliknij **przycisk OK,** aby przywrócić bazę danych z kopii zapasowej w magazynie SQL platformy Azure do nowej bazy danych.

1. Na pasku narzędzi kliknij ikonę powiadomienia, aby wyświetlić stan zadania przywracania.

   ![postęp zadania przywracania](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

1. Po zakończeniu zadania przywracania otwórz stronę **baz danych SQL,** aby wyświetlić nowo przywróconą bazę danych.

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak [wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

W poniższych sekcjach pokazano, jak używać programu PowerShell do konfigurowania długoterminowego przechowywania kopii zapasowych, wyświetlania kopii zapasowych w magazynie SQL platformy Azure i przywracania z kopii zapasowej w magazynie SQL platformy Azure.

### <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC role do zarządzania długoterminową retencję

W przypadku **programów Get-AzSqlDatabaseLongTermRetentionBackup** i **Restore-AzSqlDatabase**konieczne będzie uzyskanie jednej z następujących ról:

- Rola właściciela subskrypcji lub
- Rola współautora programu SQL Server lub
- Rola niestandardowa z następującymi uprawnieniami:

   Microsoft.Sql/locations/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

Dla **Remove-AzSqlDatabaseLongTermRetentionBackup**, trzeba będzie mieć jedną z następujących ról:

- Rola właściciela subskrypcji lub
- Rola niestandardowa z następującym uprawnieniem:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> Rola współautora programu SQL Server nie ma uprawnień do usuwania kopii zapasowych LTR.

Uprawnienia RBAC mogą być przyznawane w zakresie *subskrypcji* lub *grupy zasobów.* Jednak aby uzyskać dostęp do kopii zapasowych LTR, które należą do porzuconego serwera, uprawnienie musi być udzielone w zakresie *subskrypcji* tego serwera.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>Tworzenie zasad LTR

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Wyświetlanie zasad LTR

W tym przykładzie pokazano, jak wyświetlić listę zasad LTR na serwerze

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | `
    Get-AzSqlDatabaseLongTermRetentionPolicy -Current

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -Current
```

### <a name="clear-an-ltr-policy"></a>Czyszczenie zasad LTR

W tym przykładzie pokazano, jak wyczyścić zasady LTR z bazy danych

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Wyświetlanie kopii zapasowych LTR

W tym przykładzie pokazano, jak wyświetlić listę kopii zapasowych LTR na serwerze.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Usuwanie kopii zapasowych LTR

W tym przykładzie pokazano, jak usunąć kopię zapasową LTR z listy kopii zapasowych.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Usunięcie kopii zapasowej LTR nie jest odwracalne. Aby usunąć kopię zapasową LTR po usunięciu serwera, musisz mieć uprawnienie Zakres subskrypcji. Powiadomienia dotyczące każdego usuwania można skonfigurować w usłudze Azure Monitor, filtrując pod kątem operacji "Usuwa długoterminową kopię zapasową przechowywania". Dziennik aktywności zawiera informacje o tym, kto i kiedy złożył żądanie. Zobacz [Tworzenie alertów dziennika aktywności, aby](../azure-monitor/platform/alerts-activity-log.md) uzyskać szczegółowe instrukcje.

### <a name="restore-from-ltr-backups"></a>Przywracanie z kopii zapasowych LTR

W tym przykładzie pokazano, jak przywrócić z kopii zapasowej LTR. Uwaga: ten interfejs nie zmienił się, ale parametr identyfikatora zasobu wymaga teraz identyfikatora zasobu kopii zapasowej LTR.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Aby przywrócić z kopii zapasowej LTR po usunięciu serwera, musisz mieć uprawnienia o zakresie subskrypcji serwera i że subskrypcja musi być aktywna. Należy również pominąć opcjonalny parametr -ResourceGroupName.

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych. Zobacz [przywracanie punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych generowanych przez usługi, zobacz artykuł dotyczący [automatycznych kopii zapasowych](sql-database-automated-backups.md)
- Aby dowiedzieć się więcej o długoterminowym przechowywaniu kopii zapasowych, zobacz temat dotyczący [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md)
