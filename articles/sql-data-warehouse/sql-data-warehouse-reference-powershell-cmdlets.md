---
title: Polecenia cmdlet programu PowerShell dla usługi Azure SQL Data Warehouse
description: Znajdź najważniejsze poleceń cmdlet programu PowerShell dla usługi Azure SQL Data Warehouse, w tym jak wstrzymać i wznowić bazy danych.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 10ba16fc06a4678c7bd70cbb7e6797b8d9be8ff4
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485123"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Polecenia cmdlet programu PowerShell i interfejsów API REST usługi SQL Data Warehouse
Wiele zadań administracyjnych SQL Data Warehouse mogą być zarządzane przy użyciu poleceń cmdlet programu Azure PowerShell lub interfejsów API REST.  Poniżej przedstawiono kilka przykładów sposobu używania poleceń programu PowerShell do automatyzacji typowych zadań w usłudze SQL Data Warehouse.  Aby uzyskać pewne dobre przykłady REST, zobacz artykuł [Zarządzanie skalowalności za pomocą architektury REST][Manage scalability with REST].

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Wprowadzenie do poleceń cmdlet programu Azure PowerShell
1. Otwórz program Windows PowerShell.
2. W wierszu polecenia programu PowerShell uruchom następujące polecenia, aby zarejestrować się w usłudze Azure Resource Manager i wybierz swoją subskrypcję.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Przykład magazynu danych SQL wstrzymania
Wstrzymywanie bazy danych o nazwie "Database02" znajdującej się na serwerze o nazwie "Serwer01".  Serwer znajduje się w grupie zasobów platformy Azure o nazwie "ResourceGroup1."

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Odmiana, w tym przykładzie powoduje przekazanie w potoku pobrany obiekt do [AzSqlDatabase Wstrzymaj][Suspend-AzSqlDatabase].  W rezultacie baza danych jest wstrzymany. Końcowe polecenie wyświetla wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Uruchom przykład magazynu danych SQL
Wznów działanie bazy danych o nazwie "Database02" znajdującej się na serwerze o nazwie "Serwer01". Serwer znajduje się w grupie zasobów o nazwie "ResourceGroup1."

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Odmiana, w tym przykładzie pobiera bazę danych o nazwie "Database02" z serwerem o nazwie "Serwer01", który jest zawarty w grupie zasobów o nazwie "ResourceGroup1." Przekazuje on pobrany obiekt do [AzSqlDatabase Wznów][Resume-AzSqlDatabase].

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Pamiętaj, że jeśli serwer to foo.database.windows.net, użyj "foo", jako ServerName — polecenia cmdlet programu PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Inne obsługiwane polecenia cmdlet programu PowerShell
Te polecenia cmdlet programu PowerShell są obsługiwane za pomocą usługi Azure SQL Data Warehouse.

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoints][Get-AzSqlDatabaseRestorePoints]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Resume-AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej przykładów programu PowerShell zobacz:

* [Tworzenie magazynu danych SQL przy użyciu programu PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Przywracanie bazy danych][Database restore]

Aby uzyskać inne zadania, które można zautomatyzować za pomocą programu PowerShell, zobacz [polecenia cmdlet usługi Azure SQL Database][Azure SQL Database Cmdlets]. Należy pamiętać, że nie wszystkie polecenia cmdlet usługi Azure SQL Database są obsługiwane w przypadku usługi Azure SQL Data Warehouse.  Aby uzyskać listę zadań, które można zautomatyzować za pomocą architektury REST, zobacz [operacje usługi Azure SQL Database][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup
[Get-AzSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoints
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
