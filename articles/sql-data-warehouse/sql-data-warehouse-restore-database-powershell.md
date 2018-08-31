---
title: Przywracanie usługi Azure SQL Data Warehouse (PowerShell) | Dokumentacja firmy Microsoft
description: Zadania programu PowerShell dla usługi Azure SQL Data Warehouse przywracania.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fde06a9c7c0f0b225840c3b5a49d00fbfbcf33d9
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306270"
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Przywracanie usługi Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [Przegląd][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

W tym artykule dowiesz się, jak przywrócić usługi Azure SQL Data Warehouse przy użyciu programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem
**Sprawdź wydajność jednostek DTU.** Każda usługa SQL Data Warehouse jest obsługiwana przez serwer SQL (np. myserver.database.windows.net), która ma domyślny limit przydziału jednostek DTU.  Przed przywróceniem usłudze SQL Data Warehouse, upewnij się, że program SQL server ma wystarczającą ilość pozostały limit przydziału jednostek DTU dla przywracana baza danych. Aby dowiedzieć się, jak obliczyć potrzebnych jednostek DTU lub zażądać więcej jednostek DTU, zobacz [żądanie zmiany limitu przydziału jednostek DTU][Request a DTU quota change].

### <a name="install-powershell"></a>Instalowanie programu PowerShell
Aby można było używać programu Azure PowerShell z usługą SQL Data Warehouse, należy zainstalować program Azure PowerShell w wersji 1.0 lub nowszej.  Wersję można sprawdzić, uruchamiając **Get-Module - ListAvailable-Name AzureRM**.  Najnowszą wersję można zainstalować z [Instalatora platformy sieci Web firmy Microsoft][Microsoft Web Platform Installer].  Aby uzyskać więcej informacji na temat instalowania najnowszej wersji, zobacz [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Jak zainstalować i skonfigurować program Azure PowerShell).

## <a name="restore-an-active-or-paused-database"></a>Przywracanie bazy danych programu active lub wstrzymana
Aby przywrócić bazę danych przed użyciem migawki [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] polecenia cmdlet programu PowerShell.

1. Otwórz program Windows PowerShell.
2. Nawiąż połączenie z kontem platformy Azure i wyświetlanie listy wszystkich subskrypcji skojarzonych z Twoim kontem.
3. Wybierz subskrypcję, która zawiera bazę danych do przywrócenia.
4. Lista punktów przywracania bazy danych.
5. Wybierz punkt przywracania żądanego przy użyciu RestorePointCreationDate.
6. Przywróć bazę danych do punktu przywracania żądaną.
7. Sprawdź, czy przywróconej bazy danych jest w trybie online.

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
> Po zakończeniu przywracania odzyskanej bazy danych można skonfigurować, wykonując [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Przywracanie usuniętej bazy danych
Aby przywrócić usuniętą bazę danych, należy użyć [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] polecenia cmdlet.

1. Otwórz program Windows PowerShell.
2. Nawiąż połączenie z kontem platformy Azure i wyświetlanie listy wszystkich subskrypcji skojarzonych z Twoim kontem.
3. Wybierz subskrypcję, która zawiera przywracania usuniętej bazy danych.
4. Uzyskaj określone usuniętej bazy danych.
5. Przywracanie usuniętej bazy danych.
6. Sprawdź, czy przywróconej bazy danych jest w trybie online.

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
> Po zakończeniu przywracania odzyskanej bazy danych można skonfigurować, wykonując [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Przywróć z regionu geograficznego platformy Azure
Aby odzyskać bazę danych, należy użyć [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] polecenia cmdlet.

> [!NOTE]
> Przywracanie geograficzne, aby zoptymalizowany pod kątem można wykonać dla warstwy wydajności obliczeniowej! Aby to zrobić, należy określić zoptymalizowany dla obliczeń ServiceObjectiveName jako parametr opcjonalny. 
>
> 

1. Otwórz program Windows PowerShell.
2. Nawiąż połączenie z kontem platformy Azure i wyświetlanie listy wszystkich subskrypcji skojarzonych z Twoim kontem.
3. Wybierz subskrypcję, która zawiera bazę danych do przywrócenia.
4. Pobierz bazę danych, którą chcesz odzyskać.
5. Utwórz żądanie odzyskiwania dla bazy danych.
6. Sprawdź stan bazy danych, przywrócić geograficznie.

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

Odzyskanej bazy danych będzie włączona funkcja TDE źródłowa baza danych jest włączona funkcja TDE.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się o funkcje zachowywania ciągłości biznesowej, baz danych Azure SQL Database, należy przeczytać [omówienie ciągłości działania usługi Azure SQL Database][Azure SQL Database business continuity overview].

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
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
