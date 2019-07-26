---
title: Przywracanie usuniętego Azure SQL Data Warehouse | Microsoft Docs
description: Przewodnik dotyczący przywracania usuniętego Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 376a50a79858aee34aa71d172ca5836646a6651d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426631"
---
# <a name="restore-a-deleted-azure-sql-data-warehouse"></a>Przywracanie usuniętego Azure SQL Data Warehouse

W tym artykule dowiesz się, jak przywrócić usunięte SQL Data Warehouse przy użyciu Azure Portal i programu PowerShell:

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Sprawdź pojemność jednostek DTU.** Każdy SQL Data Warehouse jest obsługiwany przez program SQL Server (na przykład myserver.database.windows.net), który ma domyślny limit przydziału jednostek DTU.  Sprawdź, czy program SQL Server ma wystarczający limit przydziału jednostek DTU dla przywracanej bazy danych. Aby dowiedzieć się, jak obliczyć liczbę jednostek DTU potrzebnych lub aby zażądać większej liczby jednostek DTU, zobacz [żądanie zmiany limitu przydziału jednostek DTU][Request a DTU quota change].

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Przywracanie usuniętego magazynu danych za pomocą programu PowerShell

Aby przywrócić usunięty SQL Data Warehouse, należy użyć polecenia cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase] . Jeśli odpowiedni serwer logiczny został również usunięty, nie można przywrócić tego magazynu danych.

1. Przed rozpoczęciem upewnij się, że [zainstalowano Azure PowerShell][Install Azure PowerShell].
2. Otwórz program PowerShell.
3. Połącz się z kontem platformy Azure i Wyświetl listę wszystkich subskrypcji skojarzonych z Twoim kontem.
4. Wybierz subskrypcję zawierającą usunięty magazyn danych, który ma zostać przywrócony.
5. Pobierz konkretny usunięty magazyn danych.
6. Przywracanie usuniętego magazynu danych
    1. Aby przywrócić usunięte SQL Data Warehouse na inny serwer logiczny, należy określić inną nazwę serwera logicznego.  Ten serwer logiczny może również znajdować się w innej grupie zasobów i regionie.
    1. Aby przywrócić do innej subskrypcji, użyj przycisku [Przenieś][Move] , aby przenieść serwer logiczny do innej subskrypcji.
1. Sprawdź, czy przywrócony magazyn danych jest w trybie online.
1. Po zakończeniu przywracania można skonfigurować odzyskany magazyn danych, wykonując następujące czynności: [Skonfiguruj bazę danych po odzyskaniu][Configure your database after recovery].

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>" 
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Przywracanie usuniętej bazy danych przy użyciu Azure Portal

1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. Przejdź do programu SQL Server, w którym jest hostowany usunięty magazyn danych.
3. Wybierz ikonę **usunięte bazy danych** w spisie treści.

    ![Usunięte bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Wybierz usunięte SQL Data Warehouse, które chcesz przywrócić.

    ![Wybieranie usuniętych baz danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Określ nową **nazwę bazy danych** i kliknij przycisk **OK** .

    ![Określ nazwę bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Następne kroki
- [Przywracanie istniejącego magazynu danych][Restore an existing data warehouse]
- [Przywracanie z magazynu danych z geograficzną kopią zapasową][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[support ticket]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket
[Move]:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
