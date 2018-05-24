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
ms.openlocfilehash: b398ff3b5f3e6fd2ea7d6e2544e7399646f4fa5c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
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
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_net) | Tworzy sieć wirtualną. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Tworzy podsieć w sieci wirtualnej. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/public-ip#az_network_public_ip_create) | Tworzy publiczny adres IP dla bramy aplikacji. |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/application-gateway#az_application_gateway_create) | Tworzy bramę aplikacji. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | Tworzy zestaw skalowania maszyn wirtualnych. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_show) | Pobiera publiczny adres IP bramy aplikacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/overview).

Więcej przykładowych skryptów interfejsu wiersza polecenia bramy aplikacji można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../cli-samples.md).
