---
title: Przykładowy skrypt programu PowerShell — przywracanie aplikacji internetowej z kopii zapasowej | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — przywracanie aplikacji internetowej z kopii zapasowej
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 85cbcb4b01161e0004d38f08ae8c30fd398727ff
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087894"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Przywracanie aplikacji internetowej z kopii zapasowej przy użyciu programu Azure PowerShell

Ten przykładowy skrypt pobiera poprzednio utworzoną kopię zapasową z istniejącej aplikacji internetowej i przywraca ją przez zastąpienie jej zawartości. 

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Jeśli aplikacja internetowa nie jest już potrzebna, użyj następującego polecenia, aby usunąć grupę zasobów, aplikację internetową i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Pobiera listę kopii zapasowych dla aplikacji internetowej. |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Przywraca aplikację internetową z utworzonej wcześniej kopii zapasowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
