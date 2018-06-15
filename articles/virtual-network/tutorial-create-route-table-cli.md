---
title: Kierować ruchem sieciowym - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się, jak można kierować ruchem sieciowym z tabelą tras za pomocą wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: eb4a28b5a57d7e301e800cd4ad87c56b7c5df6d2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841949"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Kierować ruchem sieciowym z tabelą tras za pomocą wiersza polecenia platformy Azure

Azure automatycznie tras ruchu między wszystkich podsieci w sieci wirtualnej, domyślnie. Można utworzyć trasy do przesłonięcia Azure routing domyślny. Możliwość tworzenia niestandardowych tras jest przydatne, jeśli na przykład chcesz kierować ruchem między podsieciami przez urządzenie wirtualne sieci (NVA). W tym artykule dowiesz się, jak:

* Utwórz tabelę tras
* Utwórz trasę
* Tworzenie sieci wirtualnej z wieloma podsieciami
* Skojarz tabelę tras z podsiecią
* Utwórz NVA, który przekierowuje ruch
* Wdrażanie maszyn wirtualnych (VM) w różnych podsieciach
* Kierować ruchem z jednej podsieci do drugiego za pomocą NVA

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli do zainstalowania i używania interfejsu wiersza polecenia lokalnie tego przewodnika Szybki Start, wymaga używasz interfejsu wiersza polecenia Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Utwórz tabelę tras

Przed utworzeniem tabelę tras, Utwórz grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create) dla wszystkich zasobów utworzone w tym artykule. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Utwórz tabelę tras z [utworzyć tabelę tras az sieci](/cli/azure/network/route#az_network_route_table_create). Poniższy przykład tworzy tabelę tras o nazwie *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Utwórz trasę

Utwórz trasę w tabeli tras z [utwórz trasę tabeli tras sieciowych az](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

## <a name="associate-a-route-table-to-a-subnet"></a>Skojarz tabelę tras z podsiecią

Aby móc skojarzyć tabelę tras z podsiecią, należy utworzyć sieć wirtualną i podsieć. Utwórz sieć wirtualną z jedną podsiecią z [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Utworzyć dwa dodatkowe podsieci z [Utwórz podsieć sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Skojarz *myRouteTablePublic* tabeli tras do *publicznego* podsieć o [zaktualizować podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Utwórz NVA

NVA jest maszynę Wirtualną, która pełni funkcję sieci, takich jak routing, zapory lub Optymalizacja sieci WAN.

Utwórz NVA w *DMZ* podsieć o [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Podczas tworzenia maszyny Wirtualnej Azure tworzy i przypisuje publicznego adresu IP do maszyny Wirtualnej, domyślnie. `--public-ip-address ""` Parametr nakazuje Azure nie można utworzyć i przypisać do publicznego adresu IP do maszyny Wirtualnej, ponieważ maszyna wirtualna nie musi być połączony z Internetem. Jeśli kluczy SSH już nie istnieją w domyślnej lokalizacji klucza, polecenie tworzy je. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

Maszyna wirtualna ma kilka minut na utworzenie. Nie należy kontynuować do następnego kroku, dopóki Azure zakończy tworzenie maszyny Wirtualnej i zwraca dane wyjściowe dotyczące maszyny Wirtualnej. 

Dla karty sieciowej można było przesłać wysyłane do niego, ruch sieciowy, który nie jest przeznaczone do adresu IP, przesyłanie dalej IP musi być włączony dla interfejsu sieciowego. Włącz przesyłanie dalej IP dla karty sieciowej z [aktualizacji kart sieciowych az](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

W ramach maszyny Wirtualnej systemu operacyjnego lub aplikacji działających w Maszynie wirtualnej, musi także do przesyłania dalej ruchu sieciowego. Włącz przesyłanie dalej IP w ramach systemu operacyjnego maszyny Wirtualnej z [zestaw rozszerzenia maszyny wirtualnej az](/cli/azure/vm/extension#az_vm_extension_set):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
Polecenie może potrwać kilka minut do wykonania.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby móc weryfikować tego ruchu z *publicznego* podsieci jest kierowany do *prywatnej* podsieci za pośrednictwem NVA w kolejnym kroku. 

Utwórz maszynę Wirtualną w *publicznego* podsieć o [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). `--no-wait` Parametru zapewnia Azure można wykonać polecenia w tle, dzięki czemu można kontynuować do następnego polecenia. Aby usprawnić w tym artykule, użyto hasła. Klucze są zazwyczaj używane w przypadku wdrożeń produkcyjnych. Użycie kluczy, należy również skonfigurować agenta przekazywania SSH. Aby uzyskać więcej informacji zobacz dokumentację klienta SSH. Zastąp `<replace-with-your-password>` w poniższym poleceniu hasłem wybrane.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Utwórz maszynę Wirtualną w *prywatnej* podsieci.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Maszyna wirtualna ma kilka minut na utworzenie. Po utworzeniu maszyny Wirtualnej, interfejsu wiersza polecenia Azure zawiera informacje podobne do poniższego przykładu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Zwróć uwagę na **publicznego adresu IP**. Ten adres jest używany na dostęp do maszyny Wirtualnej z Internetu w kolejnym kroku.

## <a name="route-traffic-through-an-nva"></a>Kierować ruchem za pośrednictwem NVA

Użyj następującego polecenia, aby utworzyć sesję SSH z *myVmPrivate* maszyny Wirtualnej. Zastąp *<publicIpAddress>* z publicznym adresem IP w sieci maszyny wirtualnej. W powyższym przykładzie adres IP jest *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po wyświetleniu monitu o podanie hasła, wprowadź hasło wybranym [Tworzenie maszyn wirtualnych](#create-virtual-machines).

Użyj następującego polecenia do zainstalowania na śledzenie trasy *myVmPrivate* maszyny Wirtualnej:

```bash 
sudo apt-get install traceroute
```

Użyj następującego polecenia, aby przetestować routingu ruchu sieciowego do *myVmPublic* maszyny Wirtualnej z *myVmPrivate* maszyny Wirtualnej.

```bash
traceroute myVmPublic
```

Odpowiedź jest podobny do poniższego przykładu:

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Widać, że ruch jest kierowany bezpośrednio z *myVmPrivate* maszyny Wirtualnej, aby *myVmPublic* maszyny Wirtualnej. Trasy domyślne platformy Azure, kierować ruchem między podsieciami. 

Użyj następującego polecenia do SSH *myVmPublic* maszyny Wirtualnej z *myVmPrivate* maszyny Wirtualnej:

```bash 
ssh azureuser@myVmPublic
```

Użyj następującego polecenia do zainstalowania na śledzenie trasy *myVmPublic* maszyny Wirtualnej:

```bash 
sudo apt-get install traceroute
```

Użyj następującego polecenia, aby przetestować routingu ruchu sieciowego do *myVmPrivate* maszyny Wirtualnej z *myVmPublic* maszyny Wirtualnej.

```bash
traceroute myVmPrivate
```

Odpowiedź jest podobny do poniższego przykładu:

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Widać, że pierwszym przeskoku jest 10.0.2.4, czyli NVA prywatnego adresu IP. Drugi przeskok jest 10.0.1.4, prywatnego adresu IP *myVmPrivate* maszyny Wirtualnej. Trasy dodane do *myRouteTablePublic* tabeli tras i powiązanych z *publicznego* podsieci spowodował Azure, aby kierować ruchem przez analizę NVA, a nie bezpośrednio do *prywatnego* podsieci.

Zamknięcie sesji SSH, zarówno *myVmPublic* i *myVmPrivate* maszyn wirtualnych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, użyj [usunięcie grupy az](/cli/azure/group#az_group_delete) można usunąć grupy zasobów i wszystkie zasoby zawiera.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule możesz utworzyć tabelę tras i skojarzone go do podsieci. Utworzono NVA proste, który kierowany ruch z publicznego podsieci prywatne podsieci. Wdrażanie różnych NVAs wstępnie skonfigurowane, wykonujących funkcji sieciowych, takich jak zapory i Optymalizacja sieci WAN z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Aby dowiedzieć się więcej na temat routingu, zobacz [Omówienie routingu](virtual-networks-udr-overview.md) i [Zarządzanie tabelę tras](manage-route-table.md).

Podczas wdrażania można wielu zasobów platformy Azure w ramach sieci wirtualnej, zasobów w przypadku niektórych usług Azure PaaS nie można wdrożyć w sieci wirtualnej. Możesz nadal ograniczyć dostęp do zasobów pewnych usług Azure PaaS ruch tylko z podsieci sieci wirtualnej jednak. Aby dowiedzieć się więcej, zobacz temat [ograniczenie dostępu do sieci do zasobów PaaS](tutorial-restrict-network-access-to-resources-cli.md).
