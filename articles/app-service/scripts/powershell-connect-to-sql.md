---
title: Przykładowy skrypt programu Azure PowerShell — łączenie aplikacji z bazą danych SQL Database | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — łączenie aplikacji usługi App Service z bazą danych SQL Database
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 799fb67b20c45653ec741a92ec7a75aff2f8c509
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087831"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>Łączenie aplikacji usługi App Service z usługą SQL Database

Z tego scenariusza dowiesz się, jak utworzyć bazę danych Azure SQL Database i aplikację usługi App Service. Następnie połączysz bazę danych SQL Database z aplikacją przy użyciu ustawień aplikacji.

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, aplikację usługi App Service i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Tworzy plan usługi App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Tworzy aplikację usługi App Service. |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer usługi SQL Database. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory dla serwera usługi SQL Database. |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy bazę danych lub bazę danych Elastic Database. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modyfikuje konfigurację aplikacji usługi App Service. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
