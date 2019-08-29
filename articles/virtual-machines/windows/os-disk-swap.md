---
title: Wymiana dysku systemu operacyjnego dla maszyny wirtualnej platformy Azure przy użyciu programu PowerShell | Microsoft Docs "
description: Zmień dysk systemu operacyjnego używany przez maszynę wirtualną platformy Azure przy użyciu programu PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: b213db38dade06e5015227494fa77b2f465ba1d9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089058"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Zmienianie dysku systemu operacyjnego używanego przez maszynę wirtualną platformy Azure przy użyciu programu PowerShell

Jeśli masz istniejącą maszynę wirtualną, ale chcesz zamienić dysk na dysk kopii zapasowej lub inny dysk systemu operacyjnego, możesz użyć Azure PowerShell do wymiany dysków systemu operacyjnego. Nie trzeba usuwać ani tworzyć ponownie maszyny wirtualnej. Można nawet użyć dysku zarządzanego w innej grupie zasobów, o ile nie jest on już używany.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Maszyna wirtualna musi być stopped\deallocated, a następnie identyfikator zasobu dysku zarządzanego można zastąpić IDENTYFIKATORem zasobu innego dysku zarządzanego.

Upewnij się, że rozmiar maszyny wirtualnej i typ magazynu są zgodne z dyskiem, który chcesz dołączyć. Na przykład jeśli dysk, który ma być używany, znajduje się w Premium Storage, maszyna wirtualna musi mieć możliwość Premium Storage (na przykład rozmiaru serii DS). 

Pobierz listę dysków w grupie zasobów za pomocą polecenia [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Jeśli masz nazwę dysku, którego chcesz użyć, Ustaw jako dysk systemu operacyjnego dla maszyny wirtualnej. Ten przykład stop\deallocates maszynę wirtualną o nazwie *myVM* i przypisuje dysk o nazwie *newDisk* jako nowy dysk systemu operacyjnego. 
 
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

Aby utworzyć kopię dysku, zobacz [migawka dysku](snapshot-copy-managed-disk.md).