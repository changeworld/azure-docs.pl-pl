---
title: Dysk wymiany systemu operacyjnego dla maszyny Wirtualnej platformy Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Zmień dysk systemu operacyjnego używany przez maszynę wirtualną platformy Azure przy użyciu programu PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: caa8fe2088995e3d30c9b808f639b9280e3a74be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196196"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Zmień dysk systemu operacyjnego używany przez maszyny Wirtualnej platformy Azure przy użyciu programu PowerShell

Jeśli masz istniejącą maszynę Wirtualną, ale chcesz wymienić dysk kopii zapasowej dysku lub innego dysku systemu operacyjnego, można użyć programu Azure PowerShell, można zamienić na dyskach systemu operacyjnego. Nie masz usunięcie i ponowne utworzenie maszyny Wirtualnej. Nawet służy dysków zarządzanych w innej grupie zasobów, jak długo nie znajduje się już w użyciu.

Maszyna wirtualna musi być stopped\deallocated, a następnie identyfikator zasobu zarządzanego dysku można zastąpić identyfikator zasobu zarządzanego inny dysk.

Upewnij się, że typu rozmiaru i magazynu maszyny Wirtualnej są zgodne z dysku, który chcesz dołączyć. Na przykład jeśli dysk, który ma być używany jest magazyn w warstwie Premium, następnie maszyny Wirtualnej musi być w stanie magazyn w warstwie Premium (takich jak rozmiar z serii DS). 

Pobierz listę dysków w grupie zasobów przy użyciu [Get AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk)

```azurepowershell-interactive
Get-AzureRmDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Jeśli nazwa dysku, który chcesz użyć, ustawić go jako dysk systemu operacyjnego dla maszyny Wirtualnej. Ten przykład stop\deallocates maszyny Wirtualnej o nazwie *myVM* i przypisuje dysku o nazwie *newDisk* jako nowy dysk systemu operacyjnego. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Następne kroki**

Aby utworzyć kopię dysku, zobacz [migawki dysku](snapshot-copy-managed-disk.md).