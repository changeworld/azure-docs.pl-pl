---
title: Przykład programu PowerShell— grupa trybu failover — pojedyncza baza danych usługi Azure SQL Database
description: Przykładowy skrypt programu Azure PowerShell do utworzenia pojedynczej bazy danych usługi Azure SQL Database, dodania jej do grupy trybu failover i przetestowania trybu failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 7315f747fb7319521b25e2eb6e7af72449867888
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691771"
---
# <a name="use-powershell-to-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Dodawanie pojedynczej bazy danych usługi Azure SQL Database do grupy trybu failover za pomocą programu PowerShell 

Ten przykład skryptu programu PowerShell tworzy pojedynczą bazę danych, tworzy grupę trybu failover, dodaje do niej bazę danych i testuje tryb failover. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten samouczek wymaga programu AZ PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-scripts"></a>Przykładowe skrypty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Nowy serwer AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory dla serwera logicznego. | 
| [Nowa baza danych AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy nową pojedynczą bazę danych usługi Azure SQL Database. | 
| [Nowa grupa AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Tworzy nową grupę trybu failover. |
| [Baza danych Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Pobiera jedną lub więcej baz danych SQL. |
| [Grupa Add-AzSqlDatabaseToFailover](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Dodaje jedną lub więcej baz danych SQL platformy Azure do grupy trybu failover. |
| [Grupa Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera lub wyświetla listę grup trybu failover usługi SQL Database platformy Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Wykonuje przebłaję awaryjną grupy trybu failover usługi Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów | 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
