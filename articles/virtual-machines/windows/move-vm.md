---
title: Przenoszenie zasobu maszyny wirtualnej z systemem Windows na platformie Azure
description: Przenieś maszynę wirtualną z systemem Windows do innej subskrypcji platformy Azure lub grupy zasobów w Menedżer zasobów model wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: f5b4bf14be264d16109ddc10cd3b667e728642c6
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980707"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Przenoszenie maszyny wirtualnej z systemem Windows do innej subskrypcji platformy Azure lub grupy zasobów
W tym artykule pokazano, jak przenieść maszynę wirtualną z systemem Windows między grupami zasobów lub subskrypcjami. Przenoszenie między subskrypcjami może być przydatne, jeśli pierwotnie utworzono maszynę wirtualną w prywatnej subskrypcji, a teraz chcesz przenieść ją do subskrypcji firmy, aby kontynuować pracę. Nie musisz uruchamiać maszyny wirtualnej, aby przenieść ją i kontynuować pracę podczas przenoszenia.

> [!IMPORTANT]
>Nowe identyfikatory zasobów są tworzone w ramach przenoszenia. Po przeniesieniu maszyny wirtualnej należy zaktualizować narzędzia i skrypty w celu użycia nowych identyfikatorów zasobów.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Przenoszenie maszyny wirtualnej przy użyciu programu PowerShell

Aby przenieść maszynę wirtualną do innej grupy zasobów, należy upewnić się, że wszystkie zasoby zależne są również przeniesione. Aby uzyskać listę z IDENTYFIKATORem zasobu dla każdego z tych zasobów, należy użyć polecenia cmdlet [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) .

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId
```

Możesz użyć danych wyjściowych poprzedniego polecenia jako listę identyfikatorów zasobów rozdzielanych przecinkami, aby przenieść każdy [zasób do miejsca](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) docelowego.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Aby przenieść zasoby do innej subskrypcji, Dołącz parametr **-DestinationSubscriptionId** .

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Gdy zostanie wyświetlony monit o potwierdzenie, że chcesz przenieść określone zasoby, wpisz **Y** , aby potwierdzić.

## <a name="next-steps"></a>Następne kroki
Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcjami. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
