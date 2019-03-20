---
title: Przykład programu PowerShell — przywracanie kopii zapasowej bazy danych Azure SQL Database | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell umożliwiający przywrócenie pojedynczej bazy danych usługi Azure SQL Database z geograficznie nadmiarowych kopii zapasowych
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3bfefa704fdd819b3841dcc58866c310353bfdc3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57883614"
---
# <a name="use-powershell-to-restore-an-azure-sql-single-database-from-backups"></a>Przywracanie pojedynczej bazy danych usługi Azure SQL Database z kopii zapasowych przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell umożliwia przywrócenie bazy danych Azure SQL Database z geograficznie nadmiarowej kopii zapasowej, przywrócenie najnowszej kopii zapasowej usuniętej bazy danych Azure SQL Database oraz przywrócenie bazy danych Azure SQL Database do konkretnego punktu w czasie.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli zdecydujesz się zainstalować i korzystać z programu PowerShell lokalnie, ten samouczek wymaga programu PowerShell AZ 1.4.0 lub nowszej. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. | 
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer usługi SQL Database, który hostuje pojedynczą bazę danych lub elastyczną pulę. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy bazę danych na serwerze usługi SQL Database: autonomiczną lub w puli. |
[Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Pobiera geograficznie nadmiarową kopię zapasową bazy danej, autonomicznej lub w puli. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Przywraca bazę danych SQL, autonomiczną lub w puli. |
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Usuwa bazę danych SQL Azure, autonomiczną lub w puli. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Pobiera usuniętą bazę danych, autonomiczną lub w puli, którą można przywrócić. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
