---
title: Tworzenie sieci wirtualnej platformy Azure - Azure CLI | Dokumentacja firmy Microsoft
description: Dowiedz się szybko utworzyć sieć wirtualną przy użyciu wiersza polecenia platformy Azure. Sieć wirtualna umożliwia zasobów platformy Azure, takich jak maszyny wirtualne do komunikowania się przez użytkowników ze sobą i z Internetu.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: d07f06a1a70c859544c3b1ceb6146dc11e4d10aa
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Utwórz sieć wirtualną przy użyciu wiersza polecenia platformy Azure

Sieci wirtualnej umożliwia zasobów platformy Azure, takich jak maszyn wirtualnych (VM) do prywatnie komunikowania się ze sobą i z Internetu. W tym artykule należy Dowiedz się, jak utworzyć sieć wirtualną. Po utworzeniu sieci wirtualnej, możesz wdrożyć dwóch maszyn wirtualnych w sieci wirtualnej. Następnie podłącz do jednej maszyny Wirtualnej z Internetu, a komunikacji przez użytkowników z innych maszyn wirtualnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli do zainstalowania i używania interfejsu wiersza polecenia lokalnie, w tym artykule, wymaga używasz interfejsu wiersza polecenia Azure w wersji 2.0.28 lub nowszej. Aby znaleźć zainstalowanej wersji, uruchom `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Przed utworzeniem sieci wirtualnej, należy utworzyć grupę zasobów, aby zawierała sieci wirtualnej. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Poniższy przykład tworzy domyślną sieci wirtualnej o nazwie *myVirtualNetwork* z jednej podsieci o nazwie *domyślne*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej:

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny Wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). Jeśli kluczy SSH już nie istnieją w domyślnej lokalizacji klucza, polecenie tworzy je. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. `--no-wait` Opcja tworzy maszynę Wirtualną w tle, dzięki czemu można kontynuować do następnego kroku. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVm1*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny Wirtualnej

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Maszyna wirtualna ma kilka minut na utworzenie. Po utworzeniu maszyny Wirtualnej Azure CLI zwraca dane wyjściowe podobne do poniższego przykładu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Zwróć uwagę na **publicznego adresu IP**. Ten adres jest używany do nawiązania połączenia z maszyną Wirtualną z Internetu w następnym kroku.

## <a name="connect-to-a-vm-from-the-internet"></a>Połączenie z maszyną wirtualną z Internetu

Zastąp `<publicIpAddress>` publiczny adres IP Twojego *myVm2* maszyny Wirtualnej w poleceniu następujące, a następnie wprowadź następujące polecenie:

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-privately-between-vms"></a>Prywatnie komunikacji między maszynami wirtualnymi

Aby potwierdzić prywatnej komunikacji między *myVm2* i *myVm1* maszyn wirtualnych, wprowadź następujące polecenie:

```bash
ping myVm1 -c 4
```

Otrzymasz cztery odpowiedzi z *10.0.0.4*.

Zakończyć sesję SSH z *myVm2* maszyny Wirtualnej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, można użyć [usunięcie grupy az](/cli/azure/group#az_group_delete) można usunąć grupy zasobów i wszystkie zasoby zawiera:

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule tworzone są domyślne sieci wirtualnej i dwie maszyny wirtualne. Połączone z jedną maszynę Wirtualną z Internetu i prywatnie przekazywane między maszyny Wirtualnej i inną maszynę Wirtualną. Aby dowiedzieć się więcej na temat ustawień sieci wirtualnej, zobacz [Zarządzanie sieci wirtualnej](manage-virtual-network.md). 

Domyślnie program Azure umożliwia nieograniczony prywatnej komunikacji między maszynami wirtualnymi, lecz tylko umożliwia sesje SSH przychodzące z Internetu maszyn wirtualnych systemu Linux. Aby dowiedzieć się ograniczyć różnego rodzaju komunikacji sieciowej do i z maszyn wirtualnych, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic-cli.md)
