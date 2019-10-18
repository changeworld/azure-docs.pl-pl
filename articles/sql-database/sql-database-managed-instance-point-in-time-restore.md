---
title: SQL Database Zarządzanie wystąpieniami zarządzanymi — przywracanie do punktu w czasie | Microsoft Docs
description: Przywracanie bazy danych SQL w wystąpieniu zarządzanym do wcześniejszego punktu w czasie.
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
ms.openlocfilehash: 61249d8ac21c3bb698deb92e5f46a8998205ced9
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529664"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Przywracanie bazy danych SQL w wystąpieniu zarządzanym do wcześniejszego punktu w czasie

Użyj funkcji przywracania do określonego momentu (kopie), aby utworzyć bazę danych jako kopię innej bazy danych od pewnego czasu w przeszłości. W tym artykule opisano, jak wykonać przywracanie do punktu w czasie dla bazy danych w wystąpieniu zarządzanym Azure SQL Database.

Przywracanie do punktu w czasie jest przydatne w scenariuszach odzyskiwania, takich jak zdarzenia spowodowane błędami, nieprawidłowo załadowane dane lub usuwanie kluczowych danych. Można go również użyć po prostu do testowania lub inspekcji. Pliki kopii zapasowej są przechowywane przez 7 – 35 dni, w zależności od ustawień bazy danych.

Przywracanie do punktu w czasie może:

- Przywracanie bazy danych z istniejącej bazy danych.
- Przywracanie bazy danych z usuniętej bazy danych.

W przypadku wystąpienia zarządzanego przywracanie do punktu w czasie może również:

- Przywróć bazę danych do tego samego wystąpienia zarządzanego.
- Przywracanie bazy danych do innego wystąpienia zarządzanego.

> [!NOTE]
> Przywracanie do punktu w czasie całego wystąpienia zarządzanego nie jest możliwe. W tym artykule wyjaśniono, co jest możliwe: Przywracanie do punktu w czasie dla bazy danych hostowanej w wystąpieniu zarządzanym.

## <a name="limitations"></a>Ograniczenia

Gdy przywracasz z jednego wystąpienia zarządzanego do innego, oba wystąpienia muszą znajdować się w tej samej subskrypcji i regionie. Przywracanie między regionami i subskrypcjami nie jest obecnie obsługiwane.

> [!WARNING]
> Zapoznaj się z rozmiarem magazynu zarządzanego wystąpienia. W zależności od rozmiaru danych do przywrócenia można wypróbować magazyn z wystąpieniami. Jeśli nie ma wystarczającej ilości miejsca na przywrócone dane, użyj innego podejścia.

W poniższej tabeli przedstawiono scenariusze przywracania do punktu w czasie dla wystąpień zarządzanych:

|           |Przywróć istniejącą bazę danych do tego samego wystąpienia zarządzanego| Przywracanie istniejącej bazy danych do innego wystąpienia zarządzanego|Przywracanie usuniętej bazy danych do tego samego wystąpienia zarządzanego|Przywracanie usuniętej bazy danych do innego wystąpienia zarządzanego|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portal**| Tak|Nie |Nie|Nie|
|**Interfejs wiersza polecenia platformy Azure**|Tak |Tak |Nie|Nie|
|**Program PowerShell**| Tak|Tak |Tak|Tak|

## <a name="restore-an-existing-database"></a>Przywracanie istniejącej bazy danych

Przywróć istniejącą bazę danych do tego samego wystąpienia przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby przywrócić bazę danych do innego wystąpienia, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby określić właściwości docelowego wystąpienia zarządzanego i grupy zasobów. Jeśli nie określisz tych parametrów, baza danych zostanie domyślnie przywrócona do istniejącego wystąpienia. Azure Portal nie obsługuje obecnie przywracania do innego wystąpienia.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się do [portalu Azure](https://portal.azure.com). 
2. Przejdź do wystąpienia zarządzanego i wybierz bazę danych, którą chcesz przywrócić.
3. Na stronie Baza danych wybierz pozycję **Przywróć** :

    ![Przywracanie bazy danych przy użyciu Azure Portal](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Na stronie **przywracanie** wybierz punkt dla daty i godziny, do której chcesz przywrócić bazę danych.
5. Wybierz pozycję **Potwierdź** , aby przywrócić bazę danych. Ta akcja uruchamia proces przywracania, który tworzy nową bazę danych i wypełnia ją danymi z oryginalnej bazy danych w określonym momencie. Aby uzyskać więcej informacji na temat procesu odzyskiwania, zobacz [czas odzyskiwania](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Jeśli nie masz jeszcze zainstalowanego Azure PowerShell, zobacz [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps).

Aby przywrócić bazę danych przy użyciu programu PowerShell, określ wartości parametrów w poniższym poleceniu. Następnie uruchom polecenie:

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

Aby przywrócić bazę danych do innego wystąpienia zarządzanego, należy również określić nazwy docelowej grupy zasobów i wystąpienia zarządzanego:  

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

Aby przywrócić bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure, określ wartości parametrów w następującym poleceniu. Następnie uruchom polecenie:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Aby przywrócić bazę danych do innego wystąpienia zarządzanego, należy również określić nazwy docelowej grupy zasobów i wystąpienia zarządzanego:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Szczegółowe wyjaśnienie dostępnych parametrów można znaleźć w [dokumentacji interfejsu wiersza polecenia w celu przywrócenia bazy danych w wystąpieniu zarządzanym](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Przywracanie usuniętej bazy danych

Przywracanie usuniętej bazy danych można wykonać tylko przy użyciu programu PowerShell. Bazę danych można przywrócić do tego samego wystąpienia lub innego wystąpienia.

Aby przywrócić usuniętą bazę danych za pomocą programu PowerShell, określ wartości parametrów w następującym poleceniu. Następnie uruchom polecenie:

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

Aby przywrócić usuniętą bazę danych do innego wystąpienia, należy zmienić nazwy grupy zasobów i wystąpienia zarządzanego. Upewnij się również, że parametr Location pasuje do lokalizacji grupy zasobów i wystąpienia zarządzanego.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-an-existing-database"></a>Zastąp istniejącą bazę danych

Aby zastąpić istniejącą bazę danych, musisz:

1. Usuń istniejącą bazę danych, która ma zostać zastąpiona.
2. Zmień nazwę bazy danych "punkt w czasie" na nazwę usuniętej bazy danych.

### <a name="drop-the-original-database"></a>Usuń oryginalną bazę danych

Bazę danych można usunąć przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Możesz również porzucić bazę danych, łącząc się bezpośrednio z wystąpieniem zarządzanym, rozpoczynając SQL Server Management Studio (SSMS), a następnie uruchamiając następujące polecenie Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Użyj jednej z następujących metod, aby nawiązać połączenie z bazą danych w wystąpieniu zarządzanym:

- [Narzędzie SSMS/Azure Data Studio za pośrednictwem maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt-lokacja](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Publiczny punkt końcowy](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

W Azure Portal wybierz bazę danych z wystąpienia zarządzanego, a następnie wybierz pozycję **Usuń**.

   ![Usuwanie bazy danych przy użyciu Azure Portal](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

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

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Zmień nazwę nowej bazy danych tak, aby była zgodna z oryginalną nazwą bazy danych

Połącz się bezpośrednio z wystąpieniem zarządzanym i uruchom SQL Server Management Studio. Następnie uruchom następujące zapytanie Transact-SQL (T-SQL). Zapytanie zmieni nazwę przywróconej bazy danych na wartość porzuconej bazy danych, która ma zostać zastąpiona.

```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Użyj jednej z następujących metod, aby nawiązać połączenie z bazą danych w wystąpieniu zarządzanym:

- [Maszyna wirtualna platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt-lokacja](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Publiczny punkt końcowy](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Następne kroki

Uzyskaj informacje na temat [zautomatyzowanych kopii zapasowych](sql-database-automated-backups.md).
