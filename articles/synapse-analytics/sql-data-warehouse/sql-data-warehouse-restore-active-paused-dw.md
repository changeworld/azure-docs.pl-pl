---
title: Przywracanie istniejącego magazynu danych
description: Instrukcje dotyczące przywracania istniejącej puli SQL.
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
ms.openlocfilehash: 6fa8bd42eb067124ab6ea1db77e2f3d6fba79638
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745216"
---
# <a name="restore-an-existing-sql-pool"></a>Przywracanie istniejącej puli SQL

W tym artykule dowiesz się, jak przywrócić istniejącą pulę SQL w usłudze Azure Synapse Analytics przy użyciu witryny Azure portal i programu PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Sprawdź pojemność funkcji DTU.** Każda pula jest obsługiwana przez serwer SQL (na przykład myserver.database.windows.net), który ma domyślny przydział DTU. Sprawdź, czy serwer SQL ma wystarczającą ilość pozostałego przydziału DTU dla przywracania bazy danych. Aby dowiedzieć się, jak obliczyć potrzebne ceny DTU lub zażądać więcej DTU, zobacz [Żądanie zmiany przydziału DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Upewnij się, że [zainstalowano program Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Mieć istniejący punkt przywracania, który chcesz przywrócić. Jeśli chcesz utworzyć nowe przywracanie, zobacz [samouczek, aby utworzyć nowy punkt przywracania zdefiniowany przez użytkownika](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Przywracanie istniejącej puli SQL za pośrednictwem programu PowerShell

Aby przywrócić istniejącą pulę SQL z punktu przywracania, użyj polecenia cmdlet programu PowerShell [restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

1. Otwórz program PowerShell.

2. Połącz się ze swoim kontem platformy Azure i wymień wszystkie subskrypcje skojarzone z Twoim kontem.

3. Wybierz subskrypcję zawierającą bazę danych, która ma zostać przywrócona.

4. Wyświetl listę punktów przywracania puli SQL.

5. Wybierz żądany punkt przywracania za pomocą przyrządu RestorePointCreationDate.

6. Przywróć pulę SQL do żądanego punktu przywracania przy użyciu polecenia cmdlet programu PowerShell [restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
        1. Aby przywrócić pulę SQL na inny serwer logiczny, należy określić inną nazwę serwera logicznego.  Ten serwer logiczny może również znajdować się w innej grupie zasobów i regionie.
        2. Aby przywrócić do innej subskrypcji, użyj przycisku "Przenieś", aby przenieść serwer logiczny do innej subskrypcji.

7. Sprawdź, czy przywrócona pula SQL jest w trybie online.

8. Po zakończeniu przywracania można skonfigurować odzyskaną pulę SQL, wykonując [konfigurowanie bazy danych po odzyskaniu](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Przywracanie istniejącej puli SQL za pośrednictwem witryny Azure portal

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Przejdź do puli SQL, z której chcesz przywrócić.
3. U góry bloku Przegląd wybierz pozycję **Przywróć**.

    ![ Omówienie przywracania kopii zapasowych](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Wybierz punkty **automatycznego przywracania** lub **zdefiniowane przez użytkownika punkty przywracania**. Jeśli pula SQL nie ma żadnych automatycznych punktów przywracania, poczekaj kilka godzin lub utwórz zdefiniowany przez użytkownika punkt przywracania przed przywróceniem. W przypadku punktów przywracania zdefiniowanych przez użytkownika wybierz istniejący lub utwórz nowy. W przypadku **programu Server**można wybrać serwer logiczny w innej grupie zasobów i regionie lub utworzyć nowy. Po podaniu wszystkich parametrów kliknij przycisk **Przejrzyj + Przywróć**.

    ![Automatyczne punkty przywracania](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie usuniętej puli SQL](sql-data-warehouse-restore-deleted-dw.md)
- [Przywracanie z puli SQL kopii zapasowej geograficznej](sql-data-warehouse-restore-from-geo-backup.md)
