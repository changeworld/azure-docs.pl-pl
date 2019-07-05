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
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: d67f9e9ad99ccd6267de9dbb9fd0c64adb41f0cb
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565555"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Przenoszenie maszyny Wirtualnej z systemem Windows do innego systemu Azure subskrypcji lub grupy zasobów
W tym artykule przedstawiono sposób przenoszenia Windows maszyny wirtualnej (VM) między grupami zasobów lub subskrypcji. Przenoszenie między subskrypcjami może być przydatna, jeśli pierwotnie utworzono Maszynę wirtualną w ramach subskrypcji osobistych i teraz chcesz przenieść je do subskrypcji w firmie, aby kontynuować pracę. Nie trzeba Uruchom maszynę Wirtualną, aby przenieść go i powinno ono nadal działa podczas przenoszenia.

> [!IMPORTANT]
>Nowych identyfikatorów zasobów są tworzone w ramach przejścia. Po przeniesieniu maszyny Wirtualnej należy zaktualizować narzędzia i skrypty do używania nowych identyfikatorów zasobów. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Przenoszenie maszyny Wirtualnej przy użyciu programu Powershell

Aby przenieść maszynę wirtualną do innej grupy zasobów, należy się upewnić, że możesz również przenieść wszystkie zasoby zależne. Aby uzyskać listę o identyfikatorze zasobu każdego z tych zasobów, użyj [Get AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) polecenia cmdlet.

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId 
```

Dane wyjściowe poprzedniego polecenia można użyć jako rozdzielaną przecinkami listę identyfikatorów zasobów do [AzResource przenoszenia](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) można przenieść każdego zasobu do miejsca docelowego. 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Aby przenieść zasoby do innej subskrypcji, należy dołączyć **- DestinationSubscriptionId** parametru. 

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Gdy zostanie wyświetlony monit upewnij się, że chcesz przenieść określonych zasobów, należy wprowadzić **Y** o potwierdzenie.

## <a name="next-steps"></a>Kolejne kroki
Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcji. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../resource-group-move-resources.md).    

