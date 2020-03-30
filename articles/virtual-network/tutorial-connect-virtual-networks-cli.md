---
title: Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnej — interfejs wiersza polecenia platformy Azure
description: W tym artykule dowiesz się, jak połączyć sieci wirtualne z komunikacją równorzędową sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: aa2d75173b14e768a207336b54b3dc10a8c3ea5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235162"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Łączenie sieci wirtualnych z komunikacją równorzędnie sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Sieci wirtualne możesz łączyć ze sobą za pomocą komunikacji równorzędnej sieci wirtualnych. Po połączeniu sieci wirtualnych za pomocą komunikacji równorzędnej zasoby w obu sieciach wirtualnych mogą komunikować się ze sobą przy takim samym opóźnieniu i z taką samą przepustowością, jakby zasoby były w tej samej sieci wirtualnej. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie dwóch sieci wirtualnych
* Łączenie dwóch sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych
* Wdrażanie maszyny wirtualnej w każdej sieci wirtualnej
* Nawiązywanie komunikacji między maszynami wirtualnymi

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Tworzenie sieci wirtualnych

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów dla sieci wirtualnej i wszystkie inne zasoby utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork1* z prefiksem adresu *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Utwórz sieć wirtualną o nazwie *myVirtualNetwork2* z prefiksem adresu *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Tworzenia komunikacji równorzędnej sieci wirtualnych

Komunikacji równorzędnej są ustanawiane między identyfikatorami sieci wirtualnej, więc najpierw należy uzyskać identyfikator każdej sieci wirtualnej z [az network vnet show](/cli/azure/network/vnet) i przechowywać identyfikator w zmiennej.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Tworzenie komunikacji równorzędnej z *myVirtualNetwork1* do *myVirtualNetwork2* z [siecią sieci az równość sieci utworzyć](/cli/azure/network/vnet/peering). Jeśli `--allow-vnet-access` parametr nie jest określony, jest ustanawiana komunikacja równorzędna, ale nie może przepływać przez nią komunikacja.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

W danych wyjściowych zwróconych po wykonaniu poprzedniego polecenia widać, że **zainicjowano stan równorzędny** . *Initiated* Komunikacja równorzędna pozostaje w stanie *Inicjowane,* dopóki nie utworzysz komunikacji równorzędnej z *myVirtualNetwork2* do *myVirtualNetwork1*. Tworzenie peering z *myVirtualNetwork2* do *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

W danych wyjściowych zwróconych po wykonaniu poprzedniego polecenia widać, że **peeringState** jest *Połączony*. Platforma Azure zmieniła również stan komunikacji równorzędnej *myVirtualNetwork1-myVirtualNetwork2* w komunikacji równorzędnej *z connected*. Upewnij się, że stan komunikacji równorzędnej dla *myVirtualNetwork1-myVirtualNetwork2* peering zmieniono na *Połączony* z [siecią az vnet peering show](/cli/azure/network/vnet/peering).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Zasoby w jednej sieci wirtualnej nie mogą komunikować się z zasobami w innej sieci wirtualnej, dopóki **stan komunikacji równorzędnej** dla komunikacji równorzędnej w obu sieciach wirtualnych jest *Połączony*. 

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszynę wirtualną w każdej sieci wirtualnej, dzięki czemu będzie można komunikować się między nimi w kolejnym kroku.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVm1* w sieci wirtualnej *myVirtualNetwork1.* Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. Opcja `--no-wait` tworzy maszynę wirtualną w tle, dzięki czemu można przejść do następnego kroku.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Utwórz maszynę wirtualną w sieci wirtualnej *myVirtualNetwork2.*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny Wirtualnej interfejsu wiersza polecenia platformy Azure zawiera informacje podobne do następującego przykładu: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Zanotuj wartość adresu **publicIpAddress**. Ten adres jest używany do uzyskiwania dostępu do maszyny Wirtualnej z Internetu w późniejszym kroku.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną *myVm2.* Zamień `<publicIpAddress>` publiczny adres IP maszyny Wirtualnej. W poprzednim przykładzie publiczny adres IP to *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Ping maszyny Wirtualnej w *myVirtualNetwork1*.

```bash
ping 10.0.0.4 -c 4
```

Otrzymasz cztery odpowiedzi. 

Zamknij sesję SSH na maszynie wirtualnej *myVm2.* 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, użyj [az group delete,](/cli/azure/group) aby usunąć grupę zasobów i wszystkie zasoby, które zawiera.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano, jak połączyć dwie sieci w tym samym regionie platformy Azure z komunikacją równorzędna sieci wirtualnej. Możesz też nawiązać komunikację równorzędną między sieciami wirtualnymi w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region) i w [różnych subskrypcjach platformy Azure](create-peering-different-subscriptions.md#cli), a także utworzyć [projekty sieci w topologii gwiazdy](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) z komunikacją równorzędną. Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnych, zobacz [Virtual network peering overview (Omówienie komunikacji równorzędnej sieci wirtualnych)](virtual-network-peering-overview.md) i [Manage virtual network peerings (Zarządzanie komunikacją równorzędną sieci wirtualnych)](virtual-network-manage-peering.md).

Można [podłączyć własny komputer do sieci wirtualnej](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) za pośrednictwem sieci VPN i wchodzić w interakcje z zasobami w sieci wirtualnej lub w sieciach wirtualnych równorzędnych. Aby uzyskać skrypty wielokrotnego użytku, aby wykonać wiele zadań objętych artykułami sieci wirtualnej, zobacz [przykłady skryptów](cli-samples.md).
