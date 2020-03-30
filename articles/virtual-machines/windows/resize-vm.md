---
title: Ponowne rozmiary maszyny Wirtualnej systemu Windows na platformie Azure
description: Zmień rozmiar maszyny wirtualnej używanej dla maszyny wirtualnej platformy Azure.
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
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 6718804d4635edb2628b53017ab9d377928afad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941720"
---
# <a name="resize-a-windows-vm"></a>Zmienianie rozmiaru maszyny wirtualnej z systemem Windows

W tym artykule pokazano, jak przenieść maszynę wirtualną do innego [rozmiaru maszyny Wirtualnej](sizes.md).

Po utworzeniu maszyny wirtualnej (VM), można skalować maszynę wirtualną w górę lub w dół, zmieniając rozmiar maszyny wirtualnej. W niektórych przypadkach należy najpierw zdelocytować alokację maszyny Wirtualnej. Może się tak zdarzyć, jeśli nowy rozmiar nie jest dostępny w klastrze sprzętowym, który jest obecnie gospodarzem maszyny Wirtualnej.

Jeśli maszyna wirtualna używa magazynu w wersji Premium, upewnij się, że wybierz **wersję s** o rozmiarze, aby uzyskać obsługę magazynu w wersji Premium. Na przykład wybierz Standard_E4**s**_v3 zamiast Standard_E4_v3.

## <a name="use-the-portal"></a>Używanie portalu

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Otwórz stronę maszyny wirtualnej.
1. W menu po lewej stronie wybierz opcję **Rozmiar**.
1. Wybierz nowy rozmiar z listy dostępnych rozmiarów, a następnie wybierz pozycję **Przerzuć rozmiar**.


Jeśli maszyna wirtualna jest aktualnie uruchomiona, zmiana jej rozmiaru spowoduje jej ponowne uruchomienie. Zatrzymanie maszyny wirtualnej może ujawnić dodatkowe rozmiary.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>Używanie programu PowerShell do ponownego rozmiaru maszyny wirtualnej, która nie jest w zestawie dostępności

Ustaw niektóre zmienne. Zastąp wartości własnymi informacjami.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Wyświetl listę rozmiarów maszyn wirtualnych dostępnych w klastrze sprzętowym, w którym znajduje się maszyna wirtualna. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Jeśli odpowiedni rozmiar znajduje się na liście, uruchom następujące polecenia, aby zmienić rozmiar maszyny Wirtualnej. Jeśli żądany rozmiar nie jest wymieniony, przejdź do kroku 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Jeśli żądany rozmiar nie znajduje się na liście, uruchom następujące polecenia, aby zmienić alokację maszyny Wirtualnej, zmienić jej rozmiar i ponownie uruchomić maszynę wirtualną. Zastąp ** \<>rozmiaru newVMsize** na odpowiedni rozmiar.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Rozdzielanie maszyny Wirtualnej zwalnia wszystkie dynamiczne adresy IP przypisane do maszyny Wirtualnej. Nie ma to wpływu na system operacyjny i dyski danych. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Ponowne rozmiary maszyny wirtualnej za pomocą programu PowerShell

Jeśli nowy rozmiar maszyny Wirtualnej w zestawie dostępności nie jest dostępny w klastrze sprzętowym aktualnie obsługującym maszynę wirtualną, wszystkie maszyny wirtualne w zestawie dostępności będą musiały zostać przydzielone do zmiany rozmiaru maszyny Wirtualnej. Może być również konieczne zaktualizowanie rozmiaru innych maszyn wirtualnych w zestawie dostępności po przesiąkniętym rozmiarze jednej maszyny Wirtualnej. Aby zmienić rozmiar maszyny Wirtualnej w zestawie dostępności, wykonaj następujące kroki.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Wyświetl listę rozmiarów maszyn wirtualnych dostępnych w klastrze sprzętowym, w którym znajduje się maszyna wirtualna. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Jeśli żądany rozmiar jest na liście, uruchom następujące polecenia, aby zmienić rozmiar maszyny Wirtualnej. Jeśli nie ma go na liście, przejdź do następnej sekcji.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Jeśli żądany rozmiar nie znajduje się na liście, przejdź do następujących kroków, aby zmienić alokację wszystkich maszyn wirtualnych w zestawie dostępności, zmienić rozmiar maszyn wirtualnych i ponownie uruchomić je ponownie.

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

Zmienić rozmiar i ponownie uruchomić maszyny wirtualne w zestawie dostępności.
   
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

Aby uzyskać dodatkową skalowalność, uruchom wiele wystąpień maszyn wirtualnych i skaluj w poziomie. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie maszyn z systemem Windows w zestawie skalowania maszyny wirtualnej](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

