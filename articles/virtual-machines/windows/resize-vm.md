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
ms.date: 10/19/2016
ms.author: cynthn
ms.openlocfilehash: d2010ee9017416360069c74118b8ae25e71e1da7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="resize-a-windows-vm"></a>Zmień rozmiar maszyny Wirtualnej systemu Windows
W tym artykule przedstawiono sposób zmiany rozmiaru maszyny Wirtualnej systemu Windows, utworzony w modelu wdrażania usługi Resource Manager przy użyciu programu Azure Powershell.

Po utworzeniu maszyny wirtualnej (VM), można skalować maszyny Wirtualnej w górę lub w dół przez zmianę rozmiaru maszyny Wirtualnej. W niektórych przypadkach należy najpierw cofnąć maszyny Wirtualnej. Może to nastąpić, jeśli nowy rozmiar jest niedostępny w klastrze sprzętu, który obecnie udostępnia maszyny Wirtualnej.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Zmień rozmiar maszyny Wirtualnej systemu Windows, nie znajduje się w zbiorze dostępności
1. Wyświetl listę rozmiarów maszyn wirtualnych, które są dostępne w klastrze sprzętu, gdzie jest hostowana maszyny Wirtualnej. 
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```
2. Jeśli na liście jest wymagany rozmiar, uruchom następujące polecenia, aby zmienić rozmiar maszyny Wirtualnej. Jeśli nie ma żądanego rozmiaru, przejdź do kroku 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Jeśli żądany rozmiar nie jest wyświetlana, uruchom następujące polecenia można cofnąć alokacji maszyny Wirtualnej, rozmiar i ponownie maszyny Wirtualnej.
   
    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -Name $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [!WARNING]
> Cofanie przydziału maszyny Wirtualnej zwalnia dynamiczne adresy IP przypisane do maszyny Wirtualnej. Nie dotyczy systemu operacyjnego i dysków z danymi. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Zmień rozmiar maszyny Wirtualnej systemu Windows w zestawie dostępności
Jeśli nowy rozmiar maszyny Wirtualnej w zestawie dostępności nie jest dostępne w klastrze sprzętu obecnie udostępnia maszyny Wirtualnej, wszystkich maszyn wirtualnych w zestawie dostępności będzie konieczne przydzielenia zmiany rozmiaru maszyny Wirtualnej. Możesz również może być konieczne zaktualizowanie rozmiar innych maszyn wirtualnych w zestawie po jednej maszyny Wirtualnej został zmieniony dostępności. Aby zmienić rozmiar maszyny Wirtualnej w zestawie dostępności, wykonaj następujące kroki.

1. Wyświetl listę rozmiarów maszyn wirtualnych, które są dostępne w klastrze sprzętu, gdzie jest hostowana maszyny Wirtualnej.
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```
2. Jeśli na liście jest wymagany rozmiar, uruchom następujące polecenia, aby zmienić rozmiar maszyny Wirtualnej. Jeśli nie ma na liście, przejdź do kroku 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Wymagany rozmiar nie jest wyświetlana, kontynuuj następujące kroki, aby cofnąć wszystkich maszyn wirtualnych w zestawie dostępności, Zmień rozmiar maszyn wirtualnych i uruchomić je ponownie.
4. Zatrzymanie wszystkich maszyn wirtualnych w zestawie dostępności.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
   } 
   ```
5. Zmiana rozmiaru i ponowne uruchomienie maszyn wirtualnych w zestawie dostępności.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $newSize = "<newVmSize>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
     $vm.HardwareProfile.VmSize = $newSize
     Update-AzureRmVM -ResourceGroupName $rg -VM $vm
     Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
   }
   ```

## <a name="next-steps"></a>Kolejne kroki
* Dodatkowe możliwości skalowania uruchamianie wielu wystąpień maszyny Wirtualnej i skalowanie w poziomie. Aby uzyskać więcej informacji, zobacz [automatycznie skalować w zestawie skalowania maszyn wirtualnych systemu Windows maszyny](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

