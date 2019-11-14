---
title: Tworzenie modułu równoważenia obciążenia z strefą frontonu — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć usługa Load Balancer w warstwie Standardowa z strefą frontonu przy użyciu interfejsu wiersza polecenia platformy Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 620e4456e86a8610819495bd490353a3a5681738
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076085"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>Tworzenie usługa Load Balancer w warstwie Standardowa z strefą frontonu przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób tworzenia [Usługa Load Balancer w warstwie Standardowa](https://aka.ms/azureloadbalancerstandard) publicznego z strefą frontonu. Istnienie strefowych frontonu oznacza, że każdy przepływ ruchu przychodzącego lub wychodzącego jest obsługiwany przez jedną strefę w regionie. Moduł równoważenia obciążenia można utworzyć z strefą frontonu przy użyciu standardowego publicznego adresu IP w ramach stref w konfiguracji frontonu. Aby dowiedzieć się, jak działają strefach dostępności przy użyciu standardowego modułu równoważenia obciążenia, zobacz [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md). 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, upewnij się, że zainstalowano najnowszy [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i zalogować się do konta platformy Azure za pomocą [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
> Obsługa strefy dostępności jest dostępna dla wybieranych zasobów platformy Azure i regionami i rodzinami rozmiarów maszyn wirtualnych. Aby uzyskać więcej informacji na temat rozpocząć pracę i które zasoby platformy Azure, regionów i rodzinami rozmiarów maszyn wirtualnych można wypróbować strefy dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą następującego polecenia:

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>Tworzenie publicznego adresu IP w warstwie Standardowa

Utwórz strefowy publiczny adres IP w warstwie Standardowa przy użyciu następującego polecenia:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz publiczną usługa Load Balancer w warstwie Standardowa ze standardowym publicznym adresem IP utworzonym w poprzednim kroku przy użyciu następującego polecenia:

```azurecli-interactive
az network lb create --resource-group myResourceGroupZLB --name myLoadBalancer --public-ip-address myPublicIPZonal --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Tworzenie sondy modułu równoważenia obciążenia na porcie 80

Tworzenie sondy kondycji modułu równoważenia obciążenia, używając następującego polecenia:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupZLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Utwórz regułę modułu równoważenia obciążenia dla portu 80

Utwórz regułę modułu równoważenia obciążenia, używając następującego polecenia:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupZLB --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md).



