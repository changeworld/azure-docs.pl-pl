---
title: Utwórz pełną środowiska Linux z interfejsu wiersza polecenia platformy Azure w wersji 1.0 | Dokumentacja firmy Microsoft
description: Tworzenie magazynu, Maszynę wirtualną systemu Linux, sieci wirtualnej i podsieci, usługi równoważenia obciążenia, karty Sieciowej, publicznego adresu IP i sieciową grupę zabezpieczeń, wszystkie od podstaw przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 4a43e138d3497e01fe9e0e5c55a4a66adac767c6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-10"></a>Utwórz pełną środowiska Linux z interfejsu wiersza polecenia platformy Azure w wersji 1.0
W tym artykule budujemy prosta sieć z modułem równoważenia obciążenia i para maszyn wirtualnych, które są przydatne w przypadku tworzenia i przetwarzania proste. Firma Microsoft przeprowadzenie procesu polecenie, dopóki nie uzyskasz dwóch pracy, bezpieczne maszyn wirtualnych systemu Linux których możesz połączyć z dowolnego miejsca w Internecie. Następnie możesz przejść do bardziej złożone sieci i środowiskach.

Przeglądania zapoznanie się z hierarchią zależności modelu wdrażania usługi Resource Manager umożliwia, czy o ile włączenie go zawiera. Po jak system jest oparty, można go znacznie szybciej odtworzyć za pomocą [szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ponadto po dowiesz się, jak części środowiska dopasowania, tworzenie szablonów można zautomatyzować je staje się łatwiejsze.

Środowisko zawiera:

* Dwie maszyny wirtualne w zestawie dostępności.
* Usługi równoważenia obciążenia z regułą równoważenia obciążenia na porcie 80.
* Zasady zabezpieczeń grupy (NSG) ochrony maszyny Wirtualnej niepożądanego ruchu w sieci.

Aby utworzyć niestandardowe środowisko, należy najnowszej [Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) w trybie Menedżera zasobów (`azure config mode arm`). Należy również JSON, narzędzie do analizy. W tym przykładzie użyto [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#quick-commands) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami


## <a name="quick-commands"></a>Szybkie polecenia
Jeśli chcesz szybko wykonać zadanie, następujące szczegóły sekcji bazie polecenia do przekazania Maszynę wirtualną na platformie Azure. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć w pozostałej części dokumentu, początkowa [tutaj](#detailed-walkthrough).

Upewnij się, że masz [1.0 interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zalogowany i w trybie Menedżera zasobów:

```azurecli
azure config mode arm
```

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametru zawierają `myResourceGroup`, `mystorageaccount`, i `myVM`.

Utwórz grupę zasobów. Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w `westeurope` lokalizacji:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Sprawdź grupy zasobów, przy użyciu analizatora składni JSON:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Tworzenie konta magazynu. Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount`. (Nazwa konta magazynu musi być unikatowa, aby podać własną unikatową nazwę.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Sprawdź konto magazynu przy użyciu analizatora składni JSON:

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

Sprawdź sieć wirtualna i podsieć, za pomocą analizatora składni JSON:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Tworzenie publicznego adresu IP. Poniższy przykład tworzy publicznego adresu IP o nazwie `myPublicIP` o nazwie DNS `mypublicdns`. (Nazwa DNS musi być unikatowa, aby podać własną unikatową nazwę.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Tworzenie usługi równoważenia obciążenia. Poniższy przykład tworzy moduł równoważenia obciążenia o nazwie `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Utwórz pulę IP frontonu dla usługi równoważenia obciążenia i skojarz publicznego adresu IP. Poniższy przykład tworzy frontonu pula IP o nazwie `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Tworzenie puli adresów IP zaplecza dla usługi równoważenia obciążenia. Poniższy przykład tworzy pula IP zaplecza, o nazwie `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Utwórz sieć dla ruchu przychodzącego SSH reguły translacji adresów adres usługi równoważenia obciążenia. Poniższy przykład tworzy dwie reguły modułu równoważenia obciążenia, `myLoadBalancerRuleSSH1` i `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Utwórz sieć web przychodzącej reguły NAT modułu równoważenia obciążenia. Poniższy przykład tworzy regułę równoważenia obciążenia o nazwie `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Utwórz sondy kondycji modułu równoważenia obciążenia. Poniższy przykład tworzy sondowaniem TCP o nazwie `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Sprawdź usługi równoważenia obciążenia, pule adresów IP i reguł NAT, za pomocą analizatora składni JSON:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Tworzenie pierwszej karty interfejsu sieciowego (NIC). Zastąp `#####-###-###` sekcje z identyfikatorem subskrypcji platformy Azure Identyfikator jest odnotowany w danych wyjściowych subskrypcji **jq** podczas badania tworzysz zasoby. Można również wyświetlić identyfikator subskrypcji z `azure account list`.

Poniższy przykład tworzy karty Sieciowej o nazwie `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Tworzenie drugiej karty sieciowej. Poniższy przykład tworzy karty Sieciowej o nazwie `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Sprawdź dwie karty sieciowe za pomocą analizatora składni JSON:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Utwórz grupę zabezpieczeń sieci. Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Dodaj dwie reguły ruchu przychodzącego dla grupy zabezpieczeń sieci. Poniższy przykład tworzy dwie reguły `myNetworkSecurityGroupRuleSSH` i `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Sprawdź grupy zabezpieczeń sieci i reguł ruchu przychodzącego, za pomocą analizatora składni JSON:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Powiąż sieciową grupę zabezpieczeń z dwiema kartami sieciowymi:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Tworzenie zestawu dostępności. Poniższy przykład tworzy zbiór nazwanego dostępności `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Tworzenie pierwszej maszyny Wirtualnej systemu Linux. Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM1`:

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

Tworzenie drugiej maszyny Wirtualnej systemu Linux. Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM2`:

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

Użyj analizatora składni JSON do sprawdzenia, czy wszystko, który został utworzony:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Wyeksportuj nowe środowisko do szablonu w celu szybkiego ponownego tworzenia nowych wystąpień:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik
Szczegółowy opis kroków, które należy wykonać wyjaśnić, każdego polecenia czynności podczas tworzenia Twoje środowisko. Te pojęcia są przydatne podczas tworzenia własnego niestandardowego środowiska dla rozwoju lub produkcji.

Upewnij się, że masz [1.0 interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zalogowany i w trybie Menedżera zasobów:

```azurecli
azure config mode arm
```

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametru zawierają `myResourceGroup`, `mystorageaccount`, i `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Tworzenie grupy zasobów, a następnie wybierz pozycję lokalizacji wdrożenia
Grupy zasobów platformy Azure są jednostki wdrażania, które zawierają informacje o konfiguracji i metadanych, aby umożliwić zarządzanie logicznej wdrożenia zasobów. Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w `westeurope` lokalizacji:

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
Należy korzystać z kont magazynu dla dysków maszyny Wirtualnej i dla wszystkich dysków dodatkowe dane, które chcesz dodać. Możesz utworzyć konta magazynu prawie natychmiast po utworzeniu grup zasobów.

W tym miejscu użyjemy `azure storage account create` polecenia przekazywanie Lokalizacja konta, grupy zasobów, która kontroluje, a typ obsługi magazynu ma. Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount`:

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

Do sprawdzenia naszej grupy zasobów za pomocą `azure group show` polecenia, można użyć [jq](https://stedolan.github.io/jq/) narzędzia wraz z programem `--json` opcji wiersza polecenia platformy Azure. (Można użyć **jsawk** lub żadnej biblioteki języka aby przeanalizować dane JSON.)

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

Do sprawdzania, czy konto magazynu przy użyciu interfejsu wiersza polecenia, należy najpierw ustawić nazwy kont i kluczy. Zastąp nazwę, która zostanie wybrana nazwa konta magazynu w poniższym przykładzie:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Następnie można wyświetlić informacje o pamięci masowej łatwo:

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
Następnie możesz zacząć należy utworzyć sieć wirtualną z systemem Azure i podsieć, w którym można utworzyć maszyny wirtualne. Poniższy przykład tworzy sieć wirtualną o nazwie `myVnet` z `192.168.0.0/16` prefiks adresu:

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

Ponownie, możemy użyć opcji--json `azure group show` i `jq` aby zobaczyć, jak tworzymy naszych zasobów. Teraz `storageAccounts` zasobów i `virtualNetworks` zasobów.  

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

Teraz Utwórzmy podsieci `myVnet` sieci wirtualnej, w której maszyny wirtualne są wdrażane. Używamy `azure network vnet subnet create` polecenia wraz z zasobów już utworzyliśmy: `myResourceGroup` grupy zasobów i `myVnet` sieci wirtualnej. W poniższym przykładzie dodamy podsieci o nazwie `mySubnet` z prefiksem adresu podsieci `192.168.1.0/24`:

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

Ponieważ podsieci logicznie znajduje się w sieci wirtualnej, firma Microsoft poszukaj informacji o podsieci za pomocą polecenia nieco inne. Polecenie używamy jest `azure network vnet show`, ale w dalszym ciągu Sprawdź dane wyjściowe JSON przy użyciu `jq`.

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
Teraz Utwórzmy publicznego adresu IP (PIP), na który mamy przypisywać przez moduł równoważenia obciążenia. Umożliwia łączenie się z maszyn wirtualnych z Internetu przy użyciu `azure network public-ip create` polecenia. Ponieważ domyślnym adresem jest dynamiczny, utworzymy nazwanego wpisu DNS w **cloudapp.azure.com** domeny przy użyciu `--domain-name-label` opcji. Poniższy przykład tworzy publicznego adresu IP o nazwie `myPublicIP` o nazwie DNS `mypublicdns`. Nazwa DNS musi być unikatowa, należy dostarczyć własną unikatową nazwę DNS:

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

Publiczny adres IP jest również zasobem najwyższego poziomu, tak aby było widać, z `azure group show`.

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

Możesz przejrzeć szczegółowe zasobów, włącznie z w pełni kwalifikowaną nazwę (FQDN) domeny podrzędnej, korzystając z pełną `azure network public-ip show` polecenia. Została przydzielona logicznie zasobu publicznego adresu IP, ale nie został jeszcze przypisany określony adres. Aby uzyskać adres IP, możesz zacząć wymagają modułu równoważenia obciążenia, które firma Microsoft nie został jeszcze utworzony.

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

## <a name="create-a-load-balancer-and-ip-pools"></a>Tworzenie modułu równoważenia obciążenia i pule adresów IP
Podczas tworzenia modułu równoważenia obciążenia, umożliwia rozłożenie ruchu między wieloma maszynami wirtualnymi. Umożliwia także nadmiarowości aplikacji, uruchamiając wiele maszyn wirtualnych, które odpowiadają na żądania użytkownika w przypadku obsługi lub dużym obciążeniem. Poniższy przykład tworzy moduł równoważenia obciążenia o nazwie `myLoadBalancer`:

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

Naszej usługi równoważenia obciążenia jest dość pusty, więc warto utworzyć niektóre pule adresów IP. Chcemy, aby utworzyć dwie pule IP naszej usługi równoważenia obciążenia, jeden dla frontonu i jeden dla wewnętrznej. Puli adresów IP frontonu jest publicznie widoczna. Istnieje również lokalizacji, do którego możemy przypisać PIP, który utworzony wcześniej. Następnie używamy puli zaplecza jako lokalizacji dla naszej maszyn wirtualnych do nawiązania połączenia. W ten sposób przepływ ruchu za pośrednictwem usługi równoważenia obciążenia do maszyn wirtualnych.

Najpierw utwórz naszych frontonu puli adresów IP. Poniższy przykład tworzy puli frontonu o nazwie `myFrontEndPool`:

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

Należy zwrócić uwagę, jak użyliśmy `--public-ip-name` przełącznik, aby przekazać `myPublicIP` utworzonej wcześniej. Przypisywanie publicznego adresu IP do modułu równoważenia obciążenia można osiągnąć maszyn wirtualnych przez Internet.

Następnie Utwórzmy naszych drugi pula IP teraz naszych ruchu zaplecza. Poniższy przykład tworzy puli zaplecza, o nazwie `myBackEndPool`:

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

Zobaczysz, jak robi naszej usługi równoważenia obciążenia przez wyszukiwanie z `azure network lb show` i badanie danych wyjściowych JSON:

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

## <a name="create-load-balancer-nat-rules"></a>Tworzenie reguł NAT modułu równoważenia obciążenia
Aby uzyskać ruchu przepływających przez naszych modułu równoważenia obciążenia, należy utworzyć adres sieciowy reguł translacji adresów, które Określ akcje ruchu przychodzącego lub wychodzącego. Należy określić protokół, który ma być używany, a następnie mapują porty zewnętrznych do wewnętrznych portów zgodnie z potrzebami. W naszym środowisku Utwórzmy niektórych reguł, które umożliwiają SSH za pomocą naszej usługi równoważenia obciążenia do naszej maszyn wirtualnych. Skonfigurowanie portów TCP 4222 i 4223 do przekierowania do portu TCP 22 na naszych maszyn wirtualnych (które możemy utworzyć później). Poniższy przykład tworzy reguły o nazwie `myLoadBalancerRuleSSH1` mapować TCP port 4222 port 22:

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

Powtórz procedurę drugi reguły NAT dla SSH. Poniższy przykład tworzy reguły o nazwie `myLoadBalancerRuleSSH2` mapować TCP port 4223 port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Umożliwia również Przejdź dalej i utworzyć regułę NAT dla portu TCP 80 dla ruchu w sieci web, przechwytywanie reguły do naszej pule adresów IP. Jeśli firma Microsoft Podłączanie reguły pula IP, a Podłączanie reguły do naszej maszyn wirtualnych pojedynczo, firma Microsoft można dodawać i usuwać maszyn wirtualnych z puli adresów IP. Moduł równoważenia obciążenia jest automatycznie dostosowuje przepływu ruchu. Poniższy przykład tworzy reguły o nazwie `myLoadBalancerRuleWeb` mapować TCP port 80 z portem 80:

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
Kondycję sondowania okresowo sprawdza na maszynach wirtualnych, które znajdują się za naszej usługi równoważenia obciążenia, aby upewnić się, ich działania i odpowiada na żądania, zgodnie z definicją. Jeśli nie, usuwane z operacji, aby upewnić się, że użytkownicy nie są być kierowany do nich. Można zdefiniować niestandardowe kontroli sondy kondycji, oraz odstępach czasu i wartości limitu czasu. Aby uzyskać więcej informacji na temat sondy kondycji, zobacz [sondy modułu równoważenia obciążenia](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Poniższy przykład tworzy TCP kondycji sondowany o nazwie `myHealthProbe`:

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

W tym miejscu możemy określony interwał 15 sekund dla naszych kontroli kondycji. Firma Microsoft może pominąć maksymalnie cztery sond (jedną minutę), zanim usługi równoważenia obciążenia uzna, że host nie działa.

## <a name="verify-the-load-balancer"></a>Sprawdź usługę równoważenia obciążenia
Po zakończeniu konfiguracji usługi równoważenia obciążenia. Poniżej przedstawiono kroki wykonane:

1. Utworzono usługę równoważenia obciążenia.
2. Utworzyć pulę IP frontonu i przypisane do publicznego adresu IP.
3. Została utworzona pula adresów IP zaplecza, która maszyny wirtualne mogą nawiązać połączenie.
4. Utworzono reguł NAT, zezwalających na SSH z maszynami wirtualnymi zarządzania, oraz regułę zezwalającą port TCP 80 dla aplikacji sieci web.
5. Po dodaniu sondy kondycji, aby okresowo sprawdzać maszyn wirtualnych. To sondowanie kondycji gwarantuje, że użytkownicy nie próbują uzyskać dostęp maszynę Wirtualną, która jest już nie działa lub obsługę zawartości.

Umożliwia przeglądanie jak teraz wygląda przez moduł równoważenia obciążenia:

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

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Utwórz kartę Sieciową do użycia z maszyny Wirtualnej systemu Linux
Karty sieciowe są programowo dostępne, ponieważ reguły można stosować do ich używania. Może także zawierać więcej niż jeden. W następujących `azure network nic create` poleceń, podłącz kartę Sieciową do puli adresów IP zaplecza obciążenia i powiązać ją z reguły NAT, aby zezwolić na ruch protokołu SSH.

Zastąp `#####-###-###` sekcje z identyfikatorem subskrypcji platformy Azure Identyfikator jest odnotowany w danych wyjściowych subskrypcji `jq` podczas badania tworzysz zasoby. Można również wyświetlić identyfikator subskrypcji z `azure account list`.

Poniższy przykład tworzy karty Sieciowej o nazwie `myNic1`:

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

Można wyświetlić szczegóły, sprawdzając zasobu bezpośrednio. Zasób należy zbadać za pomocą `azure network nic show` polecenia:

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

Teraz utworzymy drugiej karty Sieciowej, przechwytywanie w ponownie do naszej puli adresów IP zaplecza. Ta reguła NAT czasu druga zezwala na ruch protokołu SSH. Poniższy przykład tworzy karty Sieciowej o nazwie `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Utwórz grupę zabezpieczeń sieci i reguł
Teraz utworzymy sieciowej grupy zabezpieczeń i reguł ruchu przychodzącego, które kontrolują dostęp do karty sieciowej. Grupa zabezpieczeń sieci może odnosić się do karty Sieciowej lub podsieci. Należy zdefiniować regułę do sterowania przepływem ruch do i z maszyn wirtualnych. Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Dodajmy regułę ruchu przychodzącego dla grupy NSG zezwolić na połączenia przychodzące do portu 22 (do obsługi protokołu SSH). Poniższy przykład tworzy reguły o nazwie `myNetworkSecurityGroupRuleSSH` aby umożliwić ruch TCP na porcie 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Teraz możemy dodać regułę ruchu przychodzącego dla grupy NSG zezwolić na połączenia przychodzące na porcie 80 (dla obsługi ruchu w sieci web). Poniższy przykład tworzy reguły o nazwie `myNetworkSecurityGroupRuleHTTP` aby umożliwić ruch TCP na porcie 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> Reguła ruchu przychodzącego jest filtrem dla połączeń sieciowych dla ruchu przychodzącego. W tym przykładzie firma Microsoft powiązać grupa NSG maszyn wirtualnych wirtualnej karty Sieciowej, co oznacza, że każde żądanie port 22 jest przekazywana do karty interfejsu Sieciowego w przypadku naszego maszyny Wirtualnej. Tę regułę dla ruchu przychodzącego jest o połączenie sieciowe, a nie o punkt końcowy, który jest co byłoby o we wdrożeniach klasycznych. Aby otworzyć port, należy pozostawić `--source-port-range` ustawioną "\*" (wartość domyślna) do akceptowania żądań przychodzących od **żadnych** żąda portu. Porty są zwykle dynamiczne.
>
>

## <a name="bind-to-the-nic"></a>Powiązanie z kartą sieciową
Grupa NSG należy powiązać karty interfejsu sieciowego. Należy połączyć z naszych kart sieciowych z naszej grupy zabezpieczeń sieci. Uruchom zarówno polecenia, aby Podłączanie obu naszych kart sieciowych:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności
Zestawy dostępności rozpowszechniania pomocy maszyn wirtualnych w domenach awarii i domen uaktualnienia. Utwórz zbiór dostępności dla maszyn wirtualnych. Poniższy przykład tworzy zbiór nazwanego dostępności `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domen błędów Definiowanie grup maszyn wirtualnych, które współużytkują wspólne przełącznik źródła i sieci zasilania. Domyślnie maszyny wirtualne, które są skonfigurowane w zestawie dostępności są rozdzielone przez maksymalnie trzy domen błędów. Ma to problemem sprzętowym w jednym z tych domen błędów nie wpływa na każdej maszynie Wirtualnej, która jest uruchomiona aplikacja. Azure automatycznie dystrybuuje maszyn wirtualnych w domenach awarii podczas umieszczania ich w zestawie dostępności.

Domen uaktualnienia wskazują grupy maszyn wirtualnych i podstawowym sprzętem fizycznym, który może zostać uruchomiony ponownie w tym samym czasie. Kolejność, w którym są ponownie uruchamiane domen uaktualnienia nie może być sekwencyjnych podczas zaplanowanej konserwacji, ale tylko jedno uaktualnienie ponownego uruchomienia w czasie. Ponownie Azure automatycznie dystrybuuje maszyn wirtualnych między domenami uaktualnienia podczas umieszczania ich w witrynie dostępności.

Przeczytaj więcej na temat [Zarządzanie dostępność maszyn wirtualnych](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Tworzenie maszyn wirtualnych systemu Linux
Po utworzeniu magazynu i zasobów sieciowych do obsługi maszyn wirtualnych dostęp do Internetu. Teraz załóżmy tworzenie tych maszyn wirtualnych i zabezpieczenia przy użyciu klucza SSH, który nie ma hasła. W takim przypadku zamierzamy utworzyć Ubuntu oparte na najnowszych LTS maszyny Wirtualnej. Możemy zlokalizować informacji obrazu przy użyciu `azure vm image list`, zgodnie z opisem w [znajdowanie obrazów maszyn wirtualnych Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Wybraliśmy obrazu za pomocą polecenia `azure vm image list westeurope canonical | grep LTS`. W takim przypadku stosujemy `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. W polu ostatniego jest przekazywana `latest` tak, aby w przyszłości zawsze uzyskujemy ostatniej kompilacji. (Jest ciągiem używamy `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Ten krok dalej jest znany dla każdego, kto został już utworzony ssh publicznego i prywatnego klucza rsa pary w systemie Linux lub Mac za pomocą **ssh-keygen - t rsa -b 2048**. Jeśli nie masz żadnych certyfikatów pary kluczy Twojej `~/.ssh` katalogu, można je utworzyć:

* Automatycznie, przy użyciu `azure vm create --generate-ssh-keys` opcji.
* Ręcznie, używając [instrukcje, aby utworzyć samodzielnie](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Alternatywnie można użyć `--admin-password` metody uwierzytelniania połączeń SSH, po utworzeniu maszyny Wirtualnej. Ta metoda jest zazwyczaj mniej bezpieczna.

Utwórz maszynę Wirtualną przełączając naszych zasobów i informacji razem z `azure vm create` polecenia:

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

Podłączeniem do maszyny Wirtualnej bezpośrednio za pomocą kluczy SSH domyślne. Upewnij się, określ odpowiedni port, ponieważ firma Microsoft jest przekazanie za pośrednictwem usługi równoważenia obciążenia. (Dla naszych pierwsza maszyna wirtualna, możemy skonfigurować regułę NAT do przesyłania dalej portu 4222 do naszej maszyny Wirtualnej.)

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
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Przejdź dalej i utworzyć drugi maszyny Wirtualnej w taki sam sposób:

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

I można teraz używać `azure vm show myResourceGroup myVM1` polecenie, aby sprawdzić, jakie zostały utworzone. W tym momencie używasz Ubuntu maszyn wirtualnych za modułem równoważenia obciążenia w Azure, który użytkownik może zalogować się do tylko z Twojej pary kluczy SSH (ponieważ haseł są wyłączone). Można zainstalować nginx lub host z wieloma adresami, wdrażanie aplikacji sieci web i widoczne ruchu przepływ przez moduł równoważenia obciążenia do obu maszyn wirtualnych.

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


## <a name="export-the-environment-as-a-template"></a>Eksportuj środowiska jako szablon
Teraz, dlatego utworzone to środowisko, co zrobić, jeśli chcesz utworzyć środowisko projektowe dodatkowych z takimi samymi parametrami lub środowiska produkcyjnego, odpowiadający jej? Menedżer zasobów używa szablony JSON, które definiują wszystkie parametry dla danego środowiska. Limit całego środowiska kompilacji, umieszczając odwołanie do tego szablonu JSON. Możesz [ręcznie utworzyć szablony JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub wyeksportować utworzenie szablonu JSON do istniejącego środowiska:

```azurecli
azure group export --name myResourceGroup
```

To polecenie tworzy `myResourceGroup.json` plik w bieżącym katalogu roboczym. Podczas tworzenia środowiska z tego szablonu, wyświetlany jest monit dla wszystkich nazw zasobów, łącznie z nazwami dla usługi równoważenia obciążenia, interfejsów sieciowych lub maszyn wirtualnych. Można go wypełnić te nazwy w pliku szablonu, dodając `-p` lub `--includeParameterDefaultValue` parametr `azure group export` polecenia, która została przedstawiona wcześniej. Edytuj szablon JSON można określić nazwy zasobu lub [Tworzenie pliku parameters.JSON następującym kodem](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , który określa nazwy zasobu.

Aby utworzyć środowisko z szablonu:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Warto przeczytać [więcej informacji na temat sposobu wdrażania z szablonów](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Więcej informacji na temat sposobu przyrostowo aktualizacja środowisk, użyj pliku parametrów i dostępu do szablonów z lokalizacji magazynu jednego.

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz przystąpić do rozpoczęcia pracy z wieloma składnikami sieciowymi i maszyny wirtualne. To środowisko próbki służy do tworzenia aplikacji przy użyciu podstawowe składniki wprowadzone w tym miejscu.
