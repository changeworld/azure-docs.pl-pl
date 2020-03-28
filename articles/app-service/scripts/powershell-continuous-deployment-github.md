---
title: 'Program PowerShell: Wdrażanie w sposób ciągły z usługi GitHub'
description: Dowiedz się, jak za pomocą programu Azure PowerShell zautomatyzować wdrażanie usługi App Service i zarządzanie nimi. W tym przykładzie pokazano, jak utworzyć aplikację z ciągłą integracją/dyskiem CD z usługi GitHub.
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: eee6ac9f9c469f9e1a9344ab4a30626c219d7836
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74685166"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Tworzenie aplikacji internetowej z ciągłym wdrażaniem z repozytorium GitHub

Ten przykładowy skrypt tworzy aplikację sieci web w usłudze App Service z powiązanymi zasobami, a następnie konfiguruje [ciągłe wdrażanie](../deploy-continuous-deployment.md) z repozytorium Usługi GitHub. Aby uzyskać więcej informacji na temat wdrożenia z repozytorium GitHub bez ciągłego wdrażania, zobacz [Create a web app and deploy code from GitHub (Tworzenie aplikacji internetowej i wdrażanie kodu z repozytorium GitHub)](powershell-deploy-github.md).

W razie potrzeby zainstaluj program Azure PowerShell przy użyciu instrukcji znalezionych `Connect-AzAccount` w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom w celu utworzenia połączenia z platformą Azure. Ponadto upewnij się, że:

- Kod aplikacji znajduje się w publicznym lub prywatnym repozytorium GitHub, którego jesteś właścicielem. Aby uzyskać automatyczne kompilacje, ustrukturyzuj repozytorium zgodnie z tabelą [Przygotuj repozytorium.](../deploy-continuous-deployment.md#prepare-your-repository)
- [Utworzono osobisty token dostępu na swoim koncie GitHub](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line).

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
