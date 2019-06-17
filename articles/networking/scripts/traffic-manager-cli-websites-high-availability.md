---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kierowanie ruchu w celu zapewnienia wysokiej dostępności aplikacji | Dokumentacja firmy Microsoft
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kierowanie ruchu w celu zapewnienia wysokiej dostępności aplikacji
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: tysonn
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: kumud
ms.openlocfilehash: 3922eb76fa0954b9c02cc86f98acb142cc1d1fee
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565313"
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Kierowanie ruchem w celu zapewnienia wysokiej dostępności aplikacji

Ten skrypt tworzy grupę zasobów, dwóch planów usługi app service, dwie aplikacje internetowe, profilu usługi traffic manager i dwa punkty końcowe Menedżer ruchu. Usługa Traffic Manager kieruje ruch do aplikacji w jednym regionie w postaci regionu podstawowego, a także do regionu pomocniczego, gdy aplikacji w regionie głównym jest niedostępne. Przed wykonaniem skryptu, możesz zmienić wartości MyWebApp, MyWebAppL1 i MyWebAppL2 unikatowych wartości na platformie Azure. Po uruchomieniu skryptu dostęp do aplikacji w regionie podstawowym z mywebapp.trafficmanager.net adresu URL.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu polecenia wykonaj można usunąć grupę zasobów, aplikacji usługi app Service i wszystkie pokrewne zasoby.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń do utworzenia grupy zasobów, aplikacji internetowej, profilu usługi Traffic Manager i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Tworzy plan usługi App Service. Przypomina farmę serwerów dla aplikacji sieci web platformy Azure. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Tworzy aplikację internetową platformy Azure w ramach planu usługi App Service. |
| [Tworzenie profilu usługi traffic manager sieci az](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Tworzy profil usługi Azure Traffic Manager. |
| [Tworzenie punktu końcowego usługi traffic manager sieci az](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Dodaje punkt końcowy do profilu usługi Azure Traffic Manager. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia aplikacji usługi można znaleźć w [dokumentacji sieci platformy Azure](../cli-samples.md).
