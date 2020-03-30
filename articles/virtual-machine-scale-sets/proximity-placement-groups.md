---
title: Podgląd grup miejsc docelowych zbliżeniowych dla zestawów skalowania maszyny wirtualnej
description: Dowiedz się więcej o tworzeniu i używaniu grup miejsc docelowych zbliżeniowych dla zestawów skalowania maszyn wirtualnych systemu Windows na platformie Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273619"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Podgląd: tworzenie i używanie grup miejsc docelowych zbliżeniowych przy użyciu programu PowerShell

Aby maszyny wirtualne były jak najbliżej, osiągając możliwie najniższe opóźnienie, należy wdrożyć zestaw skalowania w [grupie miejsc docelowych zbliżeniowo.](co-location.md#preview-proximity-placement-groups)

Grupa miejsc docelowych zbliżeniowych jest logicznym grupowaniem używanym w celu upewnienia się, że zasoby obliczeniowe platformy Azure są fizycznie zlokalizowane blisko siebie. Grupy miejsc docelowych zbliżeniowych są przydatne w przypadku obciążeń, w których wymagane jest małe opóźnienia.

> [!IMPORTANT]
> Grupy miejsc docelowych zbliżeniowych są obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Grupy miejsc docelowych zbliżeniowych nie są dostępne w tych regionach podczas podglądu: **Japonia wschodnia,** **Australia Wschodnia** i **Indie Środkowe.**


## <a name="create-a-proximity-placement-group"></a>Tworzenie grupy umieszczania w pobliżu
Utwórz grupę miejsc docelowych zbliżeniowych przy użyciu polecenia cmdlet [New-AzProximityPlacementGroup.](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Wyświetlanie listy grup miejsc docelowych zbliżeniowych

Za pomocą polecenia cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) można wyświetlić listę wszystkich grup miejsc docelowych zbliżeniowych.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Utwórz skalę w grupie `-ProximityPlacementGroup $ppg.Id` miejsc docelowych zbliżeniami, używając odwoływania się do identyfikatora grupy miejsc docelowych zbliżeniowych podczas tworzenia zestawu skalowania za pomocą [programu New-AzVMSS.](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss)

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

Wystąpienie w grupie miejsc docelowych można wyświetlić za pomocą [grupy Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Następne kroki

Można również użyć [interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/proximity-placement-groups.md) do tworzenia grup miejsc docelowych zbliżeniowych.
