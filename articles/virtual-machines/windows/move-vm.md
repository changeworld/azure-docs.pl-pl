---
title: Przenoszenie zasobu maszyny Wirtualnej systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Przenieś Maszynę wirtualną systemu Windows do innego Azure subskrypcji lub grupy zasobów w modelu wdrażania usługi Resource Manager.
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
ms.openlocfilehash: b98b8c947fb34b60c7bd27b006672e0e9d923d3b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Przenieś Maszynę wirtualną systemu Windows do innego Azure subskrypcji lub grupy zasobów
W tym artykule przedstawiono sposób przenoszenia maszyny Wirtualnej systemu Windows między grupami zasobów lub subskrypcji. Przenoszenie między subskrypcjami można skorzystać, jeśli pierwotnie utworzono Maszynę wirtualną w osobistych subskrypcji i chcesz teraz Przenieś go do subskrypcji w firmie, aby kontynuować pracę.

> [!IMPORTANT]
>Nie można przenieść dysków zarządzanych w tej chwili. 
>
>Nowych identyfikatorów zasobów są tworzone w ramach przeniesienia. Po przeniesieniu maszyny Wirtualnej należy zaktualizować narzędzia i skrypty do używania nowych identyfikatorów zasobów. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Aby przenieść Maszynę wirtualną za pomocą programu Powershell

Aby przenieść maszynę wirtualną w innej grupie zasobów, musisz upewnij się, że można również przenieść wszystkie zasoby zależne. Aby użyć polecenia cmdlet Move-AzureRMResource, należy ResourceId każdego z zasobów. Można uzyskać listę przy użyciu element ResourceId [AzureRMResource Znajdź](/powershell/module/azurerm.resources/find-azurermresource) polecenia cmdlet.

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Aby przenieść Maszynę wirtualną, trzeba przenieść wiele zasobów. Możemy użyć danych wyjściowych Znajdź AzureRMResource tworzenie rozdzielaną przecinkami listą identyfikatory zasobów i że w celu przekazania [AzureRMResource przenoszenia](/powershell/module/azurerm.resources/move-azurermresource) Aby przenieść je do miejsca docelowego. 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Przeniesienie zasobów do innej subskrypcji, obejmują **- DestinationSubscriptionId** parametru. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Użytkownik jest proszony o potwierdzenie, że chcesz przenieść określonych zasobów. 

## <a name="next-steps"></a>Kolejne kroki
Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcje. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](../../resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).    

