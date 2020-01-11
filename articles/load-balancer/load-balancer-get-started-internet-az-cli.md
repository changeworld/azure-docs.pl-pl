---
title: Tworzenie modułu równoważenia obciążenia za pomocą Strefowo nadmiarowego frontonu — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć publiczną usługa Load Balancer w warstwie Standardowa ze strefowo nadmiarowym publicznym adresem IP przy użyciu interfejsu wiersza polecenia platformy Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: allensu
ms.openlocfilehash: b9e8874883b4155d38209da07590d5ce720d2335
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894540"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-cli"></a>Tworzenie standardowego modułu równoważenia obciążenia przy użyciu strefowo nadmiarowe frontonu przy użyciu wiersza polecenia platformy Azure

W tym artykule opisano proces tworzenia publicznego [standardowego modułu równoważenia obciążenia](https://aka.ms/azureloadbalancerstandard) z strefowo nadmiarowe frontonu przy użyciu adresu publicznego adresu IP standardowych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, upewnij się, że zainstalowano najnowszy [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i zalogować się do konta platformy Azure za pomocą [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
>  Obsługa strefy dostępności jest dostępna dla wybieranych zasobów platformy Azure i regionami i rodzinami rozmiarów maszyn wirtualnych. Aby uzyskać więcej informacji na temat rozpocząć pracę i które zasoby platformy Azure, regionów i rodzinami rozmiarów maszyn wirtualnych można wypróbować strefy dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą następującego polecenia:

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Tworzenie publicznego adresu IP standardowych

Tworzenie publicznego adresu IP standardowa przy użyciu następującego polecenia:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Utwórz moduł równoważenia obciążenia

Utwórz publiczny Load Balancer w warstwie standardowa przy użyciu standardowego publicznego adresu IP, który został utworzony w poprzednim kroku, używając następującego polecenia:

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Tworzenie sondy modułu równoważenia obciążenia na porcie 80

Tworzenie sondy kondycji modułu równoważenia obciążenia, używając następującego polecenia:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Utwórz regułę modułu równoważenia obciążenia dla portu 80

Utwórz regułę modułu równoważenia obciążenia, używając następującego polecenia:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md).



