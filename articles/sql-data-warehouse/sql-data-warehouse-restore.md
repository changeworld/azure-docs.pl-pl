---
title: Przywracanie usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Jak przewodnik dotyczący Przywracanie usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ebbcbcc3d0934800980b7d8e00895b11ff2747b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60310419"
---
# <a name="restoring-azure-sql-data-warehouse"></a>Przywracanie usługi Azure SQL Data Warehouse 
W tym artykule dowiesz się, jak wykonać następujące czynności w witrynie Azure portal i programu PowerShell:

- Utwórz punkt przywracania
- Przywróć z punktu przywracania automatyczne lub punkt przywracania zdefiniowanych przez użytkownika
- Przywróć z usuniętej bazy danych
- Przywróć z geograficznej kopii zapasowej
- Utwórz kopię magazynu danych z punktu przywracania zdefiniowanych przez użytkownika

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Sprawdź wydajność jednostek DTU.** Każda usługa SQL Data Warehouse jest obsługiwana przez serwer SQL (np. myserver.database.windows.net), która ma domyślny limit przydziału jednostek DTU.  Zanim można przywrócić bazę danych SQL data warehouse, upewnij się, że program SQL server ma wystarczającą ilość pozostały limit przydziału jednostek DTU dla przywracana baza danych. Aby dowiedzieć się, jak obliczyć potrzebnych jednostek DTU lub zażądać więcej jednostek DTU, zobacz [żądanie zmiany limitu przydziału jednostek DTU][Request a DTU quota change].

## <a name="restore-through-powershell"></a>Przywracanie przy użyciu programu PowerShell

## <a name="install-powershell"></a>Instalowanie programu PowerShell
Aby można było używać programu Azure PowerShell z usługą SQL Data Warehouse, należy zainstalować program Azure PowerShell.  Wersję można sprawdzić, uruchamiając **Get-Module - ListAvailable-Name Az**. Aby uzyskać więcej informacji na temat instalowania najnowszej wersji, zobacz [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Jak zainstalować i skonfigurować program Azure PowerShell).

## <a name="restore-an-active-or-paused-database-using-powershell"></a>Przywracanie aktywnych lub wstrzymania bazy danych przy użyciu programu PowerShell
Aby przywrócić bazę danych przed użyciem punktu przywracania [AzSqlDatabase przywracania] [ Restore-AzSqlDatabase] polecenia cmdlet programu PowerShell.

1. Otwórz program Windows PowerShell.

2. Nawiąż połączenie z kontem platformy Azure i wyświetlanie listy wszystkich subskrypcji skojarzonych z Twoim kontem.

3. Wybierz subskrypcję, która zawiera bazę danych do przywrócenia.

4. Lista punktów przywracania bazy danych.

5. Wybierz punkt przywracania żądanego przy użyciu RestorePointCreationDate.

   > [!NOTE]
   > Podczas przywracania, można określić różne ServiceObjectiveName (DWU) lub innego serwera znajdującej się w innym regionie.

6. Przywróć bazę danych do punktu przywracania żądaną.

7. Sprawdź, czy przywróconej bazy danych jest w trybie online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Po zakończeniu przywracania odzyskanej bazy danych można skonfigurować, wykonując [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
>

## <a name="copy-your-data-warehouse-with-user-defined-restore-points-using-powershell"></a>Skopiuj magazyn danych z punktów przywracania zdefiniowanych przez użytkownika przy użyciu programu PowerShell
Aby przywrócić bazę danych przed użyciem punktu przywracania użytkownika [AzSqlDatabase przywracania] [ Restore-AzSqlDatabase] polecenia cmdlet programu PowerShell.

1. Otwórz program Windows PowerShell.
2. Nawiąż połączenie z kontem platformy Azure i wyświetlanie listy wszystkich subskrypcji skojarzonych z Twoim kontem.
3. Wybierz subskrypcję, która zawiera bazę danych do przywrócenia.
4. Utworzenie punktu przywracania dla Natychmiastowa kopia bazy danych
5. Zmień nazwę bazy danych na nazwę tymczasową.
6. Pobierz najnowszy punkt przywracania przez określony RestorePointLabel.
7. Pobierz identyfikator zasobu bazy danych można zainicjować operacji przywracania
8. Przywróć bazę danych do punktu przywracania żądaną.
9. Sprawdź, czy przywróconej bazy danych jest w trybie online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

## <a name="restore-a-deleted-database-using-powershell"></a>Przywracanie usuniętej bazy danych przy użyciu programu PowerShell
Aby przywrócić usuniętą bazę danych, należy użyć [AzSqlDatabase przywracania] [ Restore-AzSqlDatabase] polecenia cmdlet.

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

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Po zakończeniu przywracania odzyskanej bazy danych można skonfigurować, wykonując [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
>

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Przywróć z regionu geograficznego platformy Azure przy użyciu programu PowerShell
Aby odzyskać bazę danych, należy użyć [AzSqlDatabase przywracania] [ Restore-AzSqlDatabase] polecenia cmdlet.

> [!NOTE]
> Można przeprowadzić przywracania geograficznego, aby Gen2! Aby to zrobić, należy określić ServiceObjectiveName Gen2 (np. DW1000**c**) jako parametr opcjonalny.
>

1. Otwórz program Windows PowerShell.
2. Nawiąż połączenie z kontem platformy Azure i wyświetlanie listy wszystkich subskrypcji skojarzonych z Twoim kontem.
3. Wybierz subskrypcję, która zawiera bazę danych do przywrócenia.
4. Pobierz bazę danych, którą chcesz odzyskać.
5. Utwórz żądanie odzyskiwania dla bazy danych.
6. Sprawdź stan bazy danych, przywrócić geograficznie.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Aby skonfigurować bazę danych, po ukończeniu przywracania, zobacz [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
>

Odzyskanej bazy danych będzie włączona funkcja TDE źródłowa baza danych jest włączona funkcja TDE.

## <a name="restore-through-the-azure-portal"></a>Przywracanie w witrynie Azure portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Utwórz punkt przywracania zdefiniowanych przez użytkownika przy użyciu witryny Azure portal
1. Zaloguj się w witrynie [Azure Portal][Azure portal].

2. Przejdź do magazynu danych SQL, który chcesz utworzyć punkt przywracania dla.

3. W górnej części bloku przeglądu, wybierz **+ nowy punkt przywracania**.

    ![Nowy punkt przywracania](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Określ nazwę dla tego punktu przywracania.

    ![Nazwa punktu przywracania](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Przywracanie aktywnych lub wstrzymania bazy danych przy użyciu witryny Azure portal
1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. Przejdź do magazynu danych SQL, który chcesz przywrócić z.
3. W górnej części bloku przeglądu, wybierz **przywrócić**.

    ![ Omówienie przywracania kopii zapasowych](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Wybierz opcję **punkty przywracania na automatyczne** lub **zdefiniowanych przez użytkownika punktów przywracania**.

    ![Punkty przywracania na automatyczny](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Dla punktów przywracania User-defined **wybierz punkt przywracania** lub **utworzyć nowy punkt przywracania użytkownika**.

    ![Punkty przywracania zdefiniowanych przez użytkownika](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Przywracanie usuniętej bazy danych przy użyciu witryny Azure portal
1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. Przejdź do usuniętej bazy danych była hostowana na serwerze SQL.
3. Wybierz ikonę bazy danych usunięto w spisie treści.

    ![Usunięte bazy danych](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)

4. Wybierz usuniętej bazy danych, który chcesz przywrócić.

    ![Wybierz usuniętych baz danych](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)

5. Określ nazwę nowej bazy danych.

    ![Określ nazwę bazy danych](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

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
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
