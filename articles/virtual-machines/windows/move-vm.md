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
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: 168ba57399b2649af29820f7321dd0151618346e
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436484"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Przenoszenie maszyny Wirtualnej z systemem Windows do innego systemu Azure subskrypcji lub grupy zasobów
W tym artykule przedstawiono sposób przenoszenia maszyny Wirtualnej z systemem Windows, między grupami zasobów lub subskrypcji. Przenoszenie między subskrypcjami może być przydatna, jeśli pierwotnie utworzono Maszynę wirtualną w ramach subskrypcji osobistych i teraz chcesz przenieść je do subskrypcji w firmie, aby kontynuować pracę.

> [!IMPORTANT]
>W tej chwili nie można przenieść dyski Managed Disks. 
>
>Nowych identyfikatorów zasobów są tworzone w ramach przejścia. Po przeniesieniu maszyny Wirtualnej, musisz zaktualizować swoje narzędzia i skrypty do używania nowych identyfikatorów zasobów. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Przenoszenie maszyny Wirtualnej przy użyciu programu Powershell

Aby przenieść maszynę wirtualną do innej grupy zasobów, należy się upewnić, że możesz również przenieść wszystkie zasoby zależne. Aby użyć polecenia cmdlet Move-AzureRMResource, należy ResourceId poszczególnych zasobów. Możesz uzyskać listę przy użyciu ResourceId [Get-AzureRMResource](/powershell/module/azurerm.resources/get-azurermresource) polecenia cmdlet.

```azurepowershell-interactive
 Get-AzureRMResource -ResourceGroupName <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Aby przenieść Maszynę wirtualną trzeba przenieść wiele zasobów. Możemy użyć danych wyjściowych Get-AzureRMResource, aby utworzyć listę rozdzielonych przecinkami identyfikatory zasobów i przekazać go do [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) przenieść je do miejsca docelowego. 

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


Użytkownik jest proszony o upewnij się, że chcesz przenieść określonych zasobów. 

## <a name="next-steps"></a>Kolejne kroki
Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcji. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](../../resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).    

