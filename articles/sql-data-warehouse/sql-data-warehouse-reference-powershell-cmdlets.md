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
ms.openlocfilehash: 412c2faead0102d3995e3df9db31b81c4dd4aa22
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452009"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Polecenia cmdlet programu PowerShell i interfejsów API REST usługi SQL Data Warehouse
Wiele zadań administracyjnych SQL Data Warehouse mogą być zarządzane przy użyciu poleceń cmdlet programu Azure PowerShell lub interfejsów API REST.  Poniżej przedstawiono kilka przykładów sposobu używania poleceń programu PowerShell do automatyzacji typowych zadań w usłudze SQL Data Warehouse.  Aby uzyskać pewne dobre przykłady REST, zobacz artykuł [Zarządzanie skalowalności za pomocą architektury REST][Manage scalability with REST].

> [!NOTE]
> Aby można było używać programu Azure PowerShell z usługą SQL Data Warehouse, potrzebujesz programu Azure PowerShell w wersji 1.0.3 lub nowszej.  Wersję można sprawdzić, uruchamiając **Get-Module - ListAvailable-Name Azure**.  Najnowszą wersję można zainstalować z [Instalatora platformy sieci Web firmy Microsoft][Microsoft Web Platform Installer].  Aby uzyskać więcej informacji na temat instalowania najnowszej wersji, zobacz [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Jak zainstalować i skonfigurować program Azure PowerShell).
> 
> 

## <a name="get-started-with-azure-powershell-cmdlets"></a>Wprowadzenie do poleceń cmdlet programu Azure PowerShell
1. Otwórz program Windows PowerShell.
2. W wierszu polecenia programu PowerShell uruchom następujące polecenia, aby zarejestrować się w usłudze Azure Resource Manager i wybierz swoją subskrypcję.
   
    ```PowerShell
    Connect-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Przykład magazynu danych SQL wstrzymania
Wstrzymywanie bazy danych o nazwie "Database02" znajdującej się na serwerze o nazwie "Serwer01".  Serwer znajduje się w grupie zasobów platformy Azure o nazwie "ResourceGroup1."

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Odmiana, w tym przykładzie powoduje przekazanie w potoku pobrany obiekt do [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase].  W rezultacie baza danych jest wstrzymany. Końcowe polecenie wyświetla wyniki.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Uruchom przykład magazynu danych SQL
Wznów działanie bazy danych o nazwie "Database02" znajdującej się na serwerze o nazwie "Serwer01". Serwer znajduje się w grupie zasobów o nazwie "ResourceGroup1."

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Odmiana, w tym przykładzie pobiera bazę danych o nazwie "Database02" z serwerem o nazwie "Serwer01", który jest zawarty w grupie zasobów o nazwie "ResourceGroup1." Przekazuje on pobrany obiekt do [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [!NOTE]
> Pamiętaj, że jeśli serwer to foo.database.windows.net, użyj "foo", jako ServerName — polecenia cmdlet programu PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Inne obsługiwane polecenia cmdlet programu PowerShell
Te polecenia cmdlet programu PowerShell są obsługiwane za pomocą usługi Azure SQL Data Warehouse.

* [Get-AzureRmSqlDatabase][Get-AzureRmSqlDatabase]
* [Get-AzureRmSqlDeletedDatabaseBackup][Get-AzureRmSqlDeletedDatabaseBackup]
* [Get-AzureRmSqlDatabaseRestorePoints][Get-AzureRmSqlDatabaseRestorePoints]
* [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase]
* [Remove-AzureRmSqlDatabase][Remove-AzureRmSqlDatabase]
* [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase]
* [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]
* [Select-AzureRmSubscription][Select-AzureRmSubscription]
* [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]
* [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]

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
[Get-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabase
[Get-AzureRmSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzureRmSqlDeletedDatabaseBackup
[Get-AzureRmSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzureRmSqlDatabaseRestorePoints
[New-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/New-AzureRmSqlDatabase
[Remove-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzureRmSqlDatabase
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Restore-AzureRmSqlDatabase
[Resume-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Resume-AzureRmSqlDatabase
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzureRmSqlDatabase
[Suspend-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Suspend-AzureRmSqlDatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
