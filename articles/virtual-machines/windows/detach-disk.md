---
title: Odłączanie dysku danych z maszyny wirtualnej z systemem Windows — Azure
description: Odłączanie dysku danych z maszyny wirtualnej na platformie Azure przy użyciu modelu wdrażania Menedżer zasobów.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/08/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 301f3abd26f702f3f29c8833c835ba7d0e41bcaf
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834600"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Jak odłączyć dysk danych z maszyny wirtualnej z systemem Windows

Gdy już nie potrzebujesz dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Spowoduje to usunięcie dysku z maszyny wirtualnej, ale nie powoduje usunięcia go z magazynu.

> [!WARNING]
> Jeśli dysk zostanie odłączony, nie jest automatycznie usuwany. Jeśli subskrybujesz usługę Premium Storage, nadal będą naliczane opłaty za magazyn dla tego dysku. Aby uzyskać więcej informacji, zobacz [Cennik i rozliczenia w przypadku korzystania z Premium Storage](disks-types.md#billing).

Jeśli chcesz użyć danych znajdujących się na tym dysku, możesz dołączyć go ponownie do tej samej lub innej maszyny wirtualnej.

 

## <a name="detach-a-data-disk-using-powershell"></a>Odłączanie dysku danych przy użyciu programu PowerShell

Możesz *gorąco* usuwanie dysku danych przy użyciu programu PowerShell, ale przed odłączeniem go od maszyny wirtualnej nie trzeba aktywnie używać dysku.

W tym przykładzie usuniemy **dysk o nazwie** **myVM** z maszyny wirtualnej z **grupy zasobów.** Najpierw należy usunąć dysk przy użyciu polecenia cmdlet [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) . Następnie należy zaktualizować stan maszyny wirtualnej za pomocą polecenia cmdlet [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) , aby ukończyć proces usuwania dysku z danymi.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.

## <a name="detach-a-data-disk-using-the-portal"></a>Odłączanie dysku danych przy użyciu portalu

Można *gorąco* usuwanie dysku danych, ale nie należy aktywnie używać dysku przed odłączeniem go od maszyny wirtualnej.

1. W menu po lewej stronie wybierz pozycję **Virtual Machines**.
1. Wybierz maszynę wirtualną zawierającą dysk danych, który chcesz odłączyć.
1. W obszarze **Ustawienia** wybierz pozycję **Dyski**.
1. W górnej części okienka **dyski** wybierz pozycję **Edytuj**.
1. W okienku **dyski** z prawej strony dysku z danymi, które chcesz odłączyć, wybierz opcję **Odłącz**.
1. Wybierz pozycję **Zapisz** w górnej części strony, aby zapisać zmiany.

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz ponownie użyć dysku z danymi, możesz po prostu [dołączyć go do innej maszyny wirtualnej](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
