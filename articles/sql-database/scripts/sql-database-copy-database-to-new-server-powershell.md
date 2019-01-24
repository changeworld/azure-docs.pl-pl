---
title: Przykład programu PowerShell — kopiowanie bazy danych Azure SQL Database na nowy serwer | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell umożliwiający skopiowanie bazy danych SQL na nowy serwer
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: douglaslMS
ms.author: douglasl
ms.reviewer: carlrab
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: 410f199dc056ce77d01c5097f75315be3c7ada91
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389374"
---
# <a name="use-powershell-to-copy-a-sql-database-to-a-new-server"></a>Kopiowanie bazy danych SQL na nowy serwer za pomocą programu PowerShell

Ten przykładowy skrypt programu PowerShell umożliwia utworzenie kopii istniejącej bazy danych na nowym serwerze.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="copy-a-database-to-a-new-server"></a>Kopiowanie bazy danych na nowy serwer

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/copy-database-to-new-server/copy-database-to-new-server.ps1?highlight=18-21 "Copy database to new server")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $sourceresourcegroupname
Remove-AzureRmResourceGroup -ResourceGroupName $targetresourcegroupname
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Tworzy serwer logiczny hostujący bazę danych lub pulę elastyczną. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Tworzy bazę danych na serwerze logicznym jako pojedynczą bazę danych lub bazę danych w puli. |
| [New-AzureRmSqlDatabaseCopy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy) | Tworzy kopię bazy danych, która korzysta z migawki utworzonej w bieżącym momencie. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
