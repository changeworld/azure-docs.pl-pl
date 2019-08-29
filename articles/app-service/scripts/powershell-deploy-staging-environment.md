---
title: Przykładowy skrypt programu Azure PowerShell — tworzenie aplikacji i wdrażanie kodu w miejscu przejściowym | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — tworzenie aplikacji internetowej i wdrażanie kodu w środowisku przejściowym
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 7d65a5977ff8db8d31fe7a46d4f99569a8c2270a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087615"
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Tworzenie aplikacji internetowej i wdrażanie kodu w środowisku przejściowym

Ten przykładowy skrypt tworzy aplikację internetową w usłudze App Service z dodatkowym miejscem wdrożenia o nazwie „staging”, a następnie wdraża przykładową aplikację w tym miejscu.

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

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
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Tworzy plan usługi App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Tworzy aplikację internetową. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Modyfikuje plan usługi App Service, aby zmienić jej warstwę cenową. |
| [New-AzWebAppSlot](/powershell/module/az.websites/new-azwebappslot) | Tworzy miejsce wdrożenia aplikacji internetowej. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modyfikuje zasób w grupie zasobów. |
| [Switch-AzWebAppSlot](/powershell/module/az.websites/switch-azwebappslot) | Zamienia miejsce wdrożenia aplikacji internetowej na środowisko produkcyjne. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
