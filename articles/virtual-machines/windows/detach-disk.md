---
title: Odłączanie dysku danych z maszyny Wirtualnej Windows — Azure | Dokumentacja firmy Microsoft
description: Odłączanie dysku danych z maszyny wirtualnej na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: ca9a4478249e935afb6a52520c77d9df159fe9e7
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718729"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Jak odłączyć dysk z danymi od maszyny wirtualnej Windows

Gdy już nie potrzebujesz dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Usuwa dysk od maszyny wirtualnej, ale nie spowoduje usunięcia go z magazynu.

> [!WARNING]
> Jeśli możesz odłączyć dysku, który nie jest automatycznie usuwana. Jeśli subskrybujesz usługę Premium storage, możesz nadal będziesz ponosić opłaty za magazyn na dysku. Aby uzyskać więcej informacji, zobacz [cen i rozliczeń, korzystając z usługi Premium Storage](disks-types.md#billing).

Jeśli chcesz użyć danych znajdujących się na tym dysku, możesz dołączyć go ponownie do tej samej lub innej maszyny wirtualnej.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="detach-a-data-disk-using-powershell"></a>Odłączanie dysku danych przy użyciu programu PowerShell

Możesz *gorąca* usunięcie dysku danych przy użyciu programu PowerShell, ale upewnij się, że nic nie jest aktywnie za pomocą dysku przed odłączeniem go z maszyny Wirtualnej.

W tym przykładzie usuwamy dysku o nazwie **myDisk** z maszyny Wirtualnej **myVM** w **myResourceGroup** grupy zasobów. Najpierw usuń dysk przy użyciu [AzVMDataDisk Usuń](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) polecenia cmdlet. Następnie zaktualizuj stan maszyny wirtualnej przy użyciu [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) polecenia cmdlet, aby ukończyć proces usuwania dysku danych.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.

## <a name="detach-a-data-disk-using-the-portal"></a>Odłączanie dysku danych przy użyciu portalu

1. W menu po lewej stronie wybierz **maszyn wirtualnych**.
2. Wybierz maszynę wirtualną, która ma dysk danych, które chcesz odłączyć, a następnie kliknij przycisk **zatrzymać** można cofnąć przydziału maszyny Wirtualnej.
3. W okienku maszyny wirtualnej wybierz **dysków**.
4. W górnej części **dysków** okienku wybierz **Edytuj**.
5. W **dysków** okienko po prawej dysku danych, który chcesz odłączyć, kliknij przycisk ![obraz przycisku Odłącz](./media/detach-disk/detach.png) odłączyć przycisku.
5. Po usunięciu dysku kliknij **Zapisz** u góry okienka.
6. W okienku maszyny wirtualnej kliknij **Przegląd** a następnie kliknij przycisk **Start** przycisk u góry okienka, aby ponownie uruchomić maszynę Wirtualną.

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz ponownie użyć dysku danych, możesz po prostu [dołączyć go do innej maszyny Wirtualnej](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)