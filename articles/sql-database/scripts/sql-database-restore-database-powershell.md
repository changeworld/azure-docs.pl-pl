---
title: Przykład programu PowerShell — przywracanie kopii zapasowej bazy danych Azure SQL Database | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell umożliwiający przywrócenie bazy danych Azure SQL Database z geograficznie nadmiarowych kopii zapasowych
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/07/2018
ms.openlocfilehash: 95ce6b0b7cabd5d677bcfa4434d419c7bfc0b7b5
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869878"
---
# <a name="use-powershell-to-restore-an-azure-sql-database-from-backups"></a>Przywracanie bazy danych Azure SQL Database z kopii zapasowych przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell umożliwia przywrócenie bazy danych Azure SQL Database z geograficznie nadmiarowej kopii zapasowej, przywrócenie najnowszej kopii zapasowej usuniętej bazy danych Azure SQL Database oraz przywrócenie bazy danych Azure SQL Database do konkretnego punktu w czasie.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. | [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Tworzy serwer logiczny hostujący bazę danych lub pulę elastyczną. | 
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Tworzy bazę danych na serwerze logicznym jako pojedynczą bazę danych lub bazę danych w puli. |
[Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) | Pobiera geograficznie nadmiarową kopię zapasową bazy danych. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) | Przywraca bazę danych SQL. |
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase) | Usuwa bazę danych Azure SQL Database. |
| [Get-AzureRmSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Pobiera usuniętą bazę danych, którą można przywrócić. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
