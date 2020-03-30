---
title: Konfigurowanie zestawu skalowania maszyny wirtualnej przy pomocą istniejącego modułu równoważenia obciążenia platformy Azure — Azure PowerShell
description: Dowiedz się, jak skonfigurować zestaw skalowania maszyny wirtualnej przy pomocą istniejącego modułu równoważenia obciążenia platformy Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349990"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Konfigurowanie zestawu skalowania maszyny wirtualnej przy użyciu istniejącego modułu równoważenia obciążenia platformy Azure przy użyciu programu Azure PowerShell

W tym artykule dowiesz się, jak skonfigurować zestaw skalowania maszyny wirtualnej przy pomocą istniejącego modułu równoważenia obciążenia platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.
- Istniejący standardowy moduł równoważenia obciążenia sku w subskrypcji, w której zostanie wdrożony zestaw skalowania maszyny wirtualnej.
- Sieć wirtualna platformy Azure dla zestawu skalowania maszyny wirtualnej.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Logowanie do interfejsu wiersza polecenia platformy Azure

Zaloguj się na platformie Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Wdrażanie zestawu skalowania maszyny wirtualnej z istniejącym modułem równoważenia obciążenia

Zastąp wartości w nawiasach nazwami zasobów w konfiguracji.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

Poniższy przykład wdraża zestaw skalowania maszyny wirtualnej z:

- Zestaw skalowania maszyny wirtualnej o nazwie **myVMSS**
- Moduł równoważenia obciążenia platformy Azure o nazwie **myLoadBalancer**
- Pula wewnętrznej bazy danych modułu równoważenia obciążenia o nazwie **myBackendPool**
- Sieć wirtualna platformy Azure o nazwie **myVnet**
- Podsieć o nazwie **mySubnet**
- Grupa zasobów o nazwie **myResourceGroup**

```azureppowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> Po utworzeniu zestawu skalowania nie można zmodyfikować portu wewnętrznej bazy danych dla reguły równoważenia obciążenia używanej przez sondę kondycji modułu równoważenia obciążenia. Aby zmienić port, można usunąć sondę kondycji, aktualizując zestaw skalowania maszyny wirtualnej platformy Azure, zaktualizować port, a następnie ponownie skonfigurować sondę kondycji.

## <a name="next-steps"></a>Następne kroki

W tym artykule wdrożono zestaw skalowania maszyny wirtualnej z istniejącym modułem równoważenia obciążenia platformy Azure.  Aby dowiedzieć się więcej o zestawach skalowania maszyn wirtualnych i równoważącym obciążenia, zobacz:

- [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)
- [Co to są zestawy skalowania maszyn wirtualnych?](../virtual-machine-scale-sets/overview.md)
                                