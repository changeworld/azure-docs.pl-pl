---
title: 'PowerShell: użyj grup miejsc docelowych zbliżeniowych'
description: Dowiedz się więcej o tworzeniu i używaniu grup miejsc docelowych zbliżeniowych przy użyciu programu Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208529"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Wdrażanie maszyn wirtualnych w grupach miejsc docelowych zbliżeniowych przy użyciu programu PowerShell


Aby maszyny wirtualne były jak najbliżej, osiągając możliwie najniższe opóźnienie, należy wdrożyć je w [grupie miejsc docelowych w pobliżu](co-location.md#proximity-placement-groups).

Grupa miejsc docelowych zbliżeniowych jest logicznym grupowaniem używanym w celu upewnienia się, że zasoby obliczeniowe platformy Azure są fizycznie zlokalizowane blisko siebie. Grupy miejsc docelowych zbliżeniowych są przydatne w przypadku obciążeń, w których wymagane jest małe opóźnienia.


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


## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną w `-ProximityPlacementGroup $ppg.Id` grupie miejsc docelowych zbliżeniami, używając odwoływania się do identyfikatora grupy miejsc docelowych zbliżeniowych podczas tworzenia maszyny wirtualnej za pomocą [funkcji New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Maszynę wirtualną można wyświetlić w grupie miejsc docelowych przy użyciu [grupy Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Przenoszenie istniejącej maszyny Wirtualnej do grupy miejsc docelowych zbliżeniowych

Można również dodać istniejącą maszynę wirtualną do grupy miejsc docelowych zbliżeniowych. Musisz najpierw zatrzymać\oklokować maszynę wirtualną, a następnie zaktualizować maszynę wirtualną i ponownie uruchomić ją ponownie.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Przenoszenie istniejącej maszyny wirtualnej z grupy miejsc docelowych zbliżeniowych

Aby usunąć maszynę wirtualną z grupy miejsc docelowych zbliżeniowych, należy najpierw zatrzymać\zdelokalizować maszynę wirtualną, a następnie zaktualizować maszynę wirtualną i ponownie uruchomić ją ponownie.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Zestawy dostępności
Można również utworzyć zestaw dostępności w grupie miejsc docelowych zbliżeniowych. Użyj tego `-ProximityPlacementGroup` samego parametru z poleceniem cmdlet [New-AzAvailabilitySet,](/powershell/module/az.compute/new-azavailabilityset) aby utworzyć zestaw dostępności, a wszystkie maszyny wirtualne utworzone w zestawie dostępności zostaną również utworzone w tej samej grupie miejsc docelowych zbliżeniowych.

Aby dodać lub usunąć istniejącą grupę miejsc docelowych zbliżeniami, należy najpierw zatrzymać wszystkie maszyny wirtualne w zestawie dostępności. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Przenoszenie istniejącego zestawu dostępności do grupy miejsc docelowych zbliżeniowych

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Przenoszenie istniejącej dostępności ustawionej z grupy miejsc docelowych zbliżeniowych

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Zestawy skalowania

Można również utworzyć zestaw skalowania w grupie miejsc docelowych zbliżeniowych. Użyj tego `-ProximityPlacementGroup` samego parametru z [New-AzVmss,](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) aby utworzyć zestaw skalowania, a wszystkie wystąpienia zostaną utworzone w tej samej grupie miejsc docelowych zbliżeniowych.


Aby dodać lub usunąć istniejący zestaw skalowania do grupy miejsc docelowych zbliżeniowych, należy najpierw zatrzymać zestaw skalowania. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Przenoszenie istniejącego zestawu skalowania do grupy miejsc docelowych zbliżeniowych

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Przenoszenie istniejącej skali ustawionej z grupy miejsc docelowych zbliżeniowych

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki

Można również użyć [interfejsu wiersza polecenia platformy Azure](../linux/proximity-placement-groups.md) do tworzenia grup miejsc docelowych zbliżeniowych.
