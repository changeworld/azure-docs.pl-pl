---
title: Przykładowy skrypt programu Azure PowerShell — usuwanie kopii zapasowej aplikacji internetowej | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — usuwanie kopii zapasowej aplikacji internetowej
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d8a3d8f890001138f0582a099021b4537227a337
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53586409"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>Usuwanie kopii zapasowej aplikacji internetowej przy użyciu programu Azure PowerShell

Ten przykładowy skrypt tworzy aplikację internetową w usłudze App Service z jej powiązanymi zasobami, a następnie tworzy jednorazową kopię zapasową na potrzeby tej aplikacji. 

Aby uruchomić ten skrypt, należy posiadać istniejącą kopię zapasową dla aplikacji internetowej. Aby ją utworzyć, zobacz [Tworzenie kopii zapasowej aplikacji internetowej](powershell-backup-onetime.md) lub [Tworzenie zaplanowanej kopii zapasowej dla aplikacji internetowej](powershell-backup-scheduled.md).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, aplikację internetową i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Pobiera listę kopii zapasowych dla aplikacji internetowej. |
| [Remove-AzureRmWebAppBackup](/powershell/module/azurerm.websites/remove-azurermwebappbackup) | Usuwa określoną kopię zapasową aplikacji internetowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
