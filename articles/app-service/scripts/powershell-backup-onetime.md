---
title: 'PowerShell: łącze kopii zapasowej aplikacji'
description: Dowiedz się, jak za pomocą programu Azure PowerShell zautomatyzować wdrażanie usługi App Service i zarządzanie nimi. W tym przykładzie pokazano, jak zrobić z powrotem do kopii zapasowej aplikacji.
author: msangapu-msft
tags: azure-service-management
ms.assetid: fc755f82-ca3e-4532-b251-690b699324d6
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: c17cfcd2e55b98be9d86a73f6388d921336f993b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80057659"
---
# <a name="back-up-a-web-app-using-powershell"></a>Tworzenie kopii zapasowej aplikacji internetowej przy użyciu programu PowerShell

Ten przykładowy skrypt tworzy aplikację internetową w usłudze App Service z jej powiązanymi zasobami, a następnie tworzy jednorazową kopię zapasową na potrzeby tej aplikacji. 

W razie potrzeby zainstaluj program Azure PowerShell przy użyciu instrukcji znalezionych `Connect-AzAccount` w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom w celu utworzenia połączenia z platformą Azure. 

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-onetime/backup-onetime.ps1?highlight=1-5 "Back up a web app")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, aplikację internetową i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Tworzy konto magazynu. |
| [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) | Tworzy kontener usługi Azure Storage. |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | Generuje token SAS dla kontenera usługi Azure Storage.  |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Tworzy plan usługi App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Tworzy aplikację internetową. |
| [New-AzWebAppBackup](/powershell/module/az.websites/new-azwebappbackup) | Tworzy kopię zapasową aplikacji internetowej. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Pobiera listę kopii zapasowych dla aplikacji internetowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
