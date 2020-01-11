---
title: Tworzenie modułu równoważenia obciążenia za pomocą Strefowo nadmiarowego frontonu Azure PowerShell
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć publiczną usługa Load Balancer w warstwie Standardowa ze strefowo nadmiarowym publicznym adresem IP przy użyciu programu PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 2b8797055b308720d30b8de0bb90fad88c64cbfb
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894470"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-powershell"></a>Tworzenie standardowego modułu równoważenia obciążenia przy użyciu strefowo nadmiarowe frontonu przy użyciu programu Azure PowerShell

W tym artykule opisano sposób tworzenia publicznej [Usługa Load Balancer w warstwie Standardowa](https://aka.ms/azureloadbalancerstandard) ze strefowo nadmiarowym adresem IP przy użyciu adresu w standardzie Public.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
>  Obsługa strefy dostępności jest dostępna dla wybieranych zasobów platformy Azure i regionami i rodzinami rozmiarów maszyn wirtualnych. Aby uzyskać więcej informacji na temat rozpocząć pracę i które zasoby platformy Azure, regionów i rodzinami rozmiarów maszyn wirtualnych można wypróbować strefy dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą następującego polecenia:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Tworzenie publicznego adresu IP standardowych 
Tworzenie publicznego adresu IP standardowa przy użyciu następującego polecenia:

```azurepowershell-interactive
$publicIp = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Utwórz konfigurację adresu IP frontonu dla witryny sieci Web

Utwórz konfigurację adresu IP frontonu, używając następującego polecenia:

```azurepowershell-interactive
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Tworzenie puli adresów zaplecza

Utwórz pulę adresów zaplecza za pomocą następującego polecenia:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Tworzenie sondy modułu równoważenia obciążenia na porcie 80

Utwórz sondę kondycji na porcie 80 dla modułu równoważenia obciążenia, używając następującego polecenia:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia
 Utwórz regułę modułu równoważenia obciążenia, używając następującego polecenia:

```azurepowershell-interactive
   $rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Utwórz moduł równoważenia obciążenia
Utwórz usługa Load Balancer w warstwie Standardowa przy użyciu następującego polecenia:

```azurepowershell-interactive
$lb = New-AzLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md).