---
title: 'PowerShell: Wdrażanie kodu z usługi GitHub'
description: Dowiedz się, jak używać Azure PowerShell do automatyzowania wdrażania App Service i zarządzania nimi. Ten przykład pokazuje, jak wdrożyć kod z usługi GitHub.
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 22b6a50b76c7d2e29f4705823db904e2c35f2717
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684846"
---
# <a name="create-a-web-app-and-deploy-code-from-github"></a>Tworzenie aplikacji internetowej i wdrażanie kodu z usługi GitHub

Ten przykładowy skrypt tworzy aplikację internetową w usłudze App Service wraz z jej powiązanymi zasobami, a następnie wdraża kod aplikacji internetowej z publicznego repozytorium GitHub (bez ciągłego wdrażania). Aby uzyskać więcej informacji na temat wdrożenia z usługi GitHub z ciągłym wdrażaniem, zobacz [Tworzenie aplikacji internetowej z ciągłym wdrażaniem z usługi GitHub](powershell-continuous-deployment-github.md).

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure. Oprócz tego potrzebujesz linku do repozytorium GitHub zawierającego kod aplikacji internetowej.

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github/deploy-github.ps1?highlight=1-2 "Create a web app and deploy code from GitHub")]

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
