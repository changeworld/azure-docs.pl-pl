---
title: Przykładowy skrypt programu PowerShell Azure - usuwanie kopii zapasowej dla aplikacji sieci web | Dokumentacja firmy Microsoft
description: Przykładowy skrypt programu PowerShell Azure - usuwanie kopii zapasowej dla aplikacji sieci web
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f4204cbb4aefe590b87d0a72675823321f280f33
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2017
ms.locfileid: "23941747"
---
# <a name="delete-a-backup-for-a-web-app"></a>Usuwanie kopii zapasowej dla aplikacji sieci web

Ten przykładowy skrypt tworzy aplikację sieci web w usłudze App Service z powiązane zasoby, a następnie tworzy jednorazowej kopii zapasowej dla niego. 

Aby uruchomić ten skrypt, musisz mieć istniejącej kopii zapasowej dla aplikacji sieci web. Aby go utworzyć, zobacz [kopii zapasowej zapasowej aplikacji sieci web](app-service-powershell-backup-onetime.md) lub [utworzyć zaplanowaną kopię zapasową dla aplikacji sieci web](app-service-powershell-backup-scheduled.md).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po uruchomieniu przykładowym skrypcie następującego polecenia można usunąć grupy zasobów, aplikacji sieci web i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujących poleceń. Każde polecenie w tabeli łącza do dokumentacji określonego polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Pobiera listę kopii zapasowych dla aplikacji sieci web. |
| [Usuń AzureRmWebAppBackup](/powershell/module/azurerm.websites/remove-azurermwebappbackup) | Usuwa z określonej kopii zapasowej aplikacji sieci web. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących modułu Azure PowerShell, zobacz [dokumentacji programu Azure PowerShell](/powershell/azure/overview).

Dodatkowe przykłady programu Powershell systemu Azure dla aplikacji sieci Web usługi aplikacji Azure można znaleźć w [przykłady programu Azure PowerShell](../app-service-powershell-samples.md).
