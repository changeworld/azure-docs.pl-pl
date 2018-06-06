---
title: Zmień rozmiar maszyny Wirtualnej systemu Windows na platformie Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Zmień rozmiar maszyny wirtualnej systemu Windows tworzone w modelu wdrażania usługi Resource Manager przy użyciu programu Azure Powershell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 8854f694ce067aaa80a159430a9f48440bcdd95a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701874"
---
# <a name="resize-a-windows-vm"></a>Zmień rozmiar maszyny Wirtualnej systemu Windows

W tym artykule przedstawiono sposób przenoszenia maszyn wirtualnych na inny [rozmiar maszyny Wirtualnej](sizes.md) przy użyciu programu Azure Powershell.

Po utworzeniu maszyny wirtualnej (VM), można skalować maszyny Wirtualnej w górę lub w dół przez zmianę rozmiaru maszyny Wirtualnej. W niektórych przypadkach należy najpierw cofnąć maszyny Wirtualnej. Może to nastąpić, jeśli nowy rozmiar jest niedostępny w klastrze sprzętu, który obecnie udostępnia maszyny Wirtualnej.

Jeśli maszyna wirtualna używa magazyn w warstwie Premium, upewnij się, że wybierasz **s** wersji rozmiar, aby uzyskać pomoc techniczną magazyn w warstwie Premium. Na przykład wybrać Standard_E4**s**_v3 zamiast Standard_E4_v3.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Zmień rozmiar maszyny Wirtualnej systemu Windows, nie znajduje się w zbiorze dostępności

Ustaw niektóre zmienne. Zastąp wartości odpowiednimi informacjami.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Wyświetl listę rozmiarów maszyn wirtualnych, które są dostępne w klastrze sprzętu, gdzie jest hostowana maszyny Wirtualnej. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Jeśli na liście jest odpowiedni rozmiar, uruchom następujące polecenia, aby zmienić rozmiar maszyny Wirtualnej. Jeśli nie ma żądanego rozmiaru, przejdź do kroku 3.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```

Jeśli odpowiedni rozmiar nie ma na liście, uruchom następujące polecenia można cofnąć alokacji maszyny Wirtualnej, rozmiar i ponownie maszyny Wirtualnej. Replease **<newVMsize>** z żądany rozmiar.
   
```powershell
Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Cofanie przydziału maszyny Wirtualnej zwalnia dynamiczne adresy IP przypisane do maszyny Wirtualnej. Nie dotyczy systemu operacyjnego i dysków z danymi. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Zmień rozmiar maszyny Wirtualnej systemu Windows w zestawie dostępności

Jeśli nowy rozmiar maszyny Wirtualnej w zestawie dostępności nie jest dostępne w klastrze sprzętu obecnie udostępnia maszyny Wirtualnej, wszystkich maszyn wirtualnych w zestawie dostępności będzie konieczne przydzielenia zmiany rozmiaru maszyny Wirtualnej. Możesz również może być konieczne zaktualizowanie rozmiar innych maszyn wirtualnych w zestawie po jednej maszyny Wirtualnej został zmieniony dostępności. Aby zmienić rozmiar maszyny Wirtualnej w zestawie dostępności, wykonaj następujące kroki.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Wyświetl listę rozmiarów maszyn wirtualnych, które są dostępne w klastrze sprzętu, gdzie jest hostowana maszyny Wirtualnej. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Jeśli na liście jest wymagany rozmiar, uruchom następujące polecenia, aby zmienić rozmiar maszyny Wirtualnej. Jeśli nie ma na liście, przejdź do następnej sekcji.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Jeśli nie ma odpowiedni rozmiar, wykonaj następujące kroki, aby cofnąć wszystkich maszyn wirtualnych w zestawie dostępności, Zmień rozmiar maszyn wirtualnych i uruchomić je ponownie.

Zatrzymanie wszystkich maszyn wirtualnych w zestawie dostępności.
   
```powershell
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Zmiana rozmiaru i ponowne uruchomienie maszyn wirtualnych w zestawie dostępności.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Kolejne kroki

Dodatkowe możliwości skalowania uruchamianie wielu wystąpień maszyny Wirtualnej i skalowanie w poziomie. Aby uzyskać więcej informacji, zobacz [automatycznie skalować w zestawie skalowania maszyn wirtualnych systemu Windows maszyny](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

