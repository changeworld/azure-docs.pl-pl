---
title: Przenieś Maszynę wirtualną systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Przenieś Maszynę wirtualną systemu Linux do innego Azure subskrypcji lub grupy zasobów w modelu wdrażania usługi Resource Manager.
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
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: a4a7dd5541fe298675232ffa803f749e71f6a03f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30907501"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Przenieś Maszynę wirtualną systemu Linux do innej subskrypcji lub grupy zasobów
W tym artykule przedstawiono sposób przenieść Maszynę wirtualną systemu Linux między grupami zasobów lub subskrypcji. Przenoszenie maszyny Wirtualnej między subskrypcjami można przydatne, jeśli utworzono Maszynę wirtualną w osobistych subskrypcji i chcesz teraz Przenieś go do subskrypcji w firmie.

> [!IMPORTANT]
>Nie można przenieść dysków zarządzanych w tej chwili. 
>
>Nowych identyfikatorów zasobów są tworzone w ramach przeniesienia. Po przeniesieniu maszyny Wirtualnej należy zaktualizować narzędzia i skrypty do używania nowych identyfikatorów zasobów. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Użyj interfejsu wiersza polecenia Azure, aby przenieść Maszynę wirtualną


Zanim będzie możliwe przeniesienie maszyny Wirtualnej przy użyciu interfejsu wiersza polecenia, musisz upewnij się, że istnieją subskrypcje źródłowym i docelowym w ramach tej samej dzierżawy. Aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy, użyj [Pokaż konto az](/cli/azure/account#az_account_show).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Jeśli dzierżawy identyfikatorów subskrypcji źródłowych i docelowych nie są takie same, należy skontaktować się [obsługuje](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) przeniesienie zasobów do nowej dzierżawy.

Aby pomyślnie przenieść Maszynę wirtualną, należy przenieść maszynę Wirtualną i wszystkie dodatkowe zasoby. Użyj [listę zasobów az](/cli/azure/resource#az_resource_list) polecenie, aby wyświetlić listę wszystkich zasobów w grupie zasobów i ich identyfikatorów. Pomaga przekazać dane wyjściowe tego polecenia do pliku, można skopiować i wkleić identyfikatorów na nowsze poleceń.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Aby przenieść Maszynę wirtualną i jej zasobów w innej grupie zasobów, użyj [przeniesienia zasobu az](/cli/azure/resource#az_resource_move). Poniższy przykład przedstawia sposób przenoszenia maszyny Wirtualnej i najbardziej typowe zasoby, które wymaga. Użyj **— identyfikatory** parametr i przekaż listy rozdzielanej przecinkami (bez spacji) identyfikatorów zasobów do przeniesienia.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageacountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Jeśli chcesz przenieść maszynę Wirtualną i jej zasobów do innej subskrypcji, należy dodać **— identyfikator subskrypcji docelowej** parametr, aby określić subskrypcji docelowej.

Jeśli zostanie wyświetlona prośba o potwierdzenie, że chcesz przenieść określonego zasobu. Typ **Y** aby upewnić się, że chcesz przenieść zasoby.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Kolejne kroki
Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcje. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](../../resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).    

