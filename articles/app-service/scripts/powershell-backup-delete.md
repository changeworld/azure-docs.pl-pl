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
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 9b9a6ecc93489c6690e0128ef9129c480f506b31
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098423"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>Usuwanie kopii zapasowej aplikacji internetowej przy użyciu programu Azure PowerShell

Ten przykładowy skrypt tworzy aplikację internetową w usłudze App Service z jej powiązanymi zasobami, a następnie tworzy jednorazową kopię zapasową na potrzeby tej aplikacji. 

Aby uruchomić ten skrypt, należy posiadać istniejącą kopię zapasową dla aplikacji internetowej. Aby ją utworzyć, zobacz [Tworzenie kopii zapasowej aplikacji internetowej](powershell-backup-onetime.md) lub [Tworzenie zaplanowanej kopii zapasowej dla aplikacji internetowej](powershell-backup-scheduled.md).

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, aplikację internetową i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Pobiera listę kopii zapasowych dla aplikacji internetowej. |
| [Remove-AzWebAppBackup](/powershell/module/az.websites/remove-azwebappbackup) | Usuwa określoną kopię zapasową aplikacji internetowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
