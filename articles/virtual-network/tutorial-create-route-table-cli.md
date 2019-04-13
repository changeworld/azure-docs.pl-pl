---
title: Kierować ruchem sieciowym — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się, jak kierować ruchem sieciowym za pomocą tabeli tras z użyciem wiersza polecenia platformy Azure.
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
ms.openlocfilehash: e3106a792f486a62986734e562c96b41dd8a75dc
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524127"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Kierowanie ruchem sieciowym za pomocą tabeli tras z użyciem wiersza polecenia platformy Azure

Platforma Azure automatycznie domyślnie kieruje ruchem między wszystkimi podsieciami w sieci wirtualnej. Możesz tworzyć własne trasy zastępujące domyślne trasy platformy Azure. Możliwość tworzenia niestandardowych tras jest przydatna, jeśli na przykład chcesz kierować ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego (NVA). W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie tabeli tras
* Tworzenie trasy
* Tworzenie sieci wirtualnej z wieloma podsieciami
* Kojarzenie tabeli tras z podsiecią
* Tworzenie urządzenia NVA, które kieruje ruchem
* Wdrażanie maszyn wirtualnych w różnych podsieciach
* Kierowanie ruchem z jednej podsieci do drugiej za pomocą urządzenia NVA

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Zanim będzie można utworzyć tabelę tras, Utwórz grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group) dla wszystkich zasobów utworzonych w tym artykule. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Utwórz tabelę tras z [Tworzenie tabeli tras az sieci](/cli/azure/network/route-table#az-network-route-table-create). Poniższy przykład tworzy tabelę tras o nazwie *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Tworzenie trasy

Tworzenie trasy w tabeli tras z [tworzenie az sieci route-table route](/cli/azure/network/route-table/route#az-network-route-table-route-create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Zanim skojarzysz tabelę tras z podsiecią, musisz utworzyć sieć wirtualną i podsieć. Utwórz sieć wirtualną z jedną podsiecią z [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Utworzyć dwa dodatkowe podsieci przy użyciu [az podsieci sieci wirtualnej Utwórz](/cli/azure/network/vnet/subnet).

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

Skojarz *myRouteTablePublic* tabelę tras w celu *publicznych* podsieć o [aktualizacji podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Tworzenie urządzenia NVA

Urządzenie NVA jest maszyną wirtualną, która realizuje funkcje sieci, takie jak routing, zapora lub optymalizacja sieci WAN.

Tworzenie urządzenia NVA w *DMZ* podsieć o [tworzenie az vm](/cli/azure/vm). Podczas tworzenia maszyny Wirtualnej, platforma Azure utworzy i przypisanie publicznego adresu IP do maszyny Wirtualnej, domyślnie. `--public-ip-address ""` Parametru powoduje, że Azure nie można utworzyć i przypisać publiczny adres IP do maszyny Wirtualnej, ponieważ maszyna wirtualna nie musi być połączony z Internetem. Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

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

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie należy kontynuować do następnego kroku, dopóki nie zakończy tworzenia maszyny Wirtualnej platformy Azure i zwraca dane wyjściowe o maszynie Wirtualnej. 

Aby karta sieciowa mogła przekazywać dalej wysyłany do niej ruch sieciowy, który nie jest przeznaczony dla jej własnego adresu IP, musi być włączone przekazywanie adresu IP dla interfejsu sieciowego. Włączanie przekazywania adresów IP dla interfejsu sieciowego z [aktualizacji karty sieciowej sieci az](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

W ramach maszyny wirtualnej system operacyjny lub aplikacja działająca na maszynie wirtualnej musi także móc przekazywać dalej ruch sieciowy. Włączanie przekazywania adresów IP w ramach systemu operacyjnego maszyny Wirtualnej za pomocą [zestaw rozszerzeń maszyn wirtualnych az](/cli/azure/vm/extension):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
Polecenia może potrwać do minuty do wykonania.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej, dzięki czemu można sprawdzić, czy ruch z *publicznych* podsieci jest kierowany do *prywatnej* podsieci za pośrednictwem urządzenia NVA w późniejszym kroku. 

Utwórz Maszynę wirtualną w *publicznych* podsieć o [tworzenie az vm](/cli/azure/vm). `--no-wait` Parametr umożliwia platformie Azure można wykonać polecenia w tle, dzięki czemu można kontynuować do następnego polecenia. Aby uprościć ten artykuł, używane jest hasło. Klucze są zazwyczaj używane we wdrożeniach produkcyjnych. Jeśli używasz kluczy, należy również skonfigurować agenta przekazywania SSH. Aby uzyskać więcej informacji zobacz dokumentację używanego klienta SSH. Zastąp `<replace-with-your-password>` w następującym poleceniu za pomocą wybrane hasło.

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

Utwórz Maszynę wirtualną w *prywatnej* podsieci.

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

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny Wirtualnej platformy AZURE wyświetli informacje podobne do poniższego przykładu: 

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
Zanotuj wartość adresu **publicIpAddress**. Ten adres umożliwia dostęp do maszyny Wirtualnej z Internetu w późniejszym kroku.

## <a name="route-traffic-through-an-nva"></a>Kierowanie ruchem za pośrednictwem urządzenia NVA

Użyj następującego polecenia, aby utworzyć sesję SSH z *myVmPrivate* maszyny Wirtualnej. Zastąp  *\<publiczny adres IP >* z publicznym adresem IP swojej maszyny wirtualnej. W powyższym przykładzie adres IP jest *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po wyświetleniu monitu o hasło, wprowadź hasło wybrane w [Tworzenie maszyn wirtualnych](#create-virtual-machines).

Użyj następującego polecenia, aby zainstalować narzędzie śledzenia trasy na *myVmPrivate* maszyny Wirtualnej:

```bash 
sudo apt-get install traceroute
```

Użyj następującego polecenia, aby przetestować routing ruchu sieciowego do *myVmPublic* maszynę Wirtualną z *myVmPrivate* maszyny Wirtualnej.

```bash
traceroute myVmPublic
```

Odpowiedź jest podobna do poniższego przykładu:

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Widać, że ruch jest kierowany bezpośrednio z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic*. Domyślnych tras platformy, kierowanie ruchu bezpośrednio między podsieciami. 

Użyj następującego polecenia SSH *myVmPublic* maszynę Wirtualną z *myVmPrivate* maszyny Wirtualnej:

```bash 
ssh azureuser@myVmPublic
```

Użyj następującego polecenia, aby zainstalować narzędzie śledzenia trasy na *myVmPublic* maszyny Wirtualnej:

```bash 
sudo apt-get install traceroute
```

Użyj następującego polecenia, aby przetestować routing ruchu sieciowego do *myVmPrivate* maszynę Wirtualną z *myVmPublic* maszyny Wirtualnej.

```bash
traceroute myVmPrivate
```

Odpowiedź jest podobna do poniższego przykładu:

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Teraz możesz zobaczyć, że pierwszym przeskokiem jest 10.0.2.4, czyli prywatny adres IP urządzenia NVA. Drugim przeskokiem jest 10.0.1.4, prywatny adres IP maszyny wirtualnej *myVmPrivate*. Trasa dodana do tabeli tras *myRouteTablePublic* i powiązana z podsiecią *Public* spowodowała, że platforma Azure skierowała ruch przez urządzenie NVA, a nie bezpośrednio do podsieci *Private*.

Zamykanie sesji SSH do obu *myVmPublic* i *myVmPrivate* maszyn wirtualnych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, należy użyć [usunięcie grupy az](/cli/azure/group) Aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono tabelę tras i skojarzono ją z podsiecią. Utworzono proste urządzenie NVA, które kierowało ruch z podsieci publicznej do podsieci prywatnej. Wdrożono różne wstępnie skonfigurowane urządzenia NVA wykonujące funkcje sieciowe, takie jak zapora i optymalizacja sieci WAN, z witryny [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).

Chociaż możesz wdrożyć wiele zasobów platformy Azure w ramach sieci wirtualnej, zasobów dla niektórych usług PaaS platformy Azure nie można wdrożyć w sieci wirtualnej. Nadal możesz ograniczyć dostęp do zasobów niektórych usług PaaS platformy Azure tylko do ruchu z podsieci sieci wirtualnej. Aby dowiedzieć się więcej, zobacz temat [ograniczanie dostępu sieciowego do zasobów PaaS](tutorial-restrict-network-access-to-resources-cli.md).
