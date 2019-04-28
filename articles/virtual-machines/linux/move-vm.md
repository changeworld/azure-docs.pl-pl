---
title: Przenieś Maszynę wirtualną systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Przenieś Maszynę wirtualną systemu Linux do innego systemu Azure subskrypcji lub grupy zasobów w modelu wdrażania usługi Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: d2d3f36c9b4ee0557f9e060bec762877a94ea637
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473952"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Przenieś Maszynę wirtualną systemu Linux do innej subskrypcji lub grupy zasobów
W tym artykule przedstawiono sposób przenoszenia maszyny wirtualnej systemu Linux (VM) między grupami zasobów lub subskrypcji. Przenoszenie maszyny Wirtualnej między subskrypcjami może być przydatna, jeśli maszyna wirtualna została utworzona w ramach subskrypcji osobistych i teraz chcesz przenieść je do subskrypcji Twojej firmy.

> [!IMPORTANT]
>W tej chwili nie można przenieść usługę Azure Managed Disks. 
>
>Nowych identyfikatorów zasobów są tworzone w ramach przejścia. Po przeniesieniu maszyny Wirtualnej należy zaktualizować narzędzia i skrypty do używania nowych identyfikatorów zasobów. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Użyj wiersza polecenia platformy Azure, aby przenieść Maszynę wirtualną


Przed można przenieść maszynę Wirtualną przy użyciu wiersza polecenia platformy Azure, należy się upewnić, że istnieją subskrypcje źródłowe i docelowe, w ramach tej samej dzierżawy. Aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy, użyj [Pokaż konta az](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Jeśli identyfikatorów dzierżawy subskrypcje źródłowe i docelowe nie są takie same, należy skontaktować się [obsługuje](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) przeniesienie zasobów do nowej dzierżawy.

Aby pomyślnie przenieść Maszynę wirtualną, musisz przenieść maszynę Wirtualną i wszystkie jej zasoby pomocnicze. Użyj [az resource list](/cli/azure/resource) polecenie, aby wyświetlić listę wszystkich zasobów w grupie zasobów i ich identyfikatorów. Pomaga przekazać dane wyjściowe tego polecenia do pliku, dzięki czemu możesz skopiować i wkleić identyfikatorów do nowszego polecenia.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Aby przenieść Maszynę wirtualną i jej zasobów do innej grupy zasobów, użyj [przenoszenia zasobów az](/cli/azure/resource). Poniższy przykład pokazuje, jak przenieść maszyny Wirtualnej i najbardziej typowych zasobów, których potrzebuje. Użyj **— identyfikatory** parametru i przekaż listę rozdzielanych przecinkami (bez spacji) identyfikatorów zasobów, aby przenieść.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Jeśli chcesz przenieść maszynę Wirtualną i jej zasobów do innej subskrypcji, należy dodać **— identyfikator subskrypcji docelowej** parametru do określenia subskrypcja docelowa.

Gdy zostanie wyświetlony monit upewnij się, że chcesz przenieść określonych zasobów, należy wprowadzić **Y** o potwierdzenie.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Kolejne kroki
Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcji. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../resource-group-move-resources.md).    

