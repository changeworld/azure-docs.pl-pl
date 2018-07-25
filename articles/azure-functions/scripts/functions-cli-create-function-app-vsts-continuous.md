---
title: Tworzenie funkcji wdrażanej z repozytorium usługi Visual Studio Team Services na platformie Azure | Microsoft Docs
description: Tworzenie aplikacji funkcji i wdrażanie kodu funkcji z repozytorium usługi Visual Studio Team Services
services: functions
keywords: ''
author: syntaxc4
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 1b54cfebd3ae36fc8025aeb4ea9c91d336bc5343
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988956"
---
# <a name="create-a-function-app-and-deploy-function-code-from-visual-studio-team-services"></a>Tworzenie aplikacji funkcji i wdrażanie kodu funkcji z repozytorium usługi Visual Studio Team Services

W tym temacie przedstawiono instrukcje tworzenia [bezserwerowej](https://azure.microsoft.com/overview/serverless-computing/) aplikacji funkcji korzystającej z [planu Zużycie](../functions-scale.md#consumption-plan) w usłudze Azure Functions. Aplikacja funkcji, będąca kontenerem dla funkcji, będzie wdrażana w sposób ciągły z repozytorium usługi Visual Studio Team Services (VSTS). 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

Do wykonania instrukcji w tym temacie potrzebne są:

* Repozytorium usługi VSTS zawierające projekt aplikacji funkcji, w którym masz uprawnienia administracyjne.
* [Osobisty token dostępu](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) do repozytorium usługi VSTS.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli wolisz użyć interfejsu wiersza polecenia platformy Azure lokalnie, zainstaluj i uruchom wersję 2.0 lub nowszą. Aby określić wersję interfejsu wiersza polecenia platformy Azure, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

Ten przykładowy skrypt umożliwia utworzenie aplikacji funkcji platformy Azure i wdrożenie kodu funkcji z repozytorium usługi Visual Studio Team Services.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia, służące do utworzenia grupy zasobów, konta magazynu, aplikacji funkcji i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Tworzy konto magazynu wymagane przez aplikację funkcji. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Tworzy aplikację funkcji w bezserwerowym [planie Zużycie](../functions-scale.md#consumption-plan). |
| [az functionapp deployment source config](https://docs.microsoft.com/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Kojarzy aplikację funkcji z repozytorium Git lub Mercurial. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).
