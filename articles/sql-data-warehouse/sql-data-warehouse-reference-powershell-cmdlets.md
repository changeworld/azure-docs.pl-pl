---
title: Polecenia cmdlet programu PowerShell dla usługi Azure SQL Data Warehouse
description: Znajdź top poleceń cmdlet programu PowerShell dla usługi Azure SQL Data Warehouse, łącznie ze sposobem wstrzymywanie i wznawianie bazy danych.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 43bf1bcb1ccbb82fc15cddde85e06cac0abfd9c7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599710"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Polecenia cmdlet programu PowerShell i interfejsów API REST dla usługi SQL Data Warehouse
Wiele zadań administracyjnych usługi SQL Data Warehouse można zarządzać za pomocą poleceń cmdlet programu Azure PowerShell lub interfejsów API REST.  Poniżej przedstawiono kilka przykładów sposobu użycia poleceń programu PowerShell do automatyzacji typowych zadań w magazynie danych SQL.  Dla niektórych dobre przykłady REST, zobacz artykuł [Zarządzanie skalowanie REST][Manage scalability with REST].

> [!NOTE]
> Aby można było używać programu Azure PowerShell z usługą Magazyn danych SQL, potrzebujesz programu Azure PowerShell w wersji 1.0.3 lub nowszej.  Wersję można sprawdzić, uruchamiając **Get-Module - ListAvailable-Name Azure**.  Najnowszą wersję można zainstalować z [Instalatora platformy sieci Web firmy Microsoft][Microsoft Web Platform Installer].  Aby uzyskać więcej informacji na temat instalowania najnowszej wersji, zobacz [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Jak zainstalować i skonfigurować program Azure PowerShell).
> 
> 

## <a name="get-started-with-azure-powershell-cmdlets"></a>Wprowadzenie do poleceń cmdlet programu Azure PowerShell
1. Otwórz program Windows PowerShell.
2. W wierszu polecenia programu PowerShell, uruchom następujące polecenia, aby zarejestrować się w usłudze Azure Resource Manager i wyboru subskrypcji.
   
    ```PowerShell
    Connect-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Przykład magazynu danych SQL Wstrzymaj
Wstrzymać bazy danych o nazwie "Database02" znajdującej się na serwerze o nazwie "Serwer01".  Serwer jest w grupie zasobów platformy Azure o nazwie "ResourceGroup1."

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Zmiany, w tym przykładzie powoduje przekazanie w potoku pobrano obiekt do [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase].  W związku z tym bazy danych zostało wstrzymane. Polecenia końcowego pokazuje wyniki.

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

Zmiany, w tym przykładzie pobierana bazy danych o nazwie "Database02" z serwerem o nazwie "Serwer01", który jest zawarty w grupie zasobów o nazwie "ResourceGroup1." Go powoduje przekazanie w potoku pobrano obiekt do [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [!NOTE]
> Należy pamiętać, że jeśli serwer jest foo.database.windows.net, użyj "foo" jako ServerName — poleceń cmdlet programu PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Inne obsługiwane polecenia cmdlet programu PowerShell
Te polecenia cmdlet programu PowerShell są obsługiwane przez Magazyn danych SQL Azure.

* [Get-AzureRmSqlDatabase][Get-AzureRmSqlDatabase]
* [Get-AzureRmSqlDeletedDatabaseBackup][Get-AzureRmSqlDeletedDatabaseBackup]
* [Get-AzureRmSqlDatabaseRestorePoints][Get-AzureRmSqlDatabaseRestorePoints]
* [Nowe AzureRmSqlDatabase][New-AzureRmSqlDatabase]
* [Remove-AzureRmSqlDatabase][Remove-AzureRmSqlDatabase]
* [Przywracanie AzureRmSqlDatabase][Restore-AzureRmSqlDatabase]
* [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]
* [SELECT-AzureRmSubscription][Select-AzureRmSubscription]
* [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]
* [Wstrzymaj AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej przykładów programu PowerShell zobacz:

* [Utwórz magazyn danych SQL przy użyciu programu PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Przywracanie bazy danych][Database restore]

Inne zadania, których można zautomatyzować przy użyciu programu PowerShell, zobacz [polecenia cmdlet bazy danych SQL Azure][Azure SQL Database Cmdlets]. Należy pamiętać, że nie wszystkie polecenia cmdlet bazy danych SQL Azure są obsługiwane dla usługi Azure SQL Data Warehouse.  Aby uzyskać listę zadań, których można zautomatyzować z POZOSTAŁĄ, zobacz [operacje dla baz danych SQL Azure][Operations for Azure SQL Databases].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://msdn.microsoft.com/library/mt574084.aspx
[Operations for Azure SQL Databases]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
