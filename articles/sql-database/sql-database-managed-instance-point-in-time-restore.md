---
title: SQL Database Zarządzanie wystąpieniami zarządzanymi — przywracanie do punktu w czasie | Microsoft Docs
description: Jak przywrócić bazę danych w wystąpieniu zarządzanym SQL do wcześniejszego punktu w czasie.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862137"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>Przywracanie bazy danych wystąpienia zarządzanego SQL do wcześniejszego punktu w czasie

Przywracanie do określonego momentu (kopie) umożliwia tworzenie bazy danych jako kopii innej bazy danych w pewnym momencie w przeszłości. W tym artykule opisano, jak wykonać przywracanie do punktu w czasie dla bazy danych w wystąpieniu zarządzanym.

Przywracanie do punktu w czasie może być używane w scenariuszach odzyskiwania, takich jak zdarzenia spowodowane błędami, nieprawidłowo załadowane dane, usuwanie kluczowych danych i inne problemy, jak również po prostu do testowania lub inspekcji. W zależności od ustawień bazy danych pliki kopii zapasowej są przechowywane przez okres od 7 do 35 dni.

Przywracanie do punktu w czasie może służyć do:

- Przywracanie bazy danych z istniejącej bazy danych.
- Przywracanie bazy danych z usuniętej bazy danych.

Ponadto w przypadku wystąpienia zarządzanego można użyć przywracania do punktu w czasie, aby: 

- Przywróć bazę danych do tego samego wystąpienia zarządzanego.
- Przywracanie bazy danych do innego wystąpienia zarządzanego.


> [!NOTE]
> Przywracanie do punktu w czasie całego wystąpienia zarządzanego nie jest możliwe. Co jest możliwe i wyjaśniono w tym artykule, to przywracanie do punktu w czasie dla bazy danych hostowanej w wystąpieniu zarządzanym.


## <a name="limitations"></a>Ograniczenia

Podczas przywracania do innego wystąpienia zarządzanego oba wystąpienia muszą znajdować się w tej samej subskrypcji i regionie. Przywracanie między regionami i subskrypcjami nie jest obecnie obsługiwane.

> [!WARNING]
> Należy zachować ostrożność z rozmiarem magazynu zarządzanego wystąpienia — w zależności od rozmiaru przywracanych danych może być wykorzystana z magazynu wystąpień. Jeśli nie ma wystarczającej ilości miejsca na przywrócone dane, użyj podejścia alternatywnego.

W poniższej tabeli przedstawiono scenariusze odzyskiwania do punktu w czasie dla wystąpienia zarządzanego:

|           |Przywróć istniejącą bazę danych| Przywróć istniejącą bazę danych|Przywracanie usuniętej bazy danych| Przywracanie usuniętej bazy danych|
|:----------|:----------|:----------|:----------|:----------|
|Miejsce docelowe| Analogicznie MI|Inna MI |Analogicznie MI|Inna MI |
|Azure Portal| Tak|Nie |Nie|Nie|
|Interfejs wiersza polecenia platformy Azure|Tak |Yes |Nie|Nie|
|PowerShell| Tak|Yes |Yes|Tak|


## <a name="restore-existing-database"></a>Przywróć istniejącą bazę danych

Przywróć istniejącą bazę danych do tego samego wystąpienia przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Przywracanie bazy danych do innego wystąpienia przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure przez określenie docelowego wystąpienia zarządzanego i właściwości grupy zasobów. Jeśli te parametry nie zostaną określone, domyślnie baza danych zostanie przywrócona do istniejącego wystąpienia. Przywracanie do innego wystąpienia nie jest obecnie obsługiwane przez Azure Portal. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
1. Przejdź do wystąpienia zarządzanego i wybierz bazę danych, którą chcesz przywrócić. 
1. Na stronie Baza danych wybierz pozycję **Przywróć** . 

    ![Przywróć istniejącą bazę danych](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. Na stronie **przywracanie** wybierz punkt daty i godziny w historii, do której chcesz przywrócić bazę danych.
1. Wybierz pozycję **Potwierdź** , aby przywrócić bazę danych. Spowoduje to uruchomienie procesu przywracania, który tworzy nową bazę danych i jest wypełniany danymi z oryginalnej bazy danych w żądanym momencie. Aby uzyskać więcej informacji na temat procesu odzyskiwania, zobacz [czas odzyskiwania](sql-database-recovery-using-backups.md#recovery-time). 

1. Znajdź wystąpienie zarządzane
1. Wybierz bazę danych, którą chcesz przywrócić
1. Na ekranie Baza danych kliknij przycisk Przywróć akcję
1. Na ekranie przywracanie wybierz pozycję Data i godzina punktu w historii, do której ma zostać przywrócona baza danych.
1. Po potwierdzeniu proces przywracania zostanie uruchomiony, a w zależności od rozmiaru bazy danych zostanie utworzona nowa baza danych z danymi z oryginalnej bazy danych w żądanym momencie. W przypadku czasu trwania operacji przywracania Sprawdź odzyskiwanie przy użyciu kopii zapasowych.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Jeśli nie masz jeszcze zainstalowanego Azure PowerShell, zobacz [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps).

Aby przywrócić bazę danych przy użyciu programu PowerShell, zaktualizuj parametry za pomocą wartości i uruchom następujące polecenie:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"
 
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId
 
Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                               -InstanceName $managedInstanceName `
                               -Name $databaseName `
                               -PointInTime $pointInTime `
                               -TargetInstanceDatabaseName $targetDatabase `
```

Aby przywrócić bazę danych do innego wystąpienia zarządzanego, należy ustawić nazwę docelowej grupy zasobów i nazwę docelowego wystąpienia zarządzanego.  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Aby uzyskać szczegółowe informacje, zobacz [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).


# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Jeśli nie masz jeszcze zainstalowanego interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Aby przywrócić bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure, zaktualizuj parametry za pomocą wartości i uruchom następujące polecenie:


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


Aby przywrócić bazę danych do innego wystąpienia zarządzanego, należy ustawić nazwę docelowej grupy zasobów i nazwę docelowego wystąpienia zarządzanego.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Aby uzyskać szczegółowe wyjaśnienie dostępnych parametrów, zobacz [interfejs wiersza polecenia wystąpienia zarządzanego](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore). 

---

## <a name="restore-a-deleted-database"></a>Przywracanie usuniętej bazy danych 
 
Przywracanie usuniętej bazy danych można wykonać tylko przy użyciu programu PowerShell. Bazę danych można przywrócić do tego samego wystąpienia lub innego wystąpienia. 

Aby przywrócić usuniętą bazę danych za pomocą programu PowerShell, zaktualizuj parametry za pomocą wartości i uruchom następujące polecenie:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

Aby przywrócić usuniętą bazę danych do innego wystąpienia, należy zmienić nazwę grupy zasobów i nazwę wystąpienia zarządzanego.

Parametr Location powinien odpowiadać lokalizacji grupy zasobów i wystąpienia zarządzanego.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>Zastąp istniejącą bazę danych 
 
Aby zastąpić istniejącą bazę danych, należy również:

1. Usuń istniejącą bazę danych, która ma zostać zastąpiona.
1. Zmień nazwę przywróconego punktu w czasie na nazwę bazy danych, która została porzucona. 


### <a name="drop-original-database"></a>Usuń oryginalną bazę danych 
 
Porzucenie bazy danych można wykonać przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. 

Możesz również porzucić bazę danych, łącząc się bezpośrednio z wystąpieniem zarządzanym, uruchamiając SQL Server Management Studio (SSMS) i uruchamiając poniższe polecenie języka Transact-SQL (T-SQL).

```sql
DROP DATABASE WorldWideImporters;
```

Użyj jednej z następujących metod, aby nawiązać połączenie z bazą danych wystąpienia zarządzanego: 

- [Maszyna wirtualna SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt-lokacja](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Publiczny punkt końcowy](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

W Azure Portal wybierz bazę danych z wystąpienia zarządzanego i wybierz pozycję **Usuń**.

   ![Przywróć istniejącą bazę danych](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Użyj następującego polecenia programu PowerShell, aby usunąć istniejącą bazę danych z wystąpienia zarządzanego: 

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj następującego polecenia platformy Azure CLI, aby usunąć istniejącą bazę danych z wystąpienia zarządzanego: 

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---


### <a name="alter-new-database-name-to-original"></a>Zmień nazwę nowej bazy danych na oryginalną

Połącz się bezpośrednio z wystąpieniem zarządzanym, uruchom SQL Server Management Studio, a następnie wykonaj następujące zapytanie Transact-SQL (T-SQL), aby zmienić nazwę przywróconej bazy danych na bazę danych porzuconej, która ma zostać zastąpiona. 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


Użyj jednej z następujących metod, aby nawiązać połączenie z bazą danych wystąpienia zarządzanego: 

- [Maszyna wirtualna SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt-lokacja](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Publiczny punkt końcowy](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [przechowywania długoterminowego](sql-database-long-term-retention.md) i [zautomatyzowanych kopii zapasowych](sql-database-automated-backups.md). 
