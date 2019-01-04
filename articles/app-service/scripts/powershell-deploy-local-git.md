---
title: Przykładowy skrypt programu PowerShell — tworzenie aplikacji i wdrażanie z lokalnego repozytorium Git | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium Git
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: dcba93561f1f3488c8849ea419bd94765102f849
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53586808"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium Git

Ten przykładowy skrypt służy do tworzenia aplikacji internetowej w usłudze App Service wraz z jej powiązanymi zasobami, a następnie wdrażania kodu aplikacji internetowej z lokalnego repozytorium Git.

W razie potrzeby zaktualizuj program Azure PowerShell do najnowszej wersji, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. Ponadto kod aplikacji musi zostać zatwierdzony w lokalnym repozytorium Git.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, aplikację internetową i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Tworzy aplikację internetową z niezbędną grupą zasobów i grupą usługi App Service. Jeśli bieżący katalog zawiera repozytorium Git, dodaj również zdalny element `azure`. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
