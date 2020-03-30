---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — kierowanie ruchu w celu zapewnienia wysokiej dostępności aplikacji | Dokumenty firmy Microsoft
description: Przykład skryptu interfejsu wiersza polecenia platformy Azure — kierowanie ruchu w celu zapewnienia wysokiej dostępności aplikacji
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: allensu
ms.openlocfilehash: 2a083520a0da324d7033d4597a6b734b43c025b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74049256"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-cli"></a>Ruch trasy dla wysokiej dostępności aplikacji — Azure CLI

Ten skrypt tworzy grupę zasobów, dwa plany usługi aplikacji, dwie aplikacje sieci web, profil menedżera ruchu i dwa punkty końcowe menedżera ruchu. Usługa Traffic Manager kieruje ruch do aplikacji w jednym regionie jako regionu podstawowego i do regionu pomocniczego, gdy aplikacja w regionie podstawowym jest niedostępna. Przed wykonaniem skryptu należy zmienić wartości MyWebApp, MyWebAppL1 i MyWebAppL2 na unikatowe wartości na platformie Azure. Po uruchomieniu skryptu można uzyskać dostęp do aplikacji w regionie podstawowym za pomocą adresu URL mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po uruchomieniu przykładu skryptu polecenia follow można użyć do usunięcia grupy zasobów, aplikacji usługi App Service i wszystkich powiązanych zasobów.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń do utworzenia grupy zasobów, aplikacji internetowej, profilu usługi Traffic Manager i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Tworzy plan usługi App Service. Jest to jak farma serwerów dla aplikacji sieci web platformy Azure. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Tworzy aplikację sieci web platformy Azure w ramach planu usługi app service. |
| [az network traffic-manager profil create az network traffic-manager create az network traffic-manager create az network traffic](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Tworzy profil usługi Azure Traffic Manager. |
| [az sieć traffic-manager punkt końcowy tworzenia](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Dodaje punkt końcowy do profilu usługi Azure Traffic Manager. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi app service można znaleźć w [dokumentacji usługi Azure Networking](../cli-samples.md).
