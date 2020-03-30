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
ms.openlocfilehash: 5f22de08c4eabd3f9a3d6ee29cad3f0a9e8509e0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351386"
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

Odmiana, w tym przykładzie potoki pobranego obiektu do [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  W rezultacie baza danych jest wstrzymana. Końcowe polecenie pokazuje wyniki.

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

Odmiana w tym przykładzie pobiera bazę danych o nazwie "Database02" z serwera o nazwie "Server01", który znajduje się w grupie zasobów o nazwie "ResourceGroup1". Jest on potokiem pobranego obiektu do [resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Należy zauważyć, że jeśli serwer jest foo.database.windows.net, użyj "foo" jako -ServerName w poleceniach cmdlet programu PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Inne obsługiwane polecenia cmdlet programu PowerShell
Te polecenia cmdlet programu PowerShell są obsługiwane w magazynie danych usługi Azure Synapse Analytics.

* [Baza danych Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePunkt](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [Nowa baza danych AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Usuń-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Przywracanie-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Wznawianie-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Wybierz-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Wstrzymaj-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej przykładów programu PowerShell, zobacz:

* [Tworzenie magazynu danych przy użyciu programu PowerShell](create-data-warehouse-powershell.md)
* [Przywracanie bazy danych](sql-data-warehouse-restore-points.md)

Aby zapoznać się z innymi zadaniami, które można zautomatyzować za pomocą programu PowerShell, zobacz [polecenia cmdlet bazy danych SQL platformy Azure](https://docs.microsoft.com/powershell/module/az.sql). Nie wszystkie polecenia cmdlet usługi Azure SQL Database są obsługiwane w magazynie danych usługi Azure Synapse Analytics.  Aby uzyskać listę zadań, które można zautomatyzować za pomocą rest, zobacz [Operacje dla usługi Azure SQL Database](/rest/api/sql/).
