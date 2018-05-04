---
title: Azure CLI przykładowym skrypcie - kierować ruchem wysoką dostępność aplikacji | Dokumentacja firmy Microsoft
description: Azure CLI przykładowym skrypcie - kierować ruchem wysoką dostępność aplikacji
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: tysonn
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: kumud
ms.openlocfilehash: 82264e51fcd64615a41f5fa652c4ad58ad9dabfb
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Kierować ruchem wysoką dostępność aplikacji przy użyciu wiersza polecenia platformy Azure

Ten skrypt tworzy grupę zasobów, dwa planów usługi aplikacji, dwie aplikacje sieci web, profilu Menedżera ruchu i dwa punkty końcowe Menedżera ruchu. Menedżer ruchu kieruje ruch do aplikacji w jednym regionie jako regionu podstawowego, a w regionie pomocniczym, gdy aplikacja w regionie podstawowym jest niedostępny. Przed wykonaniem skryptu, należy zmienić wartości MyWebApp, MyWebAppL1 i MyWebAppL2 unikatowych wartości na platformie Azure. Po uruchomieniu skryptu, można uzyskać dostępu do aplikacji w regionie podstawowym z mywebapp.trafficmanager.net adresu URL.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po uruchomieniu przykładowym skrypcie, wykonaj polecenie może służyć do usunięcia grupy zasobów, aplikacji usługi app Service i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń do utworzenia grupy zasobów, aplikacji internetowej, profilu usługi Traffic Manager i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Tworzy plan usługi App Service. Przypomina farmy serwerów aplikacji sieci web platformy Azure. |
| [Utwórz az appservice w sieci web](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_create) | Tworzy aplikację sieci web platformy Azure w ramach planu usługi aplikacji. |
| [Utwórz profil Menedżera ruchu sieciowego az](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Tworzy profil usługi Azure Traffic Manager. |
| [Tworzenie punktu końcowego Menedżera ruchu sieciowego az](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) | Dodaje punkt końcowy profilu Menedżera ruchu Azure. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów aplikacji usługi interfejsu wiersza polecenia można znaleźć w [Azure Networking dokumentacji](../cli-samples.md).
