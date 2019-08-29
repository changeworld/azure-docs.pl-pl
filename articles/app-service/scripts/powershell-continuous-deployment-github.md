---
title: Przykładowy skrypt programu Azure PowerShell — tworzenie aplikacji z ciągłym wdrażaniem usługi GitHub | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — tworzenie aplikacji internetowej z ciągłym wdrażaniem z repozytorium GitHub
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: bc8be33ede80070b8e9928e01d07b6066f1c887c
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113574"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Tworzenie aplikacji internetowej z ciągłym wdrażaniem z repozytorium GitHub

Ten przykładowy skrypt tworzy aplikację internetową w App Service z jej powiązanymi zasobami, a następnie konfiguruje [ciągłe wdrażanie](../deploy-continuous-deployment.md) z repozytorium GitHub. Aby uzyskać więcej informacji na temat wdrożenia z repozytorium GitHub bez ciągłego wdrażania, zobacz [Create a web app and deploy code from GitHub (Tworzenie aplikacji internetowej i wdrażanie kodu z repozytorium GitHub)](powershell-deploy-github.md).

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure. Ponadto upewnij się, że:

- Kod aplikacji znajduje się w publicznym lub prywatnym repozytorium GitHub, którego jesteś właścicielem. Aby uzyskać kompilacje automatyczne, należy przeprowadzić strukturę repozytorium zgodnie z tabelą [Przygotowanie repozytorium](../deploy-continuous-deployment.md#prepare-your-repository) .
- [W Twoim koncie usługi GitHub został utworzony osobisty token dostępu](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line).

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

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
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modyfikuje zasób w grupie zasobów. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
