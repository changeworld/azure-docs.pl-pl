---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — mapowanie domeny niestandardowej na aplikację funkcji | Microsoft Docs
description: Przykład skryptu interfejsu wiersza polecenia platformy Azure — mapowanie domeny niestandardowej na aplikację funkcji na platformie Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 19fd73dff045d6d784dd70d1f3ad539a22faf413
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075465"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mapowanie domeny niestandardowej na aplikację funkcji

Ten przykładowy skrypt tworzy aplikację funkcji w ramach planu usługi App Service, a następnie mapuje ją na podaną przez Ciebie domenę niestandardową. Gdy aplikacja funkcji znajduje się w [plan w warstwie Premium](../functions-scale.md#premium-plan) lub [planu usługi App Service](../functions-scale.md#app-service-plan), można mapować domenę niestandardową przy użyciu rekordu A lub CNAME. Dla aplikacji funkcji w [planie Zużycie](../functions-scale.md#consumption-plan) obsługiwana jest tylko opcja CNAME. Ten przykład tworzy plan usługi App Service i wymaga rekordu A, aby mapować domenę. 

Aby uruchomić ten przykładowy skrypt, w domenie niestandardowej musisz już mieć skonfigurowany rekord A, który wskazuje domyślną nazwę domeny aplikacji internetowej. Aby uzyskać więcej informacji, zobacz [Instrukcje mapowania domeny niestandardowej dla usługi Azure App Service](https://aka.ms/appservicecustomdns). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Tworzy konto magazynu wymagane przez aplikację funkcji. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Tworzy plan usługi App Service wymagany do wykonania mapowania domeny niestandardowej. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Tworzy aplikację funkcji w planie usługi App Service. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Mapuje domenę niestandardową na aplikację funkcji. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).
