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
ms.openlocfilehash: b36a64bb82449ace7acc1de0b3c2bc7c5efebe70
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685559"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Polecenia cmdlet programu PowerShell i interfejsy API REST dla SQL Data Warehouse
Wiele zadań administracyjnych SQL Data Warehouse można zarządzać przy użyciu poleceń cmdlet Azure PowerShell lub interfejsów API REST.  Poniżej przedstawiono kilka przykładów użycia poleceń programu PowerShell do automatyzowania typowych zadań w SQL Data Warehouse.  Aby zapoznać się z niektórymi dobrymi przykładami REST, zobacz artykuł [Zarządzanie skalowalnością przy użyciu interfejsu REST][Manage scalability with REST].

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
Odmiana, w tym przykładzie potok pobrano do [zawieszania-AzSqlDatabase][Suspend-AzSqlDatabase].  W związku z tym baza danych jest wstrzymana. Końcowe polecenie wyświetla wyniki.

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

Odmiana, w tym przykładzie pobiera bazę danych o nazwie "Database02" z serwera o nazwie "Serwer01", który znajduje się w grupie zasobów o nazwie "ResourceGroup1". Pobrano potok w celu [wznowienia-AzSqlDatabase][Resume-AzSqlDatabase].

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

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoint][Get-AzSqlDatabaseRestorePoint]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Resume-AzSqlDatabase][Resume-AzSqlDatabase]
* [SELECT-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej przykładów dla programu PowerShell, zobacz:

* [Tworzenie SQL Data Warehouse przy użyciu programu PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Przywracanie bazy danych][Database restore]

Aby zapoznać się z innymi zadaniami, które można zautomatyzować za pomocą programu PowerShell, zobacz [Azure SQL Database polecenia cmdlet][Azure SQL Database Cmdlets]. Należy pamiętać, że nie wszystkie polecenia cmdlet Azure SQL Database są obsługiwane przez Azure SQL Data Warehouse.  Aby zapoznać się z listą zadań, które można zautomatyzować za pomocą usługi REST, zobacz [operacje dla Azure SQL Database][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/az.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup
[Get-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint
[New-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase
[Remove-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Resume-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase
<!-- It appears that Select-AzSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase
[Suspend-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
