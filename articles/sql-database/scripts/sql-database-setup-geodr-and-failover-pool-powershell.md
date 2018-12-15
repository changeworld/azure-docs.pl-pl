---
title: Przykład programu PowerShell — aktywna replikacja geograficzna bazy danych Azure SQL Database w puli | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell do konfigurowania aktywnej replikacji geograficznej dla bazy danych Azure SQL Database w puli i przełączania jej w tryb failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/07/2018
ms.openlocfilehash: 558ee0b9af5d33743274dba7704f9ee9dcb4cff9
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960281"
---
# <a name="use-powershell-to-configure-active-geo-replication-for-a-pooled-azure-sql-database"></a>Konfigurowanie aktywnej replikacji geograficznej dla bazy danych Azure SQL Database w puli przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell umożliwia skonfigurowanie aktywnej replikacji geograficznej dla bazy danych Azure SQL Database w elastycznej puli i przełączenie jej w tryb failover do repliki pomocniczej bazy danych Azure SQL Database.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-scripts"></a>Przykładowe skrypty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-elastic-pool.ps1?highlight=16-19 "Set up active geo-replication for elastic pool")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzureRmResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Tworzy serwer logiczny hostujący bazę danych lub pulę elastyczną. |
| [New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool) | Tworzy pulę elastyczną w ramach serwera logicznego. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Tworzy bazę danych na serwerze logicznym jako pojedynczą bazę danych lub bazę danych w puli. |
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | Aktualizuje właściwości bazy danych lub przenosi informacje o bazie danych do pul elastycznych, poza nie lub między nimi. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary)| Tworzy pomocniczą bazę danych dla istniejącej bazy danych i rozpoczyna replikację danych. |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)| Pobiera co najmniej jedną bazę danych. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary)| Przełącza pomocniczą bazę danych jako główną w celu zainicjowania trybu failover.|
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) | Pobiera linki replikacji geograficznej między bazą danych Azure SQL Database i grupą zasobów lub programem SQL Server. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
