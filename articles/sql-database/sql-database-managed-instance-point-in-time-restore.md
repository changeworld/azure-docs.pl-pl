---
title: Wystąpienie zarządzane — przywracanie w czasie (PITR)
description: Przywracanie bazy danych SQL w wystąpieniu zarządzanym do poprzedniego punktu w czasie.
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
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268810"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Przywracanie bazy danych SQL w wystąpieniu zarządzanym do poprzedniego punktu w czasie

Użyj przywracania punktu w czasie (PITR), aby utworzyć bazę danych jako kopię innej bazy danych z przeszłości. W tym artykule opisano sposób przywracania bazy danych w punkcie w czasie w wystąpieniu zarządzanym usługi Azure SQL Database.

Przywracanie w czasie jest przydatne w scenariuszach odzyskiwania, takich jak zdarzenia spowodowane przez błędy, nieprawidłowo załadowane dane lub usunięcie kluczowych danych. Można również użyć go po prostu do testowania lub inspekcji. Pliki kopii zapasowych są przechowywane przez 7 do 35 dni, w zależności od ustawień bazy danych.

Przywracanie w czasie można przywrócić bazę danych:

- z istniejącej bazy danych.
- z usuniętej bazy danych.
- do tego samego wystąpienia zarządzanego lub do innego wystąpienia zarządzanego. 

## <a name="limitations"></a>Ograniczenia

Przywracanie punktu w czasie do wystąpienia zarządzanego ma następujące ograniczenia:

- Podczas przywracania z jednego wystąpienia zarządzanego do innego, oba wystąpienia muszą być w tej samej subskrypcji i regionie. Przywracanie między regionami i między subskrypcjami nie jest obecnie obsługiwane.
- Przywracanie w czasie całego wystąpienia zarządzanego nie jest możliwe. W tym artykule wyjaśniono tylko, co jest możliwe: przywracanie punktu w czasie bazy danych, która jest hostowana w wystąpieniu zarządzanym.

> [!WARNING]
> Należy pamiętać o rozmiarze magazynu wystąpienia zarządzanego. W zależności od rozmiaru danych do przywrócenia może zabraknąć magazynu wystąpień. Jeśli nie ma wystarczającej ilości miejsca dla przywróconych danych, użyj innego podejścia.

W poniższej tabeli przedstawiono scenariusze przywracania punktu w czasie dla wystąpień zarządzanych:

|           |Przywracanie istniejącego bazy danych do tego samego wystąpienia zarządzanego| Przywracanie istniejącego bazy danych do innego wystąpienia zarządzanego|Przywróć porzucony bazę danych do tego samego wystąpienia zarządzanego|Przywracanie porzuconego bazy danych do innego wystąpienia zarządzanego|
|:----------|:----------|:----------|:----------|:----------|
|**Portal Azure**| Tak|Nie |Tak|Nie|
|**Interfejs wiersza polecenia platformy Azure**|Tak |Tak |Nie|Nie|
|**Powershell**| Tak|Tak |Tak|Tak|

## <a name="restore-an-existing-database"></a>Przywracanie istniejącej bazy danych

Przywracanie istniejącej bazy danych do tego samego wystąpienia przy użyciu portalu Azure, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby przywrócić bazę danych do innego wystąpienia, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby określić właściwości docelowego wystąpienia zarządzanego i grupy zasobów. Jeśli te parametry nie zostaną określone, baza danych zostanie domyślnie przywrócona do istniejącego wystąpienia. Witryna Azure portal nie obsługuje obecnie przywracania do innego wystąpienia.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 
2. Przejdź do wystąpienia zarządzanego i wybierz bazę danych, którą chcesz przywrócić.
3. Wybierz **pozycję Przywróć** na stronie bazy danych:

    ![Przywracanie bazy danych przy użyciu witryny Azure portal](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Na stronie **Przywracanie** wybierz punkt daty i godziny, do której chcesz przywrócić bazę danych.
5. Wybierz **pozycję Potwierdź,** aby przywrócić bazę danych. Ta akcja rozpoczyna proces przywracania, który tworzy nową bazę danych i wypełnia ją danymi z oryginalnej bazy danych w określonym momencie. Aby uzyskać więcej informacji na temat procesu odzyskiwania, zobacz [Czas odzyskiwania](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Jeśli nie masz jeszcze zainstalowanej usługi Azure PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Aby przywrócić bazę danych przy użyciu programu PowerShell, należy określić wartości parametrów w następującym poleceniu. Następnie uruchom polecenie:

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

Aby przywrócić bazę danych do innego wystąpienia zarządzanego, należy również określić nazwy docelowej grupy zasobów i docelowego wystąpienia zarządzanego:  

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

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Jeśli nie masz jeszcze zainstalowanego interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Aby przywrócić bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure, należy określić wartości parametrów w następującym poleceniu. Następnie uruchom polecenie:

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

Szczegółowe informacje na temat dostępnych parametrów można znaleźć w [dokumentacji interfejsu wiersza polecenia w celu przywrócenia bazy danych w wystąpieniu zarządzanym](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Przywracanie usuniętej bazy danych

Przywracanie usuniętej bazy danych można wykonać przy użyciu programu PowerShell lub witryny Azure Portal. Aby przywrócić usuniętą bazę danych do tego samego wystąpienia, użyj witryny Azure portal lub programu PowerShell. Aby przywrócić usuniętą bazę danych do innego wystąpienia, należy użyć programu PowerShell. 

### <a name="portal"></a>Portal 


Aby odzyskać zarządzaną bazę danych za pomocą portalu Azure, otwórz stronę przeglądu wystąpienia zarządzanego i wybierz pozycję **Usunięte bazy danych**. Wybierz usuniętą bazę danych, którą chcesz przywrócić, i wpisz nazwę nowej bazy danych, która zostanie utworzona przy przywróceniu danych z kopii zapasowej.

  ![Zrzut ekranu przedstawiający przywracanie usuniętej bazy danych wystąpień SQL platformy Azure](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Aby przywrócić bazę danych do tego samego wystąpienia, zaktualizuj wartości parametrów, a następnie uruchom następujące polecenie programu PowerShell: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Aby przywrócić bazę danych do innego wystąpienia zarządzanego, należy również określić nazwy docelowej grupy zasobów i docelowego wystąpienia zarządzanego:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Zastępowanie istniejącej bazy danych

Aby zastąpić istniejącą bazę danych, należy:

1. Upuść istniejącą bazę danych, którą chcesz zastąpić.
2. Zmień nazwę bazy danych przywróconej w czasie na nazwę porzuconej bazy danych.

### <a name="drop-the-original-database"></a>Upuść oryginalną bazę danych

Możesz upuścić bazę danych przy użyciu witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Bazę danych można również usunąć, łącząc się bezpośrednio z wystąpieniem zarządzanym, uruchamiając program SQL Server Management Studio (SSMS), a następnie uruchamiając następujące polecenie Transact-SQL (T-SQL):You can also drop the database by connecting to the managed instance directly, starting SQL Server Management Studio (SSMS), and then running the following Transact-SQL (T-SQL) command:

```sql
DROP DATABASE WorldWideImporters;
```

Użyj jednej z następujących metod, aby połączyć się z bazą danych w wystąpieniu zarządzanym:

- [SSMS/Azure Data Studio za pośrednictwem maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt-lokacja](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Publiczny punkt końcowy](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Portal](#tab/azure-portal)

W witrynie Azure portal wybierz bazę danych z wystąpienia zarządzanego, a następnie wybierz pozycję **Usuń**.

   ![Usuwanie bazy danych przy użyciu witryny Azure portal](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Użyj następującego polecenia programu PowerShell, aby usunąć istniejącą bazę danych z wystąpienia zarządzanego:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj następującego polecenia interfejsu wiersza polecenia platformy Azure, aby usunąć istniejącą bazę danych z wystąpienia zarządzanego:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Zmienianie nazwy nowej bazy danych w celu dopasowania do oryginalnej nazwy bazy danych

Połącz się bezpośrednio z wystąpieniem zarządzanym i uruchom program SQL Server Management Studio. Następnie uruchom następującą kwerendę Transact-SQL (T-SQL). Kwerenda zmieni nazwę przywróconej bazy danych na nazwę porzuconej bazy danych, którą zamierzasz zastąpić.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Użyj jednej z następujących metod, aby połączyć się z bazą danych w wystąpieniu zarządzanym:

- [Maszyna wirtualna platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt-lokacja](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Publiczny punkt końcowy](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [automatycznych kopiach zapasowych](sql-database-automated-backups.md).
