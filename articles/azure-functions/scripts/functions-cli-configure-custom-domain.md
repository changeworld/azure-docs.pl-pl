---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — mapowanie domeny niestandardowej na aplikację funkcji | Microsoft Docs
description: Przykład skryptu interfejsu wiersza polecenia platformy Azure — mapowanie domeny niestandardowej na aplikację funkcji na platformie Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/01/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: d2efd0891cb0010aac6135ce190ad77667f78efd
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29843990"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mapowanie domeny niestandardowej na aplikację funkcji

Ten przykładowy skrypt służy do tworzenia aplikacji funkcji z powiązanymi zasobami, a następnie mapowania domeny `www.<yourdomain>` na tę aplikację. Aby wykonać mapowanie na domenę niestandardową, aplikacja funkcji musi zostać utworzona w ramach planu usługi App Service, a nie planu Zużycie. Usługa Azure Functions obsługuje mapowanie domeny niestandardowej tylko przy użyciu rekordu A.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Tworzy konto magazynu wymagane przez aplikację funkcji. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Tworzy plan usługi App Service wymagany do wykonania mapowania domeny niestandardowej. |
| [az functionapp create]() | Tworzy aplikację funkcji. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#az_appservice_web_config_hostname_add) | Mapuje domenę niestandardową na aplikację funkcji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Functions można znaleźć w [dokumentacji usługi Azure Functions]().
