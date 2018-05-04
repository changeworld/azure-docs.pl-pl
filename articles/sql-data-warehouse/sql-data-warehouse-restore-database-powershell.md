---
title: Przywróć magazyn danych Azure SQL (PowerShell) | Dokumentacja firmy Microsoft
description: Zadania programu PowerShell dla usługi Azure SQL Data Warehouse przywracania.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 332b80d5c6dbe0b46a6fb793d3c0c04574744b19
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Przywróć magazyn danych Azure SQL (PowerShell)
> [!div class="op_single_selector"]
> * [Omówienie][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

W tym artykule dowiesz się, jak przywrócić Azure SQL Data Warehouse przy użyciu programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem
**Sprawdź, czy pojemność jednostek dtu w warstwie.** Każdy magazyn danych SQL jest obsługiwana przez serwer SQL (np. myserver.database.windows.net), który ma domyślnego przydziału jednostek dtu w warstwie.  Zanim będzie można przywrócić magazyn danych SQL, upewnij się, że program SQL server ma wystarczającą ilość pozostałych limit przydziału jednostek DTU dla przywracanej bazy danych. Aby dowiedzieć się, jak można obliczyć jednostek dtu w warstwie potrzebne również z prośbami więcej jednostek dtu w warstwie, zobacz [żądanie zmiany limitu przydziału jednostek dtu w warstwie][Request a DTU quota change].

### <a name="install-powershell"></a>Instalowanie programu PowerShell
Aby można było używać programu Azure PowerShell z usługą Magazyn danych SQL, należy zainstalować program Azure PowerShell w wersji 1.0 lub nowszej.  Wersję można sprawdzić, uruchamiając **Get-Module - ListAvailable-Name AzureRM**.  Najnowszą wersję można zainstalować z [Instalatora platformy sieci Web firmy Microsoft][Microsoft Web Platform Installer].  Aby uzyskać więcej informacji na temat instalowania najnowszej wersji, zobacz [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Jak zainstalować i skonfigurować program Azure PowerShell).

## <a name="restore-an-active-or-paused-database"></a>Przywróć bazę danych aktywnej lub wstrzymana
Przywracanie bazy danych przed użyciem migawki [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] polecenia cmdlet programu PowerShell.

1. Otwórz program Windows PowerShell.
2. Lista wszystkich subskrypcji skojarzonych z Twoim kontem i połącz się z kontem platformy Azure.
3. Wybierz subskrypcję, który zawiera bazę danych do przywrócenia.
4. Lista punktów przywracania dla bazy danych.
5. Wybierz punkt przywracania żądaną przy użyciu RestorePointCreationDate.
6. Przywróć bazę danych do punktu przywracania żądany.
7. Sprawdź, czy przywrócona baza danych jest w trybie online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Po ukończeniu przywracania można skonfigurować odzyskanej bazy danych, wykonując [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Przywracanie usuniętej bazy danych
Aby przywrócić usunięte bazy danych, należy użyć [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] polecenia cmdlet.

1. Otwórz program Windows PowerShell.
2. Lista wszystkich subskrypcji skojarzonych z Twoim kontem i połącz się z kontem platformy Azure.
3. Wybierz subskrypcję, która zawiera usuniętej bazy danych do przywrócenia.
4. Pobierz określone usuniętej bazy danych.
5. Przywracanie usuniętej bazy danych.
6. Sprawdź, czy przywrócona baza danych jest w trybie online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Po ukończeniu przywracania można skonfigurować odzyskanej bazy danych, wykonując [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Przywróć z regionu geograficznego platformy Azure
Aby odzyskać bazę danych, należy użyć [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] polecenia cmdlet.

> [!NOTE]
> Można wykonać przywracania geograficznie do zoptymalizowana dla warstwę wydajności obliczeniowej! Aby to zrobić, należy określić zoptymalizowana dla obliczeniowe ServiceObjectiveName jako parametr opcjonalny. 
>
> 

1. Otwórz program Windows PowerShell.
2. Lista wszystkich subskrypcji skojarzonych z Twoim kontem i połącz się z kontem platformy Azure.
3. Wybierz subskrypcję, który zawiera bazę danych do przywrócenia.
4. Pobierz bazę danych, którą chcesz odzyskać.
5. Utwórz żądanie odzyskiwania bazy danych.
6. Sprawdź stan bazy danych przywrócone z magazynu geograficznie.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Aby skonfigurować bazę danych, po ukończeniu przywracania, zobacz [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
> 
> 

Odzyskanej bazy danych będzie mieć włączono funkcji TDE, jeśli baza danych jest włączona w funkcji TDE.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o funkcjach ciągłości biznesowej wersji bazy danych SQL Azure, przeczytaj [omówienie ciągłości działalności biznesowej usługi Azure SQL Database][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
