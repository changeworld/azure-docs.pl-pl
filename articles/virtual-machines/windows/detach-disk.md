---
title: Odłączyć dysku danych maszyny wirtualnej systemu Windows - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się odłączyć dysku danych z maszyny wirtualnej na platformie Azure przy użyciu modelu wdrażania Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: e56e9ce22cc9e2bad75c944c20bff812d8720d18
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30913505"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Jak można odłączyć dysku danych od maszyny wirtualnej systemu Windows
Gdy już nie potrzebujesz dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Usuwa dysk od maszyny wirtualnej, ale nie powoduje usunięcia go z magazynu.

> [!WARNING]
> Jeśli możesz odłączyć dysk, który nie jest automatycznie usuwana. Jeśli masz subskrypcję do magazyn w warstwie Premium, będą nadal naliczane opłaty za magazyn dla dysku. Więcej informacji można znaleźć w [cennik i rozliczenia w przypadku korzystania z magazyn w warstwie Premium](premium-storage.md#pricing-and-billing).
>
>

Jeśli chcesz użyć danych znajdujących się na tym dysku, możesz dołączyć go ponownie do tej samej lub innej maszyny wirtualnej.

## <a name="detach-a-data-disk-using-the-portal"></a>Odłączanie dysku danych przy użyciu portalu

1. W menu po lewej stronie wybierz **maszyn wirtualnych**.
2. Wybierz maszynę wirtualną, która ma dysk danych, aby odłączyć, a następnie kliknij przycisk **zatrzymać** można cofnąć alokacji maszyny Wirtualnej.
3. W okienku maszyny wirtualnej, wybierz **dysków**.
4. W górnej części **dysków** okienku wybierz **Edytuj**.
5. W **dysków** okienku po prawej dysku danych, który chcesz odłączyć, kliknij przycisk ![obraz przycisku Detach](./media/detach-disk/detach.png) odłączyć przycisku.
5. Po usunięciu dysk, kliknij przycisk **zapisać** górnej części okienka.
6. W okienku maszyny wirtualnej, kliknij **omówienie** , a następnie kliknij przycisk **Start** przycisk w górnej części okienka, aby ponownie uruchomić maszyny Wirtualnej.



Odłączony dysk pozostaje w magazynie, lecz nie jest już dołączony do maszyny wirtualnej.

## <a name="detach-a-data-disk-using-powershell"></a>Odłączyć dysku danych przy użyciu programu PowerShell
W tym przykładzie pierwsze polecenie pobiera maszyny wirtualnej o nazwie **MyVM07** w **RG11** grupy zasobów przy użyciu [Get-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet i zapisuje go w **$VirtualMachine** zmiennej.

Drugi wiersz usuwa dysku danych o nazwie DataDisk3 z maszyny wirtualnej przy użyciu [AzureRmVMDataDisk Usuń](/powershell/module/azurerm.compute/remove-azurermvmdatadisk) polecenia cmdlet.

Trzeci wiersz aktualizuje stan maszyny wirtualnej przy użyciu [AzureRmVM aktualizacji](/powershell/module/azurerm.compute/update-azurermvm) polecenia cmdlet, aby ukończyć proces usuwania dysk z danymi.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -VM $VirtualMachine
```

Aby uzyskać więcej informacji, zobacz [AzureRmVMDataDisk Usuń](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz ponownie użyć dysku danych, możesz po prostu [dołączenie go do innej maszyny Wirtualnej](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

