---
title: Interfejsy API & REST programu PowerShell
description: Znajdź najlepsze polecenia cmdlet programu PowerShell dla puli SQL usługi Azure Synapse Analytics, w tym sposób wstrzymywania i wznawiania bazy danych.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f3d6d0c1f71e2262e943998cdc08717291903365
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743821"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>Interfejsy API & REST dla puli SQL usługi Azure Synapse Analytics

Wiele zadań administracyjnych puli SQL usługi Azure Synapse Analytics można zarządzać za pomocą poleceń cmdlet programu Azure PowerShell lub interfejsów API REST.  Poniżej znajduje się kilka przykładów używania poleceń programu PowerShell do automatyzacji typowych zadań w puli SQL.  Aby uzyskać kilka dobrych przykładów REST, zobacz artykuł [Zarządzanie skalowalnością za pomocą REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Wprowadzenie do poleceń cmdlet programu Azure PowerShell

1. Otwórz program Windows PowerShell.
2. W wierszu programu PowerShell uruchom te polecenia, aby zalogować się do usługi Azure Resource Manager i wybrać subskrypcję.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Przykład magazynu danych wstrzymania

Wstrzymaj bazę danych o nazwie "Database02" hostowana na serwerze o nazwie "Server01".  Serwer znajduje się w grupie zasobów platformy Azure o nazwie "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Odmiana, w tym przykładzie potoki pobranego obiektu do [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  W rezultacie baza danych jest wstrzymana. Końcowe polecenie pokazuje wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Uruchamianie przykładu magazynu danych

Wznawianie działania bazy danych o nazwie "Database02" hostowanej na serwerze o nazwie "Server01". Serwer znajduje się w grupie zasobów o nazwie "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Odmiana w tym przykładzie pobiera bazę danych o nazwie "Database02" z serwera o nazwie "Server01", który znajduje się w grupie zasobów o nazwie "ResourceGroup1". Jest on potokiem pobranego obiektu do [resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Należy zauważyć, że jeśli serwer jest foo.database.windows.net, użyj "foo" jako -ServerName w poleceniach cmdlet programu PowerShell.

## <a name="other-supported-powershell-cmdlets"></a>Inne obsługiwane polecenia cmdlet programu PowerShell

Te polecenia cmdlet programu PowerShell są obsługiwane w magazynie danych usługi Azure Synapse Analytics.

* [Baza danych Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabaseRestorePunkt](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Nowa baza danych AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Usuń-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Restore-AzSqlDatabase]/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Wznawianie-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Wstrzymaj-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów programu PowerShell, zobacz:

* [Tworzenie magazynu danych przy użyciu programu PowerShell](create-data-warehouse-powershell.md)
* [Przywracanie bazy danych](sql-data-warehouse-restore-points.md)

Aby uzyskać inne zadania, które można zautomatyzować za pomocą programu PowerShell, zobacz [polecenia cmdlets bazy danych SQL azure]/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/toc.json). Nie wszystkie polecenia cmdlet usługi Azure SQL Database są obsługiwane w magazynie danych usługi Azure Synapse Analytics. Aby uzyskać listę zadań, które można zautomatyzować za pomocą rest, zobacz [Operacje dla usługi Azure SQL Database](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
