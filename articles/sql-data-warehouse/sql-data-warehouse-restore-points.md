---
title: Punkty przywracania zdefiniowane przez użytkownika
description: Jak utworzyć punkt przywracania Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bf092b5b6c6eb88b565a940de56d614426e34d8e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759605"
---
# <a name="user-defined-restore-points"></a>Punkty przywracania zdefiniowane przez użytkownika

W tym artykule omówiono tworzenie nowego punktu przywracania zdefiniowanego przez użytkownika dla Azure SQL Data Warehouse przy użyciu programu PowerShell i Azure Portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Tworzenie punktów przywracania zdefiniowanych przez użytkownika za poorednictwem programu PowerShell

Aby utworzyć punkt przywracania zdefiniowany przez użytkownika, należy użyć polecenia cmdlet [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0) programu PowerShell.

1. Przed rozpoczęciem upewnij się, że [zainstalowano Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Otwórz program PowerShell.
3. Połącz się z kontem platformy Azure i Wyświetl listę wszystkich subskrypcji skojarzonych z Twoim kontem.
4. Wybierz subskrypcję zawierającą bazę danych, która ma zostać przywrócona.
5. Utwórz punkt przywracania natychmiastowej kopii magazynu danych.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Zapoznaj się z listą wszystkich istniejących punktów przywracania.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Utwórz punkty przywracania zdefiniowane przez użytkownika za pomocą Azure Portal

Punkty przywracania zdefiniowane przez użytkownika mogą być również tworzone za poorednictwem Azure Portal.

1. Zaloguj się do konta [Azure Portal](https://portal.azure.com/) .

2. Przejdź do SQL Data Warehouse, dla którego chcesz utworzyć punkt przywracania.

3. Wybierz pozycję **Przegląd** w okienku po lewej stronie, a następnie wybierz pozycję **+ nowy punkt przywracania**. Jeśli przycisk Nowy punkt przywracania nie jest włączony, upewnij się, że magazyn danych nie jest wstrzymany.

    ![Nowy punkt przywracania](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Określ nazwę punktu przywracania zdefiniowanego przez użytkownika, a następnie kliknij przycisk **Zastosuj**. Punkty przywracania zdefiniowane przez użytkownika mają domyślny okres przechowywania wynoszący siedem dni.

    ![Nazwa punktu przywracania](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącego magazynu danych](sql-data-warehouse-restore-active-paused-dw.md)
- [Przywracanie usuniętego magazynu danych](sql-data-warehouse-restore-deleted-dw.md)
- [Przywracanie z magazynu danych z geograficzną kopią zapasową](sql-data-warehouse-restore-from-geo-backup.md)

