---
title: Przywracanie magazynu danych z geograficznej kopii zapasowej
description: Przewodnik dotyczący przywracania geograficznego Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 69eb1221686da61868df8b06ed80664ae76d1627
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685500"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse przywracania geograficznego

W tym artykule dowiesz się, jak przywrócić magazyn danych z geograficznej kopii zapasowej za pośrednictwem Azure Portal i programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Sprawdź pojemność jednostek DTU.** Każdy SQL Data Warehouse jest obsługiwany przez program SQL Server (na przykład myserver.database.windows.net), który ma domyślny limit przydziału jednostek DTU. Sprawdź, czy program SQL Server ma wystarczający limit przydziału jednostek DTU dla przywracanej bazy danych. Aby dowiedzieć się, jak obliczyć liczbę jednostek DTU potrzebnych lub aby zażądać większej liczby jednostek DTU, zobacz [żądanie zmiany limitu przydziału jednostek DTU][Request a DTU quota change].

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Przywracanie z regionu geograficznego platformy Azure za pomocą programu PowerShell

Aby przywrócić z geograficznej kopii zapasowej, należy użyć polecenia cmdlet [Get-AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup] i [Restore-AzSqlDatabase][Restore-AzSqlDatabase] .

> [!NOTE]
> Można wykonać przywracanie geograficzne do Gen2! W tym celu należy określić Gen2 serviceobiektywname (np. wartości DW1000**c**) jako opcjonalny parametr.
>

1. Przed rozpoczęciem upewnij się, że [zainstalowano Azure PowerShell][Install Azure PowerShell].
2. Otwórz program PowerShell.
2. Połącz się z kontem platformy Azure i Wyświetl listę wszystkich subskrypcji skojarzonych z Twoim kontem.
3. Wybierz subskrypcję zawierającą magazyn danych, który ma zostać przywrócony.
4. Pobierz magazyn danych, który chcesz odzyskać.
5. Utwórz żądanie odzyskiwania dla hurtowni danych.
6. Sprawdź stan magazynu danych, który został przywrócony do lokalizacji geograficznej.
7. Aby skonfigurować magazyn danych po zakończeniu przywracania, zobacz [Konfigurowanie bazy danych po odzyskaniu][Configure your database after recovery].

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

Odzyskana baza danych będzie TDE, jeśli źródłowa baza danych jest włączona.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Przywracanie z regionu geograficznego platformy Azure za pośrednictwem Azure Portal

Wykonaj kroki opisane poniżej, aby przywrócić Azure SQL Data Warehouse z geograficznej kopii zapasowej:

1. Zaloguj się do konta [Azure Portal][Azure portal] .
1. Kliknij pozycję **+ Utwórz zasób** i Wyszukaj SQL Data Warehouse a następnie kliknij przycisk **Utwórz**.

    ![Nowy magazyn DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Wprowadź informacje wymagane na karcie **podstawowe** i kliknij przycisk **Dalej: Ustawienia dodatkowe**.

    ![Podstawy](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. W polu **Użyj istniejącego parametru danych** wybierz pozycję **kopia zapasowa** , a następnie wybierz odpowiednią kopię zapasową z opcji przewijania w dół. Kliknij przycisk **Przegląd + Utwórz**.
 
   ![kopia zapasowa](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. Po przywróceniu magazynu danych sprawdź, czy **stan** jest w trybie online.

## <a name="next-steps"></a>Następne kroki
- [Przywracanie istniejącego magazynu danych][Restore an existing data warehouse]
- [Przywracanie usuniętego magazynu danych][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
