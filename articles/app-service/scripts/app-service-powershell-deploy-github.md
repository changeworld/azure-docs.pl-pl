---
title: Przykładowy skrypt programu Azure PowerShell — tworzenie aplikacji internetowej i wdrażanie kodu z usługi GitHub | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — tworzenie aplikacji internetowej i wdrażanie kodu z usługi GitHub
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: d60503e195c1a8f32992cfb1978ad7a181c069b0
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39323946"
---
# <a name="create-a-web-app-and-deploy-code-from-github"></a>Tworzenie aplikacji internetowej i wdrażanie kodu z usługi GitHub

Ten przykładowy skrypt tworzy aplikację internetową w usłudze App Service wraz z jej powiązanymi zasobami, a następnie wdraża kod aplikacji internetowej z publicznego repozytorium GitHub (bez ciągłego wdrażania). Aby uzyskać więcej informacji na temat wdrożenia z usługi GitHub z ciągłym wdrażaniem, zobacz [Tworzenie aplikacji internetowej z ciągłym wdrażaniem z usługi GitHub](app-service-powershell-continuous-deployment-github.md).

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. Oprócz tego potrzebujesz linku do repozytorium GitHub zawierającego kod aplikacji internetowej.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github/deploy-github.ps1?highlight=1-2 "Create a web app and deploy code from GitHub")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, aplikację internetową i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Tworzy plan usługi App Service. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Tworzy aplikację internetową. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Modyfikuje zasób w grupie zasobów. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../app-service-powershell-samples.md).
