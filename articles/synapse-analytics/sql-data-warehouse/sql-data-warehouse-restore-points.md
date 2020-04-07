---
title: Zdefiniowane przez użytkownika punkty przywracania
description: Jak utworzyć punkt przywracania dla puli SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5c9b7eb1b03b6b6e3721c13f9ebf7da25dd2e376
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745059"
---
# <a name="user-defined-restore-points"></a>Zdefiniowane przez użytkownika punkty przywracania

W tym artykule dowiesz się, aby utworzyć nowy punkt przywracania zdefiniowany przez użytkownika dla puli SQL w usłudze Azure Synapse Analytics przy użyciu programu PowerShell i witryny Azure portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Tworzenie punktów przywracania zdefiniowanych przez użytkownika za pośrednictwem programu PowerShell

Aby utworzyć punkt przywracania zdefiniowany przez użytkownika, należy użyć polecenia cmdlet [programu PowerShell programu New-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

1. Przed rozpoczęciem należy zainstalować [program Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otwórz program PowerShell.
3. Połącz się ze swoim kontem platformy Azure i wymień wszystkie subskrypcje skojarzone z Twoim kontem.
4. Wybierz subskrypcję zawierającą bazę danych, która ma zostać przywrócona.
5. Utwórz punkt przywracania dla natychmiastowej kopii magazynu danych.

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

6. Zobacz listę wszystkich istniejących punktów przywracania.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Tworzenie punktów przywracania zdefiniowanych przez użytkownika za pośrednictwem witryny Azure Portal

Punkty przywracania zdefiniowane przez użytkownika można również tworzyć za pośrednictwem witryny Azure Portal.

1. Zaloguj się do swojego konta [w witrynie Azure portal.](https://portal.azure.com/)

2. Przejdź do puli SQL, dla której chcesz utworzyć punkt przywracania.

3. Wybierz **pozycję Przegląd** z lewego okienka, wybierz pozycję + Nowy punkt **przywracania**. Jeśli przycisk Nowy punkt przywracania nie jest włączony, upewnij się, że pula SQL nie jest wstrzymana.

    ![Nowy punkt przywracania](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Określ nazwę punktu przywracania zdefiniowanego przez użytkownika i kliknij przycisk **Zastosuj**. Punkty przywracania zdefiniowane przez użytkownika mają domyślny okres przechowywania wynoszący siedem dni.

    ![Nazwa punktu przywracania](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącej puli SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Przywracanie usuniętej puli SQL](sql-data-warehouse-restore-deleted-dw.md)
- [Przywracanie z puli SQL kopii zapasowej geograficznej](sql-data-warehouse-restore-from-geo-backup.md)

