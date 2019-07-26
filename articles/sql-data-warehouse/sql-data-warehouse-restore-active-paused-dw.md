---
title: Przywróć istniejący Azure SQL Data Warehouse | Microsoft Docs
description: Przewodnik dotyczący przywracania istniejących Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 8593378dd35a6aaa31aaefca41dd921c6e65d336
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426748"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Przywróć istniejący Azure SQL Data Warehouse

W tym artykule dowiesz się, jak przywrócić istniejące SQL Data Warehouse przy użyciu Azure Portal i programu PowerShell:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Sprawdź pojemność jednostek DTU.** Każdy SQL Data Warehouse jest obsługiwany przez program SQL Server (na przykład myserver.database.windows.net), który ma domyślny limit przydziału jednostek DTU. Sprawdź, czy program SQL Server ma wystarczającą liczbę pozostałych limitów przydziału jednostek DTU dla przywracanej bazy danych. Aby dowiedzieć się, jak obliczyć liczbę jednostek DTU potrzebnych lub aby zażądać większej liczby jednostek DTU, zobacz [żądanie zmiany limitu przydziału jednostek DTU][Request a DTU quota change].

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Upewnij się, że zainstalowano [Azure PowerShell][Install Azure PowerShell].
2. Mieć istniejący punkt przywracania, z którego chcesz przeprowadzić przywracanie. Jeśli chcesz utworzyć nowe przywracanie, zapoznaj się z [samouczkiem, aby utworzyć nowy punkt przywracania zdefiniowany przez użytkownika][the tutorial to create a new user-defined restore point].

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Przywracanie istniejącego magazynu danych za poorednictwem programu PowerShell

Aby przywrócić istniejący magazyn danych z punktu przywracania, użyj polecenia cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase] programu PowerShell.

1. Otwórz program PowerShell.

2. Połącz się z kontem platformy Azure i Wyświetl listę wszystkich subskrypcji skojarzonych z Twoim kontem.

3. Wybierz subskrypcję zawierającą bazę danych, która ma zostać przywrócona.

4. Wyświetl listę punktów przywracania dla hurtowni danych.

5. Wybierz żądany punkt przywracania za pomocą RestorePointCreationDate.

6. Przywróć magazyn danych do żądanego punktu przywracania za pomocą polecenia cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase] programu PowerShell.
        1. Aby przywrócić SQL Data Warehouse do innego serwera logicznego, należy określić inną nazwę serwera logicznego.  Ten serwer logiczny może również znajdować się w innej grupie zasobów i regionie.
        2. Aby przywrócić do innej subskrypcji, użyj przycisku "Przenieś", aby przenieść serwer logiczny do innej subskrypcji.

7. Sprawdź, czy przywrócony magazyn danych jest w trybie online.

8. Po zakończeniu przywracania można skonfigurować odzyskany magazyn danych, wykonując następujące czynności: [Skonfiguruj bazę danych po odzyskaniu][Configure your database after recovery].

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

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Przywróć istniejący magazyn danych za pomocą Azure Portal

1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. Przejdź do usługi SQL Data Warehouse, z której chcesz wykonać przywracanie.
3. W górnej części bloku przegląd wybierz pozycję **Przywróć**.

    ![ Omówienie przywracania kopii zapasowych](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Wybierz **punkty przywracania automatycznego** lub **punkty przywracania zdefiniowane przez użytkownika**. Jeśli magazyn danych nie ma żadnych automatycznych punktów przywracania, poczekaj kilka godzin lub przed przywróceniem Utwórz punkt przywracania zdefiniowany przez użytkownika. Dla punktów przywracania zdefiniowanych przez użytkownika wybierz istniejącą lub Utwórz nową. W przypadku **serwera**można wybrać serwer logiczny w innej grupie zasobów i regionie lub utworzyć nowy. Po podania wszystkich parametrów kliknij przycisk **Przegląd + Przywróć**.

    ![Automatyczne punkty przywracania](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Następne kroki
- [Przywracanie usuniętego magazynu danych][Restore a deleted data warehouse]
- [Przywracanie z magazynu danych z geograficzną kopią zapasową][Restore from a geo-backup data warehouse]
 
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
[the tutorial to create a new user-defined restore point]:../sql-data-warehouse/sql-data-warehouse-restore-points.md
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
