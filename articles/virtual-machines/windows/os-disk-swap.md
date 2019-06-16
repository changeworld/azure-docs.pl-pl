---
title: Dysk wymiany systemu operacyjnego na Maszynie wirtualnej platformy Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: be00f8338430452faa8bd5a20d9e2ddfcbb9d09d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64701298"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Zmień dysk systemu operacyjnego używany przez Maszynę wirtualną platformy Azure przy użyciu programu PowerShell

Jeśli masz istniejącą maszynę Wirtualną, ale chcesz zamienić na dysku dla dysku kopii zapasowej lub inny dysk systemu operacyjnego, można użyć programu Azure PowerShell o zamianę dysków systemu operacyjnego. Nie masz usunięcie i ponowne utworzenie maszyny Wirtualnej. Nawet służy dysku zarządzanego w innej grupie zasobów, tak długo, jak go nie jest już używana.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Maszyna wirtualna musi być stopped\deallocated, a następnie identyfikator zasobu dysku zarządzanego można zastąpić o identyfikatorze zasobu z innego dysku zarządzanego.

Upewnij się, że typ rozmiaru i magazynu maszyny Wirtualnej są zgodne z dysku, który chcesz dołączyć. Na przykład w przypadku dysku, którego chcesz użyć w usłudze Premium Storage, maszyny Wirtualnej musi być w stanie magazynu w warstwie Premium (na przykład rozmiar serii DS). 

Pobierz listę dysków w grupie zasobów przy użyciu [Get AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Jeśli masz nazwę dysku, którego chcesz użyć, ustaw ją jako dysk systemu operacyjnego dla maszyny Wirtualnej. Ten przykład stop\deallocates maszyny Wirtualnej o nazwie *myVM* i przypisuje dysku o nazwie *newDisk* jako nowy dysk systemu operacyjnego. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Następne kroki**

Aby utworzyć kopię dysku, zobacz [Tworzenie migawki dysku](snapshot-copy-managed-disk.md).