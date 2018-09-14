---
title: Przenieś zasób maszynę Wirtualną Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Przenieś maszyny Wirtualnej z systemem Windows do innego systemu Azure subskrypcji lub grupy zasobów w modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 1daf04e3f878d0748bfa0904259c7b7187481843
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580492"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Przenoszenie maszyny Wirtualnej z systemem Windows do innego systemu Azure subskrypcji lub grupy zasobów
W tym artykule przedstawiono sposób przenoszenia Windows maszyny wirtualnej (VM) między grupami zasobów lub subskrypcji. Przenoszenie między subskrypcjami może być przydatna, jeśli pierwotnie utworzono Maszynę wirtualną w ramach subskrypcji osobistych i teraz chcesz przenieść je do subskrypcji w firmie, aby kontynuować pracę.

> [!IMPORTANT]
>W tej chwili nie można przenieść usługę Azure Managed Disks. 
>
>Nowych identyfikatorów zasobów są tworzone w ramach przejścia. Po przeniesieniu maszyny Wirtualnej należy zaktualizować narzędzia i skrypty do używania nowych identyfikatorów zasobów. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Przenoszenie maszyny Wirtualnej przy użyciu programu Powershell

Aby przenieść maszynę wirtualną do innej grupy zasobów, należy się upewnić, że możesz również przenieść wszystkie zasoby zależne. Aby uzyskać listę o identyfikatorze zasobu każdego z tych zasobów, użyj [Get-AzureRMResource](/powershell/module/azurerm.resources/get-azurermresource) polecenia cmdlet.

```azurepowershell-interactive
 Get-AzureRMResource -ResourceGroupName <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Dane wyjściowe poprzedniego polecenia można użyć jako rozdzielaną przecinkami listę identyfikatorów zasobów do [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) można przenieść każdego zasobu do miejsca docelowego. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Aby przenieść zasoby do innej subskrypcji, należy dołączyć **- DestinationSubscriptionId** parametru. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Gdy zostanie wyświetlony monit upewnij się, że chcesz przenieść określonych zasobów, należy wprowadzić **Y** o potwierdzenie.

## <a name="next-steps"></a>Kolejne kroki
Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcji. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../resource-group-move-resources.md).    

