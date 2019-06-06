---
title: Przykład programu PowerShell — grupa trybu failover — pojedyncza baza danych Azure SQL Database | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell do konfigurowania grupy trybu failover aktywnej replikacji geograficznej dla pojedynczej bazy danych w usłudze Azure SQL Database i przełączania jej w tryb failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: a90753d075f7f4db9dbae95fde979d471db3247c
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729193"
---
# <a name="use-powershell-to-configure-an-active-geo-replication-failover-group-for-a-single-database-in-azure-sql-database"></a>Konfigurowanie grupy trybu failover aktywnej replikacji geograficznej dla pojedynczej bazy danych w usłudze Azure SQL Database przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell umożliwia skonfigurowanie grupy trybu failover aktywnej replikacji geograficznej dla pojedynczej bazy danych i przełączenie jej w tryb failover do repliki pomocniczej bazy danych.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z programu PowerShell lokalnie, ten samouczek wymaga programu PowerShell AZ 1.4.0 lub nowszej. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-scripts"></a>Przykładowe skrypty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-database-failover-group.ps1?highlight=18-21 "Set up failover group for single database")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Tworzy elastyczną pulę. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Aktualizuje właściwości bazy danych lub przenosi informacje o bazie danych do pul elastycznych, poza nie lub między nimi. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Tworzy pomocniczą bazę danych dla istniejącej bazy danych i rozpoczyna replikację danych. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Pobiera co najmniej jedną bazę danych. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Przełącza pomocniczą bazę danych jako główną w celu zainicjowania trybu failover.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Pobiera linki replikacji geograficznej między bazą danych Azure SQL Database i grupą zasobów lub programem SQL Server. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) | Przerywa replikację danych między bazą danych SQL Database i wybraną pomocniczą bazą danych. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Tworzy nową grupę trybu failover usługi Azure SQL Database dla wybranych serwerów. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Zamienia role serwerów w grupie trybu failover i przełącza wszystkie pomocnicze bazy danych do roli głównej. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera określoną grupę trybu failover bazy danych Azure SQL Database lub wyświetla listę grup trybu failover na serwerze. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
