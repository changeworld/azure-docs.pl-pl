---
title: Ruch sieciowy trasy — interfejs wiersza polecenia platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule dowiesz się, jak kierować ruch sieciowy za pomocą tabeli tras przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 5fa94b93e081ab6334c39b848068f50682f5f1f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235067"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Rozsyłanie ruchu sieciowego za pomocą tabeli tras przy użyciu interfejsu wiersza polecenia platformy Azure

Platforma Azure automatycznie domyślnie kieruje ruchem między wszystkimi podsieciami w sieci wirtualnej. Możesz tworzyć własne trasy zastępujące domyślne trasy platformy Azure. Możliwość tworzenia niestandardowych tras jest przydatna, jeśli na przykład chcesz kierować ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego (NVA). W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie tabeli tras
* Tworzenie trasy
* Tworzenie sieci wirtualnej z wieloma podsieciami
* Kojarzenie tabeli tras z podsiecią
* Tworzenie urządzenia NVA, które kieruje ruchem
* Wdrażanie maszyn wirtualnych w różnych podsieciach
* Kierowanie ruchem z jednej podsieci do drugiej za pomocą urządzenia NVA

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Przed utworzeniem tabeli marszruty należy utworzyć grupę zasobów z [utworzeniem grupy AZ](/cli/azure/group) dla wszystkich zasobów utworzonych w tym artykule. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Utwórz tabelę tras z [tworzeniem tabeli tras sieciowych az](/cli/azure/network/route-table#az-network-route-table-create). Poniższy przykład tworzy tabelę marszruty o nazwie *myRouteTablePublic*. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Tworzenie trasy

Utwórz trasę w tabeli tras z [az trasy tabeli tras sieciowych utworzyć](/cli/azure/network/route-table/route#az-network-route-table-route-create). 

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

Zanim skojarzysz tabelę tras z podsiecią, musisz utworzyć sieć wirtualną i podsieć. Utwórz sieć wirtualną za pomocą jednej podsieci z [siecią wirtualną AZ .](/cli/azure/network/vnet)

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Utwórz dwie dodatkowe podsieci za pomocą [sieci sieciowej az tworzenie podsieci .](/cli/azure/network/vnet/subnet)

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

Skojarz tabelę *marszruty myRouteTablePublic* z podsiecią *publiczną* z [aktualizacją podsieci sieci AZ](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Tworzenie urządzenia NVA

Urządzenie NVA jest maszyną wirtualną, która realizuje funkcje sieci, takie jak routing, zapora lub optymalizacja sieci WAN.

Utwórz urządzenie WUS w podsieci *DMZ* z [utworzeniem az vm](/cli/azure/vm). Podczas tworzenia maszyny Wirtualnej platforma Azure domyślnie tworzy i przypisuje publiczny adres IP do maszyny Wirtualnej. Parametr `--public-ip-address ""` instruuje platformę Azure, aby nie tworzyć i przypisywać publicznego adresu IP do maszyny Wirtualnej, ponieważ maszyna wirtualna nie musi być połączona z Internetem. Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie kontynuuj do następnego kroku, dopóki platforma Azure zakończy tworzenie maszyny Wirtualnej i zwraca dane wyjściowe dotyczące maszyny Wirtualnej. 

Aby karta sieciowa mogła przekazywać dalej wysyłany do niej ruch sieciowy, który nie jest przeznaczony dla jej własnego adresu IP, musi być włączone przekazywanie adresu IP dla interfejsu sieciowego. Włącz przekazywanie adresów IP dla interfejsu sieciowego za pomocą [aktualizacji sieciowej az](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

W ramach maszyny wirtualnej system operacyjny lub aplikacja działająca na maszynie wirtualnej musi także móc przekazywać dalej ruch sieciowy. Włącz przekazywanie adresów IP w systemie operacyjnym maszyny Wirtualnej za pomocą [zestawu rozszerzeń AZ VM:](/cli/azure/vm/extension)

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

Wykonanie polecenia może potrwać do minuty.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby sprawdzić, czy ruch z podsieci *publicznej* jest kierowany do podsieci *prywatnej* za pośrednictwem urządzenia wuszyniu w późniejszym kroku. 

Tworzenie maszyny Wirtualnej w podsieci *publicznej* za pomocą [az vm create](/cli/azure/vm). Parametr `--no-wait` umożliwia platformie Azure wykonanie polecenia w tle, dzięki czemu można przejść do następnego polecenia. Aby usprawnić ten artykuł, używane jest hasło. Klucze są zwykle używane w wdrożeniach produkcyjnych. Jeśli używasz kluczy, należy również skonfigurować przekazywanie agenta SSH. Aby uzyskać więcej informacji, zobacz dokumentację klienta SSH. Zastąp `<replace-with-your-password>` w poniższym poleceniu wybranym hasłem.

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

Utwórz maszynę wirtualną w podsieci *prywatnej.*

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

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny Wirtualnej interfejsu wiersza polecenia platformy Azure zawiera informacje podobne do następującego przykładu: 

```output
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

Zanotuj wartość adresu **publicIpAddress**. Ten adres jest używany do uzyskiwania dostępu do maszyny Wirtualnej z Internetu w późniejszym kroku.

## <a name="route-traffic-through-an-nva"></a>Kierowanie ruchem za pośrednictwem urządzenia NVA

Użyj następującego polecenia, aby utworzyć sesję SSH z *maszyną wirtualną myVmPrivate.* Zastąp * \<publicIpAddress>* publicznym adresem IP maszyny Wirtualnej. W powyższym przykładzie adres IP to *13.90.242.231*.

```bash
ssh azureuser@<publicIpAddress>
```

Po wyświetleniu monitu o podanie hasła wprowadź hasło wybrane w [obszarze Tworzenie maszyn wirtualnych](#create-virtual-machines).

Użyj następującego polecenia, aby zainstalować trasę śledzenia na *maszynie wirtualnej myVmPrivate:*

```bash
sudo apt-get install traceroute
```

Użyj następującego polecenia, aby przetestować routing dla ruchu sieciowego do maszyny Wirtualnej *myVmPublic* z maszyny wirtualnej *myVmPrivate.*

```bash
traceroute myVmPublic
```

Odpowiedź jest podobna do poniższego przykładu:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Widać, że ruch jest kierowany bezpośrednio z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic*. Trasy domyślne platformy Azure, kieruj ruch bezpośrednio między podsieciami. 

Użyj następującego polecenia do SSH do *myVmPublic* VM maszyny wirtualnej z *myVmPrivate* VM:

```bash
ssh azureuser@myVmPublic
```

Użyj następującego polecenia, aby zainstalować trasę śledzenia na maszynie wirtualnej *myVmPublic:*

```bash
sudo apt-get install traceroute
```

Użyj następującego polecenia, aby przetestować routing dla ruchu sieciowego do maszyny wirtualnej *myVmPrivate* z maszyny Wirtualnej *myVmPublic.*

```bash
traceroute myVmPrivate
```

Odpowiedź jest podobna do poniższego przykładu:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Teraz możesz zobaczyć, że pierwszym przeskokiem jest 10.0.2.4, czyli prywatny adres IP urządzenia NVA. Drugim przeskokiem jest 10.0.1.4, prywatny adres IP maszyny wirtualnej *myVmPrivate*. Trasa dodana do tabeli tras *myRouteTablePublic* i powiązana z podsiecią *Public* spowodowała, że platforma Azure skierowała ruch przez urządzenie NVA, a nie bezpośrednio do podsieci *Private*.

Zamknij sesje SSH zarówno na maszynach wirtualnych *myVmPublic,* jak i *myVmPrivate.*

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, użyj [az group delete,](/cli/azure/group) aby usunąć grupę zasobów i wszystkie zasoby, które zawiera.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono tabelę marszruty i skojarzyno ją z podsiecią. Utworzono proste urządzenie NVA, które kierowało ruch z podsieci publicznej do podsieci prywatnej. Wdrożono różne wstępnie skonfigurowane urządzenia NVA wykonujące funkcje sieciowe, takie jak zapora i optymalizacja sieci WAN, z witryny [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).

Chociaż możesz wdrożyć wiele zasobów platformy Azure w ramach sieci wirtualnej, zasobów dla niektórych usług PaaS platformy Azure nie można wdrożyć w sieci wirtualnej. Nadal możesz ograniczyć dostęp do zasobów niektórych usług PaaS platformy Azure tylko do ruchu z podsieci sieci wirtualnej. Aby dowiedzieć się, jak to zrobić, zobacz [Ograniczanie dostępu do sieci do zasobów PaaS](tutorial-restrict-network-access-to-resources-cli.md).
