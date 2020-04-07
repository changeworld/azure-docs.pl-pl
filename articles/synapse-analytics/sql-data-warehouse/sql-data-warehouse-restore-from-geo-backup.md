---
title: Przywracanie magazynu danych z kopii zapasowej geograficznej
description: Przewodnik dotyczący przywracania geograficznego puli SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e0980a9142dc966916d5a4df898ea53b0ddeae5
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745082"
---
# <a name="geo-restore-for-sql-pool"></a>Przywracanie geograficzne dla puli SQL

W tym artykule nauczysz się przywracać pulę SQL z kopii zapasowej geograficznej za pośrednictwem witryny Azure portal i programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Sprawdź pojemność funkcji DTU.** Każda pula SQL jest obsługiwana przez serwer SQL (na przykład myserver.database.windows.net), który ma domyślny przydział DTU. Sprawdź, czy serwer SQL ma wystarczającą ilość pozostałego przydziału DTU dla przywracania bazy danych. Aby dowiedzieć się, jak obliczyć potrzebne ceny DTU lub zażądać więcej DTU, zobacz [Żądanie zmiany przydziału DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Przywracanie z regionu geograficznego platformy Azure za pośrednictwem programu PowerShell

Aby przywrócić z kopii zapasowej geograficznej, należy użyć polecenia cmdlet [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

> [!NOTE]
> Można wykonać geo-przywracanie do Gen2! W tym celu należy określić nazwę usługi Gen2 ServiceObjectiveName**c**(np.
>

1. Przed rozpoczęciem należy zainstalować [program Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otwórz program PowerShell.
3. Połącz się ze swoim kontem platformy Azure i wymień wszystkie subskrypcje skojarzone z Twoim kontem.
4. Wybierz subskrypcję zawierającą magazyn danych, który ma zostać przywrócony.
5. Pobierz magazyn danych, który chcesz odzyskać.
6. Utwórz żądanie odzyskiwania dla magazynu danych.
7. Sprawdź stan magazynu danych przywróconych geograficznie.
8. Aby skonfigurować magazyn danych po zakończeniu przywracania, zobacz [Konfigurowanie bazy danych po odzyskaniu]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

Odzyskana baza danych będzie włączona TDE, jeśli źródłowa baza danych jest włączona TDE.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Przywracanie z regionu geograficznego platformy Azure za pośrednictwem witryny Azure portal

Wykonaj kroki opisane poniżej, aby przywrócić pulę SQL z kopii zapasowej geograficznej:

1. Zaloguj się do swojego konta [w witrynie Azure portal.](https://portal.azure.com/)
2. Kliknij **pozycję + Utwórz zasób**.

   ![Nowy DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Kliknij **pozycję Bazy danych,** a następnie **Usługa Azure Synapse Analytics (dawniej SQL DW) **.

   ![Nowy DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Wypełnij wymagane informacje na karcie **Podstawy** i kliknij przycisk **Dalej: Dodatkowe ustawienia**.

   ![Podstawy](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. W obszarze **Użyj istniejącego** parametru danych wybierz **pozycję Kopia zapasowa** i wybierz odpowiednią kopię zapasową z opcji przewijania w dół. Kliknij **pozycję Recenzja + Utwórz**.

   ![kopia zapasowa](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Po przywróceniu magazynu danych sprawdź, czy **stan** jest w trybie online.

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącej puli SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Przywracanie usuniętej puli SQL](sql-data-warehouse-restore-deleted-dw.md)
