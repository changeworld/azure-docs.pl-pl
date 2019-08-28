---
title: Przykładowy skrypt programu Azure PowerShell — przywracanie kopii zapasowej aplikacji do innej subskrypcji | Microsoft Docs
description: Przykładowy skrypt programu PowerShell — przywracanie aplikacji internetowej z kopii zapasowej w innej subskrypcji
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 5f293f290bd0c8b6c5546d37a43dde64a5af4f82
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098401"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Przywracanie aplikacji internetowej z kopii zapasowej w innej subskrypcji przy użyciu programu PowerShell

Ten przykładowy skrypt pobiera poprzednio utworzoną kopię zapasową z istniejącej aplikacji internetowej i przywraca ją w aplikacji internetowej w innej subskrypcji. 

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Jeśli aplikacja internetowa nie jest już potrzebna, użyj następującego polecenia, aby usunąć grupę zasobów, aplikację internetową i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Dodaje uwierzytelnione konto na potrzeby żądań poleceń cmdlet usługi Azure Resource Manager.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Pobiera listę kopii zapasowych dla aplikacji internetowej. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Tworzy aplikację internetową |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Przywraca aplikację internetową z utworzonej wcześniej kopii zapasowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
