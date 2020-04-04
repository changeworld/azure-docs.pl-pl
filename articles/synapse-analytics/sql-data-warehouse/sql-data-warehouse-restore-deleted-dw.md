---
title: Przywracanie usuniętej puli SQL
description: Jak prowadzić do przywracania usuniętej puli SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 251fdb83e848aaac3a5391320df23149ce1bce33
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633049"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Przywracanie usuniętej puli SQL przy użyciu usługi Azure Synapse Analytics

W tym artykule nauczysz się przywracać sql przy użyciu witryny Azure portal lub programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Sprawdź pojemność funkcji DTU.** Każda pula SQL jest obsługiwana przez serwer SQL (na przykład myserver.database.windows.net), który ma domyślny przydział DTU.  Sprawdź, czy serwer SQL ma wystarczającą ilość pozostałego przydziału DTU dla przywracania bazy danych. Aby dowiedzieć się, jak obliczyć potrzebne ceny DTU lub zażądać więcej DTU, zobacz [Żądanie zmiany przydziału DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Przywracanie usuniętego magazynu danych za pośrednictwem programu PowerShell

Aby przywrócić usuniętą pulę SQL, użyj polecenia cmdlet [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) Jeśli odpowiedni serwer logiczny również został usunięty, nie można przywrócić tego magazynu danych.

1. Przed rozpoczęciem należy zainstalować [program Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Otwórz program PowerShell.
3. Połącz się ze swoim kontem platformy Azure i wymień wszystkie subskrypcje skojarzone z Twoim kontem.
4. Wybierz subskrypcję zawierającą usunięty magazyn danych, który ma zostać przywrócony.
5. Pobierz określony magazyn usuniętych danych.
6. Przywracanie usuniętego magazynu danych
    1. Aby przywrócić usunięty magazyn danych SQL na inny serwer logiczny, należy określić inną nazwę serwera logicznego.  Ten serwer logiczny może również znajdować się w innej grupie zasobów i regionie.
    1. Aby przywrócić do innej subskrypcji, użyj przycisku [Przenieś,](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) aby przenieść serwer logiczny do innej subskrypcji.
7. Sprawdź, czy przywrócony magazyn danych jest w trybie online.
8. Po zakończeniu przywracania można skonfigurować magazyn odzyskanych danych, wykonując [konfigurację bazy danych po odzyskaniu](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Przywracanie usuniętej bazy danych za pomocą portalu Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Przejdź do serwera SQL, na który znajdował się usunięty magazyn danych.
3. Wybierz ikonę **Usunięte bazy danych** w spisie treści.

    ![Usunięte bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Wybierz usunięty magazyn danych SQL, który chcesz przywrócić.

    ![Wybieranie pozycji Usunięte bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Określ nową **nazwę bazy danych** i kliknij przycisk **OK**

    ![Określ nazwę bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącej puli SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Przywracanie z puli SQL kopii zapasowej geograficznej](sql-data-warehouse-restore-from-geo-backup.md)
