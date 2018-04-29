---
title: Utwórz publiczny obciążenia standardowego równoważenia z zonal frontonu adres publiczny adres IP za pomocą programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć publiczny standardowe usługi równoważenia obciążenia z zonal frontonu adres publiczny adres IP za pomocą programu Azure PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: dbb4176ac61cf707b28cddc98db80a1188be3cc8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-frontend-using-azure-powershell"></a>Utwórz publiczny obciążenia standardowego równoważenia z zonal frontonu przy użyciu programu Azure PowerShell

W tym artykule opisano przez proces tworzenia publiczny [standardowe usługi równoważenia obciążenia](https://aka.ms/azureloadbalancerstandard) z zonal frontonu przy użyciu adresu publicznego adresu IP standardowa. Aby zrozumieć, jak działają stref dostępności z modułem równoważenia obciążenia standardowego, zobacz [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md). 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Obsługa stref dostępności jest dostępna dla wybierz zasobów platformy Azure i regiony i rodziny rozmiar maszyny Wirtualnej. Więcej informacji na temat rozpocząć, które zasobów platformy Azure, regiony i rodziny rozmiar maszyny Wirtualnej można spróbować stref dostępności przy, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów, przy użyciu następującego polecenia:

```powershell
New-AzureRmResourceGroup -Name myResourceGroupZLB -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Utwórz publiczny Standard IP 
Tworzenie publicznego adresu IP standardowe przy użyciu następującego polecenia:

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupZLB -Name 'myPublicIPZonal' `
  -Location westeurope -AllocationMethod Static -Sku Standard -zone 1
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Utwórz konfigurację IP frontonu witryny sieci Web

Tworzenie konfiguracji IP frontonu przy użyciu następującego polecenia:

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Tworzenie puli adresów zaplecza

Utwórz pulę adresów zaplecza za pomocą następującego polecenia:

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Utwórz sondę modułu równoważenia obciążenia na porcie 80

Utwórz sondy kondycji na porcie 80 dla usługi równoważenia obciążenia za pomocą następującego polecenia:

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia
 Utwórz regułę modułu równoważenia obciążenia za pomocą następującego polecenia:

```powershell
   $rule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia
Utwórz obciążenia równoważenia standardowe przy użyciu następującego polecenia:

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroupZLB -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md).



