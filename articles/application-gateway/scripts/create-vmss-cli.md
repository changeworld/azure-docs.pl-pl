---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — zarządzanie ruchem internetowym | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — zarządzanie ruchem internetowym za pomocą bramy aplikacji i zestawu skalowania maszyn wirtualnych.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 638aa3bf0e278ce00c124d8217a1bf9104e878fb
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413787"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Zarządzanie ruchem internetowym przy użyciu interfejsu wiersza polecenia platformy Azure

Ten skrypt tworzy bramę aplikacji, która używa zestawu skalowania maszyn wirtualnych dla serwerów zaplecza. Bramę aplikacji można następnie skonfigurować do zarządzania ruchem internetowym. Po uruchomieniu skryptu można przetestować bramę aplikacji, korzystając z jej publicznego adresu IP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, bramę aplikacji i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-net) | Tworzy sieć wirtualną. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network_vnet_subnet_create) | Tworzy podsieć w sieci wirtualnej. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest) | Tworzy publiczny adres IP dla bramy aplikacji. |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest) | Tworzy bramę aplikacji. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az-vmss-create) | Tworzy zestaw skalowania maszyn wirtualnych. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip) | Pobiera publiczny adres IP bramy aplikacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/overview).

Więcej przykładowych skryptów interfejsu wiersza polecenia bramy aplikacji można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../cli-samples.md).
