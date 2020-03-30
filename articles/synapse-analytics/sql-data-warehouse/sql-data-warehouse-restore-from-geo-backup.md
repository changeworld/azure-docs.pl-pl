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
ms.openlocfilehash: 4390ed39c86e041d3fbd776415f0ffbe71f605bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350165"
---
# <a name="geo-restore-for-sql-pool"></a>Przywracanie geograficzne dla puli SQL

W tym artykule nauczysz się przywracać pulę SQL z kopii zapasowej geograficznej za pośrednictwem witryny Azure portal i programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Sprawdź pojemność funkcji DTU.** Każda pula SQL jest obsługiwana przez serwer SQL (na przykład myserver.database.windows.net), który ma domyślny przydział DTU. Sprawdź, czy serwer SQL ma wystarczającą ilość pozostałego przydziału DTU dla przywracania bazy danych. Aby dowiedzieć się, jak obliczyć potrzebne ceny DTU lub zażądać więcej DTU, zobacz [Żądanie zmiany przydziału DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Przywracanie z regionu geograficznego platformy Azure za pośrednictwem programu PowerShell

Aby przywrócić z kopii zapasowej geograficznej, należy użyć polecenia cmdlet [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) i [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)

> [!NOTE]
> Można wykonać geo-przywracanie do Gen2! W tym celu należy określić nazwę usługi Gen2 ServiceObjectiveName**c**(np.
>

1. Przed rozpoczęciem należy zainstalować [program Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Otwórz program PowerShell.
2. Połącz się ze swoim kontem platformy Azure i wymień wszystkie subskrypcje skojarzone z Twoim kontem.
3. Wybierz subskrypcję zawierającą magazyn danych, który ma zostać przywrócony.
4. Pobierz magazyn danych, który chcesz odzyskać.
5. Utwórz żądanie odzyskiwania dla magazynu danych.
6. Sprawdź stan magazynu danych przywróconych geograficznie.
7. Aby skonfigurować magazyn danych po zakończeniu przywracania, zobacz [Konfigurowanie bazy danych po odzyskaniu]( ../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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
1. Kliknij **pozycję + Utwórz zasób**. 

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