---
title: Używanie grup umieszczania w sąsiedztwie dla maszyn wirtualnych z systemem Windows | Microsoft Docs
description: Dowiedz się więcej na temat tworzenia i używania grup umieszczania sąsiedztwa dla maszyn wirtualnych z systemem Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 9e9b8dddf7f48fe672a37b0343d215e0735ed760
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302497"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-powershell"></a>Wersja zapoznawcza: Wdrażanie maszyn wirtualnych w grupach umieszczania sąsiedztwa przy użyciu programu PowerShell


Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w obrębie [grupy umieszczania sąsiedztwa](co-location.md#preview-proximity-placement-groups).

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.

> [!IMPORTANT]
> Grupy umieszczania w sąsiedztwie są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Grupy umieszczania w sąsiedztwie nie są dostępne w tych regionach w ramach wersji zapoznawczej: **Japonia Wschodnia**, **Australia Wschodnia** i **Indie Środkowe**.


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

Utwórz maszynę wirtualną w grupie położenia sąsiedztwa `-ProximityPlacementGroup $ppg.Id` przy użyciu polecenia, aby odwołać się do identyfikatora grupy umieszczania bliskości podczas tworzenia maszyny wirtualnej przy użyciu polecenia [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) .

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
