---
title: Przenoszenie maszyny Wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
description: Przenoszenie maszyny Wirtualnej do innej subskrypcji platformy Azure lub grupy zasobów przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: ebcd5f166fd1876f67121787c23d23860c9fa4b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944600"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>Przenoszenie maszyny Wirtualnej do innej subskrypcji lub grupy zasobów
W tym artykule opisano, jak przenieść maszynę wirtualną (VM) między grupami zasobów lub subskrypcjami. Przenoszenie maszyny Wirtualnej między subskrypcjami może być przydatne, jeśli utworzono maszynę wirtualną w subskrypcji osobistej, a teraz chcesz przenieść ją do subskrypcji firmy.

> [!IMPORTANT]
>Nowe identyfikatory zasobów są tworzone jako część przenoszenia. Po przeniesieniu maszyny Wirtualnej należy zaktualizować narzędzia i skrypty, aby użyć nowych identyfikatorów zasobów.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>Przenoszenie maszyny Wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure


Aby można było przenieść maszynę wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure, należy upewnić się, że subskrypcje źródłowe i docelowe istnieją w ramach tej samej dzierżawy. Aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy, użyj [programu AZ Account Show](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Jeśli identyfikatory dzierżawy dla subskrypcji źródłowych i docelowych nie są takie same, należy skontaktować się z [pomocą techniczną,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) aby przenieść zasoby do nowej dzierżawy.

Aby pomyślnie przenieść maszynę wirtualną, należy przenieść maszynę wirtualną i wszystkie jej zasoby pomocnicze. Polecenie [az resource list](/cli/azure/resource) służy do wystawiania wszystkich zasobów w grupie zasobów i ich identyfikatorów. Pomaga potoku dane wyjściowe tego polecenia do pliku, dzięki czemu można skopiować i wkleić identyfikatory do późniejszych poleceń.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Aby przenieść maszynę wirtualną i jej zasoby do innej grupy zasobów, użyj [funkcji przenoszenia zasobów az](/cli/azure/resource). W poniższym przykładzie pokazano, jak przenieść maszynę wirtualną i najczęstsze zasoby, które wymaga. Użyj parametru **-ids** i przekaż na liście oddzielonych przecinkami (bez spacji) identyfikatorów, aby zasoby można przenieść.

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

Jeśli chcesz przenieść maszynę wirtualną i jej zasoby do innej subskrypcji, dodaj parametr **--destination-subscriptionId,** aby określić subskrypcję docelową.

Gdy zostaniesz poproszony o potwierdzenie, że chcesz przenieść określone zasoby, wprowadź **Y,** aby potwierdzić.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Następne kroki
Można przenieść wiele różnych typów zasobów między grupami zasobów i subskrypcji. Aby uzyskać więcej informacji, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
