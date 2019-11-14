---
title: Tworzenie kompletnego środowiska systemu Linux przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure
description: Tworzenie magazynu, maszyny wirtualnej z systemem Linux, sieci wirtualnej i podsieci, modułu równoważenia obciążenia, karty sieciowej, publicznego adresu IP i sieciowej grupy zabezpieczeń, a wszystko to od podstaw przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 1ee89ce18600685f3f82bfb49d4d8ecbaf192b04
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036524"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Tworzenie kompletnego środowiska systemu Linux przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure
W tym artykule tworzymy prostą sieć z modułem równoważenia obciążenia oraz parę maszyn wirtualnych, które są przydatne do tworzenia i przetwarzania prostych danych. Analizujemy polecenie Process Command by, dopóki nie masz dwóch działających i bezpiecznych maszyn wirtualnych z systemem Linux, z którymi można się połączyć z dowolnego miejsca w Internecie. Następnie możesz przejść do bardziej złożonych sieci i środowisk.

W ten sposób poznasz hierarchię zależności, którą oferuje Menedżer zasobów model wdrażania, i o ile mocy zapewnia. Po zawieszeniu się, jak system został skompilowany, można ponownie skompilować go znacznie szybciej przy użyciu [szablonów Azure Resource Manager](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ponadto, po poznaniu współdziałania części środowiska, tworzenie szablonów służących do automatyzowania ich jest łatwiejsze.

Środowisko zawiera:

* Dwie maszyny wirtualne w zestawie dostępności.
* Moduł równoważenia obciążenia z regułą równoważenia obciążenia na porcie 80.
* Reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ochrony maszyny wirtualnej przed niechcianym ruchem.

Aby utworzyć to środowisko niestandardowe, potrzebujesz najnowszego [klasycznego interfejsu wiersza polecenia platformy Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) w trybie Menedżer zasobów (`azure config mode arm`). Potrzebujesz także narzędzia do analizy JSON. W tym przykładzie używa [JQ](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Klasyczny interfejs wiersza polecenia platformy Azure](#quick-commands) — nasz interfejs wiersza polecenia dla modeli wdrożeń klasycznych i zarządzania zasobami (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania usługi Resource Management


## <a name="quick-commands"></a>Szybkie polecenia
Jeśli musisz szybko wykonać zadanie, w poniższej sekcji przedstawiono podstawowe polecenia dotyczące przekazywania maszyny wirtualnej do platformy Azure. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć w pozostałej części dokumentu, zaczynając od tego [miejsca](#detailed-walkthrough).

Upewnij się, że masz zalogowanie [klasycznego interfejsu wiersza polecenia platformy Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i użycie trybu Menedżer zasobów:

```azurecli
azure config mode arm
```

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów obejmują `myResourceGroup`, `mystorageaccount`i `myVM`.

Utwórz grupę zasobów. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Sprawdź grupę zasobów przy użyciu analizatora JSON:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Utwórz konto magazynu. Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount`. (Nazwa konta magazynu musi być unikatowa, więc podaj własną unikatową nazwę).

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Sprawdź konto magazynu przy użyciu analizatora JSON:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Utwórz sieć wirtualną. Poniższy przykład tworzy sieć wirtualną o nazwie `myVnet`:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Utwórz podsieć. Poniższy przykład tworzy podsieć o nazwie `mySubnet`:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Sprawdź sieć wirtualną i podsieć przy użyciu analizatora JSON:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Utwórz publiczny adres IP. Poniższy przykład tworzy publiczny adres IP o nazwie `myPublicIP` z nazwą DNS `mypublicdns`. (Nazwa DNS musi być unikatowa, więc podaj własną unikatową nazwę).

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Utwórz moduł równoważenia obciążenia. Poniższy przykład tworzy moduł równoważenia obciążenia o nazwie `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Utwórz pulę adresów IP frontonu dla modułu równoważenia obciążenia i skojarz publiczny adres IP. Poniższy przykład tworzy pulę adresów IP frontonu o nazwie `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Utwórz pulę adresów IP zaplecza dla modułu równoważenia obciążenia. Poniższy przykład tworzy pulę adresów IP zaplecza o nazwie `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Utwórz przychodzące reguły translacji adresów sieciowych (NAT) protokołu SSH dla modułu równoważenia obciążenia. Poniższy przykład tworzy dwie reguły modułu równoważenia obciążenia, `myLoadBalancerRuleSSH1` i `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Utwórz reguły NAT ruchu przychodzącego sieci Web dla modułu równoważenia obciążenia. Poniższy przykład tworzy regułę modułu równoważenia obciążenia o nazwie `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Utwórz sondę kondycji modułu równoważenia obciążenia. Poniższy przykład tworzy sondę TCP o nazwie `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Sprawdź, czy moduł równoważenia obciążenia, pule adresów IP i reguły NAT są używane przy użyciu analizatora JSON:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Utwórz pierwszą kartę sieciową (NIC). Zastąp sekcje `#####-###-###` własnym IDENTYFIKATORem subskrypcji platformy Azure. Identyfikator subskrypcji jest zanotowany w danych wyjściowych **JQ** podczas badania tworzonych zasobów. Identyfikator subskrypcji można także wyświetlić za pomocą `azure account list`.

Poniższy przykład tworzy kartę sieciową o nazwie `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Utwórz drugą kartę sieciową. Poniższy przykład tworzy kartę sieciową o nazwie `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Sprawdź dwie karty sieciowe przy użyciu analizatora JSON:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Utwórz sieciową grupę zabezpieczeń. Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Dodaj dwie reguły ruchu przychodzącego dla sieciowej grupy zabezpieczeń. Poniższy przykład tworzy dwie reguły `myNetworkSecurityGroupRuleSSH` i `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Sprawdź sieciową grupę zabezpieczeń i reguły ruchu przychodzącego za pomocą analizatora JSON:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Powiąż grupę zabezpieczeń sieci z dwiema kartami interfejsu sieciowego:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Utwórz zestaw dostępności. Poniższy przykład tworzy zestaw dostępności o nazwie `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Utwórz pierwszą maszynę wirtualną z systemem Linux. Poniższy przykład tworzy maszynę wirtualną o nazwie `myVM1`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Utwórz drugą maszynę wirtualną z systemem Linux. Poniższy przykład tworzy maszynę wirtualną o nazwie `myVM2`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Użyj analizatora JSON, aby sprawdzić, czy wszystko zostało skompilowane:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Wyeksportuj nowe środowisko do szablonu, aby szybko utworzyć nowe wystąpienia:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik
Szczegółowe kroki, które należy wykonać, wyjaśniają, jakie poszczególne polecenia są wykonywane podczas tworzenia środowiska. Te pojęcia są przydatne podczas tworzenia własnych środowisk niestandardowych na potrzeby programowania lub produkcji.

Upewnij się, że masz zalogowanie [klasycznego interfejsu wiersza polecenia platformy Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i użycie trybu Menedżer zasobów:

```azurecli
azure config mode arm
```

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów obejmują `myResourceGroup`, `mystorageaccount`i `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Tworzenie grup zasobów i Wybieranie lokalizacji wdrożenia
Grupy zasobów platformy Azure są logicznymi jednostkami wdrażania, które zawierają informacje o konfiguracji i metadane umożliwiające logiczne Zarządzanie wdrożeniami zasobów. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Dane wyjściowe:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Wymagane są konta magazynu dla dysków maszyny wirtualnej i dla wszystkich dodatkowych dysków z danymi, które chcesz dodać. Konta magazynu można tworzyć niemal natychmiast po utworzeniu grup zasobów.

W tym miejscu użyjemy polecenia `azure storage account create`, przekazując lokalizację konta, grupę zasobów, która go kontroluje, i typ obsługiwanego magazynu. Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount`:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Dane wyjściowe:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Aby przejrzeć naszą grupę zasobów za pomocą polecenia `azure group show`, użyjmy narzędzia [JQ](https://stedolan.github.io/jq/) wraz z opcją `--json` interfejsu wiersza polecenia platformy Azure. (Możesz użyć **jsawk** lub dowolnej biblioteki języka, aby przeanalizować kod JSON).

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Dane wyjściowe:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Aby zbadać konto magazynu przy użyciu interfejsu wiersza polecenia, należy najpierw ustawić nazwy kont i kluczy. Zastąp nazwę konta magazynu w poniższym przykładzie nazwą, którą wybierzesz:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Możesz łatwo wyświetlić informacje o magazynie:

```azurecli
azure storage container list
```

Dane wyjściowe:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci
Następnie musisz utworzyć sieć wirtualną działającą na platformie Azure i podsieć, w której można tworzyć maszyny wirtualne. Poniższy przykład tworzy sieć wirtualną o nazwie `myVnet` z prefiksem adresu `192.168.0.0/16`:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Dane wyjściowe:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Ponownie Użyj opcji--JSON `azure group show` i `jq`, aby zobaczyć, w jaki sposób tworzysz zasoby. Mamy teraz zasób `storageAccounts` i zasób `virtualNetworks`.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Dane wyjściowe:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Teraz Utwórzmy podsieć w `myVnet` sieci wirtualnej, w której są wdrażane maszyny wirtualne. Używamy `azure network vnet subnet create` polecenia wraz z już utworzonymi zasobami: `myResourceGroup` grupie zasobów i `myVnet` sieci wirtualnej. W poniższym przykładzie dodamy podsieć o nazwie `mySubnet` z prefiksem adresu podsieci `192.168.1.0/24`:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Dane wyjściowe:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Ze względu na to, że podsieć jest logicznie w sieci wirtualnej, szukamy informacji o podsieci z nieco innym poleceniem. Używane polecenie jest `azure network vnet show`, ale nadal badamy dane wyjściowe JSON przy użyciu `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Dane wyjściowe:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP
Teraz Utwórzmy publiczny adres IP (PIP) przypisywany do modułu równoważenia obciążenia. Umożliwia nawiązanie połączenia z maszynami wirtualnymi z Internetu za pomocą polecenia `azure network public-ip create`. Ponieważ domyślny adres jest dynamiczny, w domenie **cloudapp.Azure.com** tworzymy wpis DNS o nazwie przy użyciu opcji `--domain-name-label`. Poniższy przykład tworzy publiczny adres IP o nazwie `myPublicIP` z nazwą DNS `mypublicdns`. Ponieważ nazwa DNS musi być unikatowa, należy podać własną unikatową nazwę DNS:

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Dane wyjściowe:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Publiczny adres IP jest również zasobem najwyższego poziomu, więc można go wyświetlić za pomocą `azure group show`.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Dane wyjściowe:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Więcej informacji o zasobach, w tym w pełni kwalifikowanej nazwie domeny (FQDN) poddomeny, można sprawdzić za pomocą polecenia Complete `azure network public-ip show`. Zasób publicznego adresu IP został przydzielony logicznie, ale określony adres nie został jeszcze przypisany. Aby uzyskać adres IP, należy potrzebować modułu równoważenia obciążenia, który jeszcze nie został utworzony.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Dane wyjściowe:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Tworzenie modułu równoważenia obciążenia i pul adresów IP
Podczas tworzenia modułu równoważenia obciążenia można rozpowszechnić ruch na wielu maszynach wirtualnych. Zapewnia również nadmiarowość aplikacji przez uruchamianie wielu maszyn wirtualnych, które odpowiadają na żądania użytkowników w przypadku konserwacji lub dużego obciążenia. Poniższy przykład tworzy moduł równoważenia obciążenia o nazwie `myLoadBalancer`:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Dane wyjściowe:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Nasz moduł równoważenia obciążenia jest dość pusty, więc Utwórzmy kilka pul adresów IP. Chcemy utworzyć dwie pule adresów IP dla naszego modułu równoważenia obciążenia, jeden dla frontonu i jeden dla zaplecza. Pula adresów IP frontonu jest publicznie widoczna. Jest również lokalizacją, do której przypiszemy utworzony wcześniej element PIP. Następnie używamy puli zaplecza jako lokalizacji dla maszyn wirtualnych, z którymi ma nawiązać połączenie. Dzięki temu ruch może przepływać przez moduł równoważenia obciążenia do maszyn wirtualnych.

Najpierw Utwórzmy pulę adresów IP frontonu. Poniższy przykład tworzy pulę frontonu o nazwie `myFrontEndPool`:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Dane wyjściowe:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Zwróć uwagę, w jaki sposób użyto przełącznika `--public-ip-name`, aby przejść do `myPublicIP` utworzonego wcześniej. Przypisanie publicznego adresu IP do modułu równoważenia obciążenia umożliwia nawiązanie połączenia z maszynami wirtualnymi za pośrednictwem Internetu.

Następnie Utwórzmy kolejną pulę adresów IP, tym razem dla ruchu zaplecza. Poniższy przykład tworzy pulę zaplecza o nazwie `myBackEndPool`:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Dane wyjściowe:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Możemy zobaczyć, jak działa nasz moduł równoważenia obciążenia, przeglądając `azure network lb show` i sprawdzając dane wyjściowe JSON:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Dane wyjściowe:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Tworzenie reguł translatora adresów sieciowych usługi równoważenia obciążenia
Aby uzyskać ruch przepływający przez nasz moduł równoważenia obciążenia, należy utworzyć reguły translatora adresów sieciowych (NAT) określające akcje przychodzące lub wychodzące. Można określić protokół, który ma być używany, a następnie mapować zewnętrzne porty na porty wewnętrzne zgodnie z potrzebami. W naszym środowisku utworzymy pewne reguły zezwalające na używanie protokołu SSH za pośrednictwem naszego modułu równoważenia obciążenia z maszynami wirtualnymi. Skonfigurujemy porty TCP 4222 i 4223, aby skierować port TCP 22 na naszych maszynach wirtualnych (którą utworzysz później). Poniższy przykład tworzy regułę o nazwie `myLoadBalancerRuleSSH1`, aby mapować port TCP 4222 na port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Dane wyjściowe:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Powtórz procedurę dla drugiej reguły NAT dla protokołu SSH. Poniższy przykład tworzy regułę o nazwie `myLoadBalancerRuleSSH2`, aby mapować port TCP 4223 na port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Można również utworzyć regułę NAT dla portu TCP 80 dla ruchu w sieci Web, podłączając regułę do naszych pul adresów IP. Jeśli odłączysz regułę do puli adresów IP, zamiast podłączać regułę do maszyn wirtualnych, można dodawać lub usuwać maszyny wirtualne z puli adresów IP. Moduł równoważenia obciążenia automatycznie dostosowuje przepływ ruchu. Poniższy przykład tworzy regułę o nazwie `myLoadBalancerRuleWeb`, aby mapować port TCP 80 na port 80:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Dane wyjściowe:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Tworzenie sondy kondycji modułu równoważenia obciążenia
Sonda kondycji okresowo sprawdza na maszynach wirtualnych, które znajdują się za naszym modułem równoważenia obciążenia, aby upewnić się, że działają i odpowiadają na żądania zgodnie z definicją. Jeśli nie, są one usuwane z operacji, aby upewnić się, że użytkownicy nie są kierowani do nich. Można zdefiniować niestandardowe sprawdzenia dla sondy kondycji, a także interwały i wartości limitu czasu. Aby uzyskać więcej informacji na temat sond kondycji, zobacz [Load Balancer sondy](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Poniższy przykład tworzy `myHealthProbe`sondowania kondycji TCP o nazwie:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Dane wyjściowe:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

W tym miejscu określono interwał 15 sekund dla naszych testów kondycji. Możemy pominąć maksymalnie cztery sondy (minutę), zanim moduł równoważenia obciążenia uzna, że host nie działa już.

## <a name="verify-the-load-balancer"></a>Weryfikowanie modułu równoważenia obciążenia
Teraz Konfiguracja modułu równoważenia obciążenia jest gotowa. Poniżej przedstawiono kroki, które należy wykonać:

1. Utworzono moduł równoważenia obciążenia.
2. Utworzono pulę adresów IP frontonu i przypisano do niej publiczny adres IP.
3. Utworzono pulę adresów IP zaplecza, z którą mogą się łączyć maszyny wirtualne.
4. Utworzono reguły NAT zezwalające na używanie protokołu SSH na maszynach wirtualnych, a także regułę, która zezwala na port TCP 80 dla naszej aplikacji sieci Web.
5. Dodano sondę kondycji, aby okresowo sprawdzać maszyny wirtualne. To sondowanie kondycji zapewnia, że użytkownicy nie próbują uzyskać dostępu do maszyny wirtualnej, która nie działa ani nie obsługuje zawartości.

Zapoznaj się z informacjami o tym, jak wygląda Twój moduł równoważenia obciążenia:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Dane wyjściowe:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Tworzenie karty sieciowej do użycia z maszyną wirtualną z systemem Linux
Karty sieciowe są dostępne programowo, ponieważ można stosować reguły do ich używania. Możesz również mieć więcej niż jeden. W poniższym poleceniu `azure network nic create` można podłączyć kartę sieciową do puli adresów IP zaplecza i skojarzyć ją z regułą NAT, aby zezwolić na ruch SSH.

Zastąp sekcje `#####-###-###` własnym IDENTYFIKATORem subskrypcji platformy Azure. Identyfikator subskrypcji jest zanotowany w danych wyjściowych `jq` podczas badania tworzonych zasobów. Identyfikator subskrypcji można także wyświetlić za pomocą `azure account list`.

Poniższy przykład tworzy kartę sieciową o nazwie `myNic1`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Dane wyjściowe:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Szczegóły można wyświetlić, sprawdzając zasób bezpośrednio. Zapoznaj się z zasobem za pomocą polecenia `azure network nic show`:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Dane wyjściowe:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Teraz tworzymy drugą kartę sieciową, przełączając się do puli adresów IP zaplecza. Tym razem druga reguła NAT zezwala na ruch SSH. Poniższy przykład tworzy kartę sieciową o nazwie `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Tworzenie sieciowej grupy zabezpieczeń i reguł
Teraz tworzymy sieciową grupę zabezpieczeń i reguły ruchu przychodzącego, które regulują dostęp do karty sieciowej. Do karty sieciowej lub podsieci można zastosować grupę zabezpieczeń sieci. Definiowane są reguły umożliwiające sterowanie przepływem ruchu do i z maszyn wirtualnych. Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Dodajmy regułę ruchu przychodzącego dla sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące na porcie 22 (do obsługi protokołu SSH). Poniższy przykład tworzy regułę o nazwie `myNetworkSecurityGroupRuleSSH`, aby zezwolić na ruch TCP na porcie 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Teraz Dodajmy regułę ruchu przychodzącego dla sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące na porcie 80 (do obsługi ruchu w sieci Web). Poniższy przykład tworzy regułę o nazwie `myNetworkSecurityGroupRuleHTTP`, aby zezwalać na ruch TCP na porcie 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> Reguła ruchu przychodzącego jest filtrem dla przychodzących połączeń sieciowych. W tym przykładzie powiążemy sieciowej grupy zabezpieczeń z wirtualną kartą sieciową maszyn wirtualnych, co oznacza, że każde żądanie do portu 22 jest przesyłane do karty sieciowej maszyny wirtualnej. Ta reguła ruchu przychodzącego dotyczy połączenia sieciowego, a nie informacji o punkcie końcowym, co w przypadku wdrożeń klasycznych. Aby otworzyć port, należy pozostawić `--source-port-range` ustawione na "\*" (wartość domyślna), aby akceptować żądania przychodzące z **dowolnego** portu żądającego. Porty są zwykle dynamiczne.
>
>

## <a name="bind-to-the-nic"></a>Powiąż z kartą sieciową
Powiąż sieciowej grupy zabezpieczeń z kartami sieciowymi. Musimy połączyć nasze karty sieciowe z naszą sieciową grupą zabezpieczeń. Uruchom Oba polecenia, aby podłączyć się do obu kart sieciowych:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności
Zestawy dostępności ułatwiają rozmieszczanie maszyn wirtualnych w domenach błędów i domenach uaktualniania. Utwórzmy zestaw dostępności dla maszyn wirtualnych. Poniższy przykład tworzy zestaw dostępności o nazwie `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domeny błędów definiują grupowanie maszyn wirtualnych, które współużytkują wspólne źródło i przełącznik sieciowy. Domyślnie maszyny wirtualne skonfigurowane w ramach zestawu dostępności są oddzielane do trzech domen błędów. Dobrym pomysłem jest to, że problem z sprzętem w jednej z tych domen błędów nie ma wpływu na każdą maszynę wirtualną, na której działa aplikacja. Platforma Azure automatycznie rozdziela maszyny wirtualne między domeny błędów, umieszczając je w zestawie dostępności.

Domeny uaktualnienia wskazują grupy maszyn wirtualnych i bazowego sprzętu fizycznego, które mogą być ponownie uruchamiane w tym samym czasie. Kolejność, w której domeny uaktualnień jest ponownie uruchamiana, może nie być sekwencyjna podczas planowanej konserwacji, ale tylko jeden z nich jest uruchamiany ponownie. Ponownie program Azure automatycznie dystrybuuje maszyny wirtualne między domenami uaktualniania, umieszczając je w lokacji dostępności.

Dowiedz się więcej o [zarządzaniu dostępnością maszyn wirtualnych](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Tworzenie maszyn wirtualnych z systemem Linux
Utworzono zasoby magazynu i sieci do obsługi maszyn wirtualnych dostępnych z Internetu. Teraz Utwórzmy te maszyny wirtualne i zabezpiecz je za pomocą klucza SSH, który nie ma hasła. W takim przypadku będziemy tworzyć maszyny wirtualne Ubuntu na podstawie najnowszych LTS. Informacje o obrazie można zlokalizować przy użyciu `azure vm image list`, zgodnie z opisem w artykule [Znajdowanie obrazów maszyn wirtualnych platformy Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Wybrano obraz przy użyciu polecenia `azure vm image list westeurope canonical | grep LTS`. W tym przypadku używamy `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Dla ostatniego pola przekazujemy `latest` tak, aby w przyszłości zawsze uzyskać najnowszą kompilację. (Używany ciąg jest `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Następnym krokiem jest zapoznanie się z każdą osobą, która utworzyła parę kluczy publicznych i prywatnych SSH RSA w systemie Linux lub Mac przy użyciu **protokołu ssh-keygen-t RSA-b 2048**. Jeśli nie masz żadnych par kluczy certyfikatu w katalogu `~/.ssh`, możesz je utworzyć:

* Automatycznie przy użyciu opcji `azure vm create --generate-ssh-keys`.
* Ręcznie przy użyciu [instrukcji, aby utworzyć je samodzielnie](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Alternatywnie można użyć metody `--admin-password` do uwierzytelniania połączeń SSH po utworzeniu maszyny wirtualnej. Ta metoda jest zazwyczaj mniej bezpieczna.

Utworzymy maszynę wirtualną, przenosząc wszystkie nasze zasoby i informacje przy użyciu polecenia `azure vm create`:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Dane wyjściowe:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Możesz natychmiast połączyć się z maszyną wirtualną przy użyciu domyślnych kluczy SSH. Upewnij się, że określisz odpowiedni port, ponieważ przekazujemy moduł równoważenia obciążenia. (W przypadku pierwszej maszyny wirtualnej skonfigurujemy regułę NAT do przesyłania dalej portu 4222 do naszej maszyny wirtualnej).

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Dane wyjściowe:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Przejdź dalej i Utwórz drugą maszynę wirtualną w taki sam sposób:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Możesz teraz użyć polecenia `azure vm show myResourceGroup myVM1`, aby przejrzeć utworzone elementy. W tym momencie korzystasz z maszyn wirtualnych Ubuntu za modułem równoważenia obciążenia na platformie Azure, do których można się zalogować tylko za pomocą pary kluczy SSH (ponieważ hasła są wyłączone). Można zainstalować Nginx lub http, wdrożyć aplikację internetową i sprawdzić przepływ ruchu przez moduł równoważenia obciążenia do obu maszyn wirtualnych.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Dane wyjściowe:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Eksportowanie środowiska jako szablonu
Teraz, gdy to środowisko zostało utworzone, co zrobić, jeśli chcesz utworzyć dodatkowe środowisko programistyczne z tymi samymi parametrami lub środowiskiem produkcyjnym, które je dopasowuje? Menedżer zasobów używa szablonów JSON, które definiują wszystkie parametry środowiska. Wszystkie środowiska można tworzyć, odwołując się do tego szablonu JSON. [Szablony JSON można kompilować ręcznie](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub wyeksportować istniejące środowisko, aby utworzyć szablon JSON:

```azurecli
azure group export --name myResourceGroup
```

To polecenie tworzy plik `myResourceGroup.json` w bieżącym katalogu roboczym. Po utworzeniu środowiska na podstawie tego szablonu zostanie wyświetlony monit dotyczący wszystkich nazw zasobów, w tym nazw modułu równoważenia obciążenia, interfejsów sieciowych lub maszyn wirtualnych. Można wypełnić te nazwy w pliku szablonu, dodając `-p` lub `--includeParameterDefaultValue` parametru do polecenia `azure group export`, które zostało pokazane wcześniej. Edytuj szablon JSON, aby określić nazwy zasobów, lub [Utwórz plik Parameters. JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , który określa nazwy zasobów.

Aby utworzyć środowisko na podstawie szablonu:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Warto zapoznać się z artykułem [więcej na temat wdrażania z szablonów](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Dowiedz się, jak stopniowo aktualizować środowiska, korzystać z pliku parametrów i uzyskiwać dostęp do szablonów z pojedynczej lokalizacji magazynu.

## <a name="next-steps"></a>Następne kroki
Teraz wszystko jest gotowe do rozpoczęcia pracy z wieloma składnikami sieciowymi i maszynami wirtualnymi. Możesz użyć tego przykładowego środowiska do kompilowania aplikacji przy użyciu najważniejszych składników wprowadzonych w tym miejscu.
