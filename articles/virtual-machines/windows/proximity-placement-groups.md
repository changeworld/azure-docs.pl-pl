---
title: Używanie grup umieszczania w sąsiedztwie dla maszyn wirtualnych z systemem Windows
description: Dowiedz się więcej na temat tworzenia i używania grup umieszczania sąsiedztwa dla maszyn wirtualnych z systemem Windows na platformie Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d0c35737151b060dcffba8944f4a1361d36dc14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171206"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Wdrażanie maszyn wirtualnych w grupach umieszczania sąsiedztwa przy użyciu programu PowerShell


Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w obrębie [grupy umieszczania sąsiedztwa](co-location.md#proximity-placement-groups).

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.


## <a name="create-a-proximity-placement-group"></a>Tworzenie grupy umieszczania w pobliżu
Utwórz grupę umieszczania sąsiedztwa przy użyciu polecenia cmdlet [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) . 

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

## <a name="list-proximity-placement-groups"></a>Wyświetl listę grup umieszczania zbliżeniowe

Wszystkie grupy położenia zbliżeniowe można wyświetlić za pomocą polecenia cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) .

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną w grupie położenia zbliżeniowe przy użyciu `-ProximityPlacementGroup $ppg.Id`, aby odwołać się do identyfikatora grupy umieszczania bliskości w przypadku tworzenia maszyny wirtualnej przy użyciu polecenia [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) .

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Maszynę wirtualną można zobaczyć w grupie umieszczania za pomocą polecenia [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

## <a name="availability-sets"></a>Zestawy dostępności
Zestaw dostępności można również utworzyć w grupie umieszczania sąsiedztwa. Użyj tego samego `-ProximityPlacementGroup` parametru z poleceniem cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) , aby utworzyć zestaw dostępności, a wszystkie maszyny wirtualne utworzone w zestawie dostępności również zostaną utworzone w tej samej grupie umieszczania sąsiedztwa.

## <a name="scale-sets"></a>Zestawy skalowania

Zestaw skalowania można również utworzyć w grupie umieszczania sąsiedztwa. Użyj tego samego `-ProximityPlacementGroup` parametru z parametrem [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) , aby utworzyć zestaw skalowania, a wszystkie wystąpienia zostaną utworzone w tej samej grupie umieszczania sąsiedztwa.

## <a name="next-steps"></a>Następne kroki

Za pomocą [interfejsu wiersza polecenia platformy Azure](../linux/proximity-placement-groups.md) można także tworzyć grupy umieszczania sąsiedztwa.
