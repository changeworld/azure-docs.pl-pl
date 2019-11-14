---
title: Zmienianie rozmiaru maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu programu PowerShell
description: Zmienianie rozmiaru maszyny wirtualnej z systemem Windows utworzonej w modelu wdrażania Menedżer zasobów przy użyciu programu Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 4b30f2fd8e095b00898e083e33c23c7c9a915b99
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073370"
---
# <a name="resize-a-windows-vm"></a>Zmienianie rozmiaru maszyny wirtualnej z systemem Windows

W tym artykule pokazano, jak przenieść maszynę wirtualną do innego [rozmiaru maszyny wirtualnej](sizes.md) przy użyciu programu Azure PowerShell.

Po utworzeniu maszyny wirtualnej można skalować ją w górę lub w dół, zmieniając rozmiar maszyny wirtualnej. W niektórych przypadkach należy najpierw cofnąć alokację maszyny wirtualnej. Może się tak zdarzyć, jeśli nowy rozmiar nie jest dostępny w klastrze sprzętowym hostującym maszynę wirtualną.

Jeśli maszyna wirtualna używa Premium Storage, upewnij się, że wybrano wersję **s** rozmiaru do uzyskania pomocy technicznej Premium Storage. Na przykład wybierz Standard_E4**s**_v3 zamiast Standard_E4_v3.

 

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Zmienianie rozmiaru maszyny wirtualnej z systemem Windows, która nie znajduje się w zestawie dostępności

Ustaw pewne zmienne. Zastąp wartości własnymi informacjami.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Wyświetl listę rozmiarów maszyn wirtualnych, które są dostępne w klastrze sprzętowym, w którym jest hostowana maszyna wirtualna. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Jeśli odpowiedni rozmiar znajduje się na liście, uruchom następujące polecenia, aby zmienić rozmiar maszyny wirtualnej. Jeśli żądany rozmiar nie znajduje się na liście, przejdź do kroku 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Jeśli żądany rozmiar nie znajduje się na liście, uruchom następujące polecenia, aby cofnąć alokację maszyny wirtualnej, zmienić jej rozmiar i ponownie uruchomić maszynę wirtualną. Zastąp **\<newVMsize >** rozmiarem, który chcesz.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Cofnięcie przydziału maszyny wirtualnej powoduje wydanie wszystkich dynamicznych adresów IP przypisanych do maszyny wirtualnej. Nie dotyczy to dysków systemu operacyjnego i danych. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Zmienianie rozmiaru maszyny wirtualnej z systemem Windows w zestawie dostępności

Jeśli nowy rozmiar maszyny wirtualnej w zestawie dostępności nie jest dostępny w klastrze sprzętowym hostującym maszynę wirtualną, wszystkie maszyny wirtualne w zestawie dostępności muszą zostać cofnięte, aby zmienić rozmiar maszyny wirtualnej. Może być też konieczne zaktualizowanie rozmiaru pozostałych maszyn wirtualnych w zestawie dostępności po zmianie rozmiaru jednej maszyny wirtualnej. Aby zmienić rozmiar maszyny wirtualnej w zestawie dostępności, wykonaj następujące czynności.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Wyświetl listę rozmiarów maszyn wirtualnych, które są dostępne w klastrze sprzętowym, w którym jest hostowana maszyna wirtualna. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Jeśli jest wyświetlany żądany rozmiar, uruchom następujące polecenia, aby zmienić rozmiar maszyny wirtualnej. Jeśli nie ma go na liście, przejdź do następnej sekcji.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Jeśli żądanego rozmiaru nie ma na liście, wykonaj następujące kroki, aby cofnąć alokację wszystkich maszyn wirtualnych w zestawie dostępności, zmienić rozmiar maszyn wirtualnych, a następnie uruchom je ponownie.

Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Zmień rozmiar i ponownie uruchom maszyny wirtualne w zestawie dostępności.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkową skalowalność, należy uruchomić wiele wystąpień maszyn wirtualnych i skalować je w poziomie. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie maszyn z systemem Windows w zestawie skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

