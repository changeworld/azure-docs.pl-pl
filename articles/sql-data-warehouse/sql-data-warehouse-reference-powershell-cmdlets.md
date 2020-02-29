---
title: Interfejsy API REST programu PowerShell &
description: Znajdź najważniejsze polecenia cmdlet programu PowerShell dla puli SQL usługi Azure Synapse Analytics, w tym sposób wstrzymywania i wznawiania bazy danych.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0c8b1e9b7526bd45d037f053715613b53ec163f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198460"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>Interfejsy API REST programu PowerShell & dla puli SQL usługi Azure Synapse Analytics

Wiele zadań administracyjnych puli SQL usługi Azure Synapse Analytics można zarządzać przy użyciu poleceń cmdlet Azure PowerShell lub interfejsów API REST.  Poniżej przedstawiono kilka przykładów użycia poleceń programu PowerShell do automatyzowania typowych zadań w puli SQL.  Aby zapoznać się z niektórymi dobrymi przykładami REST, zobacz artykuł [Zarządzanie skalowalnością przy użyciu interfejsu REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Wprowadzenie do Azure PowerShell poleceń cmdlet

1. Otwórz program Windows PowerShell.
2. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby zalogować się do Azure Resource Manager i wybrać subskrypcję.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Przykład wstrzymywania magazynu danych
Wstrzymywanie bazy danych o nazwie "Database02" hostowanej na serwerze o nazwie "Serwer01".  Serwer należy do grupy zasobów platformy Azure o nazwie "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Odmiana, w tym przykładzie potok pobrano do [zawieszania-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  W związku z tym baza danych jest wstrzymana. Końcowe polecenie wyświetla wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Uruchom przykład magazynu danych

Wznów działanie bazy danych o nazwie "Database02" hostowanej na serwerze o nazwie "Serwer01". Serwer jest zawarty w grupie zasobów o nazwie "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Odmiana, w tym przykładzie pobiera bazę danych o nazwie "Database02" z serwera o nazwie "Serwer01", który znajduje się w grupie zasobów o nazwie "ResourceGroup1". Pobrano potok w celu [wznowienia-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Należy pamiętać, że jeśli serwer jest foo.database.windows.net, użyj "foo" jako-ServerName w poleceniach cmdlet programu PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Inne obsługiwane polecenia cmdlet programu PowerShell
Te polecenia cmdlet programu PowerShell są obsługiwane przez usługę Azure Synapse Analytics Data Warehouse.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [SELECT-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej przykładów dla programu PowerShell, zobacz:

* [Tworzenie magazynu danych przy użyciu programu PowerShell](create-data-warehouse-powershell.md)
* [Przywracanie bazy danych](sql-data-warehouse-restore-database-powershell.md)

Aby zapoznać się z innymi zadaniami, które można zautomatyzować za pomocą programu PowerShell, zobacz [Azure SQL Database polecenia cmdlet](https://docs.microsoft.com/powershell/module/az.sql). Nie wszystkie polecenia cmdlet Azure SQL Database są obsługiwane przez usługę Azure Synapse Analytics Data Warehouse.  Aby zapoznać się z listą zadań, które można zautomatyzować za pomocą usługi REST, zobacz [operacje dla Azure SQL Database](/rest/api/sql/).
