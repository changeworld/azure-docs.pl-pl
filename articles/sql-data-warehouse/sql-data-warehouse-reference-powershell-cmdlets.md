---
title: Polecenia cmdlet programu PowerShell
description: Znajdź najważniejsze polecenia cmdlet programu PowerShell dla Azure SQL Data Warehouse, w tym informacje na temat wstrzymania i wznowienia bazy danych.
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
ms.openlocfilehash: c5f85f102d72ac2e4a0315109748d48573f49407
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721187"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Polecenia cmdlet programu PowerShell i interfejsy API REST dla SQL Data Warehouse
Wiele zadań administracyjnych SQL Data Warehouse można zarządzać przy użyciu poleceń cmdlet Azure PowerShell lub interfejsów API REST.  Poniżej przedstawiono kilka przykładów użycia poleceń programu PowerShell do automatyzowania typowych zadań w SQL Data Warehouse.  Aby zapoznać się z niektórymi dobrymi przykładami REST, zobacz artykuł [Zarządzanie skalowalnością przy użyciu interfejsu REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Wprowadzenie do Azure PowerShell poleceń cmdlet
1. Otwórz program Windows PowerShell.
2. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby zalogować się do Azure Resource Manager i wybrać subskrypcję.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Przykład wstrzymywania SQL Data Warehouse
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

## <a name="start-sql-data-warehouse-example"></a>Rozpocznij SQL Data Warehouse przykład
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
Te polecenia cmdlet programu PowerShell są obsługiwane w Azure SQL Data Warehouse.

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

* [Tworzenie SQL Data Warehouse przy użyciu programu PowerShell](create-data-warehouse-powershell.md)
* [Przywracanie bazy danych](sql-data-warehouse-restore-database-powershell.md)

Aby zapoznać się z innymi zadaniami, które można zautomatyzować za pomocą programu PowerShell, zobacz [Azure SQL Database polecenia cmdlet](https://docs.microsoft.com/powershell/module/az.sql). Nie wszystkie polecenia cmdlet Azure SQL Database są obsługiwane przez Azure SQL Data Warehouse.  Aby zapoznać się z listą zadań, które można zautomatyzować za pomocą usługi REST, zobacz [operacje dla Azure SQL Database](https://msdn.microsoft.com/library/azure/dn505719.aspx).
