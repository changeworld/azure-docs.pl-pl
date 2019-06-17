---
title: Tworzenie kompletnego środowiska systemu Linux za pomocą wiersza polecenia platformy Azure Classic | Dokumentacja firmy Microsoft
description: Tworzenie magazynu, Maszynę wirtualną systemu Linux, sieci wirtualnej i podsieci, modułu równoważenia obciążenia, kartę Sieciową, publiczny adres IP i sieciową grupę zabezpieczeń, wszystkie od podstaw przy użyciu klasycznego wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 04c1d69fc46b9a918038e93c4fc56681f225d365
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328718"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Tworzenie kompletnego środowiska systemu Linux za pomocą klasycznego wiersza polecenia platformy Azure
W tym artykule firma Microsoft tworzy prostą sieć przy użyciu modułu równoważenia obciążenia i pary maszyn wirtualnych, które są przydatne do tworzenia i przetwarzania proste. Części omówimy proces polecenia przez polecenie, dopóki nie uzyskasz dwa działa, bezpieczne maszyn wirtualnych systemu Linux do których możesz połączyć z dowolnego miejsca w Internecie. Następnie możesz przejść do bardziej złożonych sieci i środowisk.

Po drodze poznasz hierarchii zależności, zapewnia modelu wdrażania usługi Resource Manager i o ile włączenie go zawiera. Gdy zobaczysz, jak zbudowane jest systemu, można ponownie utworzyć jej znacznie szybciej za pomocą [szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ponadto po dowiesz się, jak części środowiska współdziałają ze sobą, tworzenie szablonów, aby je zautomatyzować staje się łatwiejsze.

Środowisko zawiera:

* Dwie maszyny wirtualne w zestawie dostępności.
* Moduł równoważenia obciążenia z regułą równoważenia obciążenia na porcie 80.
* Reguły sieciowej grupy zabezpieczeń (NSG) do ochrony maszyny Wirtualnej z niepożądanym ruchem.

Do utworzenia tego środowiska niestandardowego, potrzebujesz najnowszej [klasyczny interfejs wiersza polecenia platformy Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) w trybie Menedżera zasobów (`azure config mode arm`). Należy również narzędzia analizy JSON. W tym przykładzie użyto [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Klasyczny interfejs wiersza polecenia Azure](#quick-commands) — Nasz interfejs wiersza polecenia dla klasycznego i zasobów zarządzania modeli wdrażania (w tym artykule)
- [Interfejs wiersza polecenia Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — naszej nowej generacji interfejsu wiersza polecenia dla modelu wdrażania usługi resource management


## <a name="quick-commands"></a>Szybkie polecenia
Jeśli chcesz szybko wykonać zadania w poniższej sekcji opisano szczegółowo base polecenia do przekazania Maszynę wirtualną na platformie Azure. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć w pozostałej części dokumentu, uruchamianie [tutaj](#detailed-walkthrough).

Upewnij się, że masz [wiersza polecenia platformy Azure Classic](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zalogowany i korzystania z trybu usługi Resource Manager:

```azurecli
azure config mode arm
```

W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru zawierają `myResourceGroup`, `mystorageaccount`, i `myVM`.

Utwórz grupę zasobów. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Sprawdź grupy zasobów przy użyciu analizatora składni JSON:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Utwórz konto magazynu. Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount`. (Nazwa konta magazynu muszą być unikatowe, więc należy podać swoją własną unikatową nazwę.)

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

Sprawdź sieć wirtualną i podsieć, za pomocą analizatora składni JSON:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Tworzenie publicznego adresu IP. Poniższy przykład tworzy publiczny adres IP o nazwie `myPublicIP` nazwą DNS `mypublicdns`. (Nazwa DNS muszą być unikatowe, więc należy podać swoją własną unikatową nazwę.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Tworzenie modułu równoważenia obciążenia. Poniższy przykład tworzy moduł równoważenia obciążenia o nazwie `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Utwórz pulę adresów IP frontonu modułu równoważenia obciążenia i skojarz publiczny adres IP. Poniższy przykład tworzy pulę adresów IP frontonu o nazwie `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Utwórz pulę adresów IP zaplecza dla modułu równoważenia obciążenia. Poniższy przykład tworzy pulę adresów IP zaplecza o nazwie `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Utwórz sieć dla ruchu przychodzącego protokołu SSH reguły translatora adresów (NAT) dla modułu równoważenia obciążenia. Poniższy przykład tworzy dwie reguły modułu równoważenia obciążenia, `myLoadBalancerRuleSSH1` i `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Utwórz sieć web reguły NAT modułu równoważenia obciążenia dla ruchu przychodzącego. Poniższy przykład tworzy regułę modułu równoważenia obciążenia o nazwie `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Tworzenie sondy kondycji modułu równoważenia obciążenia. Poniższy przykład tworzy sondę TCP, o nazwie `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Zweryfikować moduł równoważenia obciążenia, pule adresów IP i reguł translatora adresów Sieciowych przy użyciu analizatora JSON:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Tworzenie pierwszej karty interfejsu sieciowego (NIC). Zastąp `#####-###-###` sekcje własnym identyfikatorem subskrypcji platformy Azure Identyfikator jest podana w danych wyjściowych subskrypcja **jq** podczas badania zasobów tworzona. Można również wyświetlić swój identyfikator subskrypcji z `azure account list`.

Poniższy przykład tworzy kartę Sieciową o nazwie `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Tworzenie drugiej karty sieciowej. Poniższy przykład tworzy kartę Sieciową o nazwie `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Aby zweryfikować dwie karty sieciowe, przy użyciu analizatora JSON:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Utwórz sieciową grupę zabezpieczeń. Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Dodaj dwie reguły dla ruchu przychodzącego dla sieciowej grupy zabezpieczeń. Poniższy przykład tworzy dwie reguły `myNetworkSecurityGroupRuleSSH` i `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Sprawdź sieciowej grupy zabezpieczeń i reguł dla ruchu przychodzącego za pomocą analizatora składni JSON:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Powiąż sieciową grupę zabezpieczeń z dwiema kartami sieciowymi:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Tworzenie zestawu dostępności. Poniższy przykład obejmuje tworzenie zestawu dostępności o nazwie `myAvailabilitySet`:

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

Utwórz drugą maszynę Wirtualną systemu Linux. Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM2`:

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

Korzystanie z analizatora JSON do sprawdzenia, czy wszystkie elementy, który został utworzony:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Eksportuj nowe środowisko do szablonu w celu ponownego szybkie tworzenie nowych wystąpień:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik
Szczegółowy opis kroków, które należy wykonać wyjaśniają, każde polecenie czynności jak tworzysz swoje środowisko. Te pojęcia są przydatne podczas tworzenia niestandardowego środowiska programowania lub produkcji.

Upewnij się, że masz [wiersza polecenia platformy Azure Classic](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zalogowany i korzystania z trybu usługi Resource Manager:

```azurecli
azure config mode arm
```

W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru zawierają `myResourceGroup`, `mystorageaccount`, i `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Tworzenie grupy zasobów i Wybieranie lokalizacji wdrożenia
Grupy zasobów platformy Azure są jednostek logiczną wdrożenia, które zawierają informacje dotyczące konfiguracji oraz metadane, aby umożliwić zarządzanie logiczną wdrożenia zasobów. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`:

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
Potrzebujesz konta usługi storage dla dysków maszyn wirtualnych i dla dowolnego dodatkowego dysku z danymi, które chcesz dodać. Niemal natychmiast, po utworzeniu grupy zasobów możesz utworzyć konta magazynu.

W tym miejscu użyjemy `azure storage account create` polecenia przekazaniem Lokalizacja konta, grupy zasobów, które kontroluje, a typ ma storage — pomoc techniczna. Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount`:

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

Aby sprawdzić naszą grupę zasobów za pomocą `azure group show` poleceń, użyjemy [jq](https://stedolan.github.io/jq/) narzędzia wraz z `--json` opcji wiersza polecenia platformy Azure. (Możesz użyć **jsawk** lub każdą bibliotekę języka chcesz przeanalizować za pomocą pliku JSON.)

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

Aby zbadać konta magazynu przy użyciu interfejsu wiersza polecenia, należy najpierw do ustawiania nazwy konta i klucze. Zastąp nazwę tworzonej nazwę konta magazynu w następującym przykładzie:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Następnie można wyświetlić informacje dotyczące Twojego magazynu łatwe:

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
Następnie możesz zacząć, należy utworzyć sieć wirtualną, uruchomiony w systemie Azure i podsieć, w którym można utworzyć maszyny wirtualne. Poniższy przykład tworzy sieć wirtualną o nazwie `myVnet` z `192.168.0.0/16` prefiks adresu:

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

Ponownie, możemy użyć opcji--json `azure group show` i `jq` aby zobaczyć, jak tworzymy nasze zaawansowane zasoby. W efekcie powstał `storageAccounts` zasobów i `virtualNetworks` zasobów.  

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

Teraz Utwórzmy podsieci `myVnet` sieci wirtualnej, w której maszyny wirtualne są wdrażane. Używamy `azure network vnet subnet create` polecenie wraz z zasobami, utworzyliśmy już: `myResourceGroup` grupy zasobów i `myVnet` sieci wirtualnej. W poniższym przykładzie dodamy podsieć o nazwie `mySubnet` z prefiksem adresu podsieci `192.168.1.0/24`:

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

Ponieważ podsieci logicznie znajduje się wewnątrz sieci wirtualnej, firma Microsoft poszukaj informacji o podsieci za pomocą polecenia nieco inne. Polecenie używamy jest `azure network vnet show`, ale Kontynuujemy Sprawdź dane wyjściowe JSON za pomocą `jq`.

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
Teraz Utwórzmy publiczny adres IP (PIP), który możemy Przypisz do modułu równoważenia obciążenia. Umożliwia łączenie maszyn wirtualnych z Internetu przy użyciu `azure network public-ip create` polecenia. Ponieważ adres domyślny jest dynamiczny, utworzymy nazwanego wpisu DNS w **cloudapp.azure.com** domeny przy użyciu `--domain-name-label` opcji. Poniższy przykład tworzy publiczny adres IP o nazwie `myPublicIP` nazwą DNS `mypublicdns`. Ponieważ nazwa DNS musi być unikatowa, możesz podać swoją własną unikatową nazwę DNS:

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

Publiczny adres IP jest również zasobem najwyższego poziomu, można to sprawdzić za pomocą `azure group show`.

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

Możesz zbadać, aby uzyskać więcej informacji zasobów, w tym w pełni kwalifikowana nazwa domeny (FQDN) domeny podrzędnej, korzystając z pełnego `azure network public-ip show` polecenia. Zasób publicznego adresu IP przydzielone logicznie, ale nie został jeszcze przypisany określony adres. Aby uzyskać adres IP, zamierzasz muszą modułu równoważenia obciążenia, które firma Microsoft nie zostały jeszcze utworzone.

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
Podczas tworzenia modułu równoważenia obciążenia pozwala rozdziela ruch między wieloma maszynami wirtualnymi. Umożliwia także nadmiarowości aplikacji, uruchamiając wiele maszyn wirtualnych, które odpowiadają na żądania użytkownika w przypadku konserwacji lub dużym obciążeniem. Poniższy przykład tworzy moduł równoważenia obciążenia o nazwie `myLoadBalancer`:

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

Nasze usługi równoważenia obciążenia jest dość pusta, więc Utwórzmy niektóre pule adresów IP. Chcemy utworzyć dwie pule adresów IP dla naszej usługi równoważenia obciążenia: jeden dla frontonu, a drugi dla wewnętrznej. Puli adresów IP frontonu jest widoczna publicznie. Jest również lokalizacji, do której firma Microsoft Przypisz publiczny adres IP, która została utworzona wcześniej. Następnie używamy puli zaplecza jako lokalizacja, w przypadku naszych maszyn wirtualnych Aby nawiązać połączenie. W ten sposób ruch może przepływać za pośrednictwem modułu równoważenia obciążenia do maszyn wirtualnych.

Po pierwsze Utwórzmy naszych puli adresów IP frontonu. Poniższy przykład tworzy pulę frontonu o nazwie `myFrontEndPool`:

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

Należy zauważyć, jak użyliśmy `--public-ip-name` przełącznika, aby przekazać `myPublicIP` utworzona wcześniej. Przypisanie publicznego adresu IP do modułu równoważenia obciążenia, można dotrzeć do maszyn wirtualnych przez Internet.

Następnie utworzymy naszej drugiej puli adresów IP, tym razem dla naszych ruchu zaplecza. Poniższy przykład tworzy pulę zaplecza o nazwie `myBackEndPool`:

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

Widać, sposobu działania naszych modułu równoważenia obciążenia przez wyszukiwanie za pomocą `azure network lb show` i badanie danych wyjściowych JSON:

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

## <a name="create-load-balancer-nat-rules"></a>Tworzenie reguły NAT modułu równoważenia obciążenia
Aby uzyskać ruch przepływający przez naszych modułu równoważenia obciążenia, należy utworzyć adres sieciowy reguły translatora (NAT), które określają akcji dla ruchu przychodzącego lub wychodzącego. Możesz określić protokół do użycia, a następnie mapowania portów zewnętrznych do wewnętrznych portów zgodnie z potrzebami. W naszym środowisku Utwórzmy niektórych reguł, które umożliwiają SSH za pośrednictwem naszej usługi równoważenia obciążenia do naszych maszyn wirtualnych. Skonfigurujemy porty TCP 4222 i 4223 kierować do portu TCP 22 na nasze maszyny wirtualne, (które możemy utworzyć później). Poniższy przykład tworzy regułę o nazwie `myLoadBalancerRuleSSH1` mapować TCP port 4222 na port 22:

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

Powtórz procedurę dla drugiej reguły NAT dla protokołu SSH. Poniższy przykład tworzy regułę o nazwie `myLoadBalancerRuleSSH2` mapować TCP port 4223 na port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Umożliwia także Przejdź dalej i Utwórz regułę NAT dla portu TCP 80 dla ruchu w sieci web, przechwytywanie reguły do naszego pule adresów IP. Jeśli firma Microsoft podpiąć reguły do puli adresów IP zamiast Podłączanie zasadę, aby nasze maszyny wirtualne pojedynczo, firma Microsoft można dodawać i usuwać maszyny wirtualne z puli adresów IP. Moduł równoważenia obciążenia są automatycznie dostosowuje przepływu ruchu. Poniższy przykład tworzy regułę o nazwie `myLoadBalancerRuleWeb` do mapowania porcie 80 protokołu TCP na porcie 80:

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
Sonda kondycji okresowo sprawdza na maszynach wirtualnych, które znajdują się za naszych modułu równoważenia obciążenia, aby upewnić się, ich działania i odpowiadanie na żądania, zgodnie z definicją. W przeciwnym razie zostali oni usunięci z działania, aby upewnić się, że użytkownicy nie ma być kierowany do ich. Można zdefiniować niestandardowe sprawdza, czy sondę kondycji, wraz z odstępach czasu i wartości limitu czasu. Aby uzyskać więcej informacji na temat sond kondycji, zobacz [sondy modułu równoważenia obciążenia](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Poniższy przykład tworzy TCP kondycji sondowany o nazwie `myHealthProbe`:

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

W tym miejscu określonej odstępie 15 sekund dla naszych kontrole kondycji. Firma Microsoft może pominąć maksymalnie cztery sondy (jednej minuty), zanim moduł równoważenia obciążenia uzna, że host działa już.

## <a name="verify-the-load-balancer"></a>Sprawdź modułu równoważenia obciążenia
Po zakończeniu konfiguracji usługi równoważenia obciążenia. Poniżej przedstawiono kroki, które zostały podjęte:

1. Utworzono moduł równoważenia obciążenia.
2. Możesz utworzyć pulę adresów IP frontonu i do niej przypisany publiczny adres IP.
3. Utworzono pulę adresów IP zaplecza, które maszyny wirtualne mogą łączyć się z.
4. Reguły translatora adresów Sieciowych, umożliwiające SSH do maszyn wirtualnych, zarządzania oraz regułę zezwalającą na porcie 80 protokołu TCP dla naszej aplikacji sieci web została utworzona.
5. Po dodaniu sondę kondycji, aby okresowo sprawdzać maszyn wirtualnych. Tej sondy kondycji gwarantuje, że użytkownicy nie należy próbować dostępu do maszyny Wirtualnej, który już nie działa lub obsługujących zawartość.

Podsumujmy, jak moduł równoważenia obciążenia wygląda teraz:

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

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Utwórz kartę Sieciową do użycia z maszyną Wirtualną systemu Linux
Karty sieciowe są programowo dostępne, ponieważ możliwe jest stosowanie reguł do ich wykorzystania. Również może mieć więcej niż jeden. W następującym `azure network nic create` polecenia Podłączanie do interfejsu Sieciowego do puli adresów IP zaplecza obciążenia i skojarzyć ją z regułą translatora adresów Sieciowych, aby zezwalać na ruch SSH.

Zastąp `#####-###-###` sekcje własnym identyfikatorem subskrypcji platformy Azure Identyfikator jest podana w danych wyjściowych subskrypcja `jq` podczas badania zasobów tworzona. Można również wyświetlić swój identyfikator subskrypcji z `azure account list`.

Poniższy przykład tworzy kartę Sieciową o nazwie `myNic1`:

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

Można wyświetlić szczegóły, sprawdzając zasobu bezpośrednio. Sprawdź zasobu za pomocą `azure network nic show` polecenia:

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

Teraz możemy utworzyć drugi interfejs Sieciowy przechwytywanie w ponownie do naszych puli adresów IP zaplecza. Tę regułę NAT czasu druga zezwala na ruch SSH. Poniższy przykład tworzy kartę Sieciową o nazwie `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Utwórz sieciową grupę zabezpieczeń i reguł
Teraz możemy utworzyć sieciową grupę zabezpieczeń i reguł dla ruchu przychodzącego, które umożliwiają zarządzanie dostępem do karty sieciowej. Sieciowa grupa zabezpieczeń można zastosować do kart Sieciowych lub podsieci. Należy zdefiniować regułę, aby sterować przepływem ruchu sieciowego do i z maszyn wirtualnych. Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Dodajmy Reguła ruchu przychodzącego dla sieciowej grupy zabezpieczeń zezwolić na połączenia przychodzące na porcie 22 (do obsługi protokołu SSH). Poniższy przykład tworzy regułę o nazwie `myNetworkSecurityGroupRuleSSH` aby umożliwić ruch TCP na porcie 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Teraz możemy dodać regułę ruchu przychodzącego dla sieciowej grupy zabezpieczeń zezwolić na połączenia przychodzące na porcie 80 (do obsługi ruchu w sieci web). Poniższy przykład tworzy regułę o nazwie `myNetworkSecurityGroupRuleHTTP` aby umożliwić ruch TCP na porcie 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> Reguła ruchu przychodzącego jest filtrem dla połączeń sieciowych dla ruchu przychodzącego. W tym przykładzie firma Microsoft powiązać sieciowej grupy zabezpieczeń z maszyn wirtualnych z wirtualną kartę Sieciową, co oznacza, że każde żądanie na port 22 jest przekazywana do karty Sieciowej na maszynie Wirtualnej z naszych. Ta reguła ruchu przychodzącego jest o połączenie sieciowe, a nie o punktu końcowego, czyli co byłoby o we wdrożeniach klasycznych. Otwierania portu, należy pozostawić `--source-port-range` ustawiona na "\*" (wartość domyślna) do akceptowania żądań przychodzących od **wszelkie** żądanie portu. Porty są zazwyczaj dynamicznych.
>
>

## <a name="bind-to-the-nic"></a>Powiązywanie z kartą Sieciową
Powiąż sieciowej grupy zabezpieczeń do kart sieciowych. Musimy połączyć nasz kart sieciowych z naszych sieciową grupą zabezpieczeń. Uruchom oba polecenia, aby zaczepić oba nasz kart sieciowych:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności
Zestawy dostępności pomocy rozprzestrzeniania się maszyn wirtualnych w różnych domenach błędów i uaktualnień. Utwórz zestaw dostępności dla maszyn wirtualnych. Poniższy przykład obejmuje tworzenie zestawu dostępności o nazwie `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domeny błędów definiują grupę maszyn wirtualnych, które mają wspólnego źródła zasilania i sieci przełącznika. Domyślnie maszyny wirtualne, które są skonfigurowane w zestawie dostępności są rozdzielane między maksymalnie trzy domeny błędów. Chodzi o to, że problem ze sprzętem w jednym z tych domen błędów nie ma wpływu na każdej maszynie Wirtualnej, na którym działa aplikacja. Azure automatycznie rozdziela maszyny wirtualne między domeny błędów podczas umieszczania ich w zestawie dostępności.

Domen uaktualnienia wskazanie grup maszyn wirtualnych i odpowiedniego sprzętu fizycznego, które mogą być uruchamiane w tym samym czasie. Kolejność, w którym wykonywany jest ponowny rozruch domen uaktualnienia może nie być sekwencyjne podczas zaplanowanej konserwacji, ale tylko jedno uaktualnienie jest uruchamiany ponownie w danym momencie. Ponownie Azure automatycznie rozdziela maszyny wirtualne między domeny uaktualnienia podczas umieszczania ich w witrynie dostępności.

Przeczytaj więcej na temat [Zarządzanie dostępnością maszyn wirtualnych](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Tworzenie maszyn wirtualnych systemu Linux
Utworzono zasoby magazynu i sieci do obsługi maszyn wirtualnych dostępne za pośrednictwem Internetu. Teraz możemy utworzyć te maszyny wirtualne i zabezpieczenia przy użyciu klucza SSH, który nie ma hasła. W tym przypadku będziemy tworzyć oparte na najnowszych LTS maszyny Wirtualnej z systemem Ubuntu. Firma Microsoft zlokalizować te informacje z obrazów za pomocą `azure vm image list`, zgodnie z opisem w [znajdowanie obrazów maszyn wirtualnych Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Wybrano obraz za pomocą polecenia `azure vm image list westeurope canonical | grep LTS`. W tym przypadku używamy `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Na ostatnim polu przekazujemy `latest` tak, aby w przyszłości zawsze uzyskujemy najnowszej kompilacji. (Ciąg, używamy `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Ta następnym krokiem jest znany dla każdego, kto utworzył już ssh rsa klucz publiczny i prywatny pair w systemie Linux lub Mac za pomocą **ssh-keygen - t rsa -b 2048**. Jeśli nie masz żadnych certyfikatów pary kluczy swojej `~/.ssh` katalogu, możesz utworzyć je:

* Automatycznie, przy użyciu `azure vm create --generate-ssh-keys` opcji.
* Ręcznie, przy użyciu [instrukcjami, aby utworzyć samodzielnie](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Alternatywnie, można użyć `--admin-password` metody uwierzytelniania połączenia protokołu SSH, po utworzeniu maszyny Wirtualnej. Ta metoda jest zazwyczaj mniej bezpieczna opcja.

Możemy utworzyć maszynę Wirtualną, przenosząc wszystkie nasze zasoby i informacje wraz z `azure vm create` polecenia:

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

Możesz połączyć z maszyną wirtualną bezpośrednio przy użyciu kluczy SSH domyślne. Upewnij się, określ odpowiedni port, ponieważ firma Microsoft jest przekazywanych przez moduł równoważenia obciążenia. (Dla pierwszej maszyny Wirtualnej, możemy skonfigurować reguły NAT do przekazywania portu 4222 do maszyny Wirtualnej).

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

Przejdź dalej i tworzenie drugiej maszyny Wirtualnej w taki sam sposób:

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

Można teraz używać `azure vm show myResourceGroup myVM1` polecenie, aby sprawdzić, został utworzony. W tym momencie używasz Ubuntu maszyn wirtualnych za modułem równoważenia obciążenia na platformie Azure, które użytkownik może zalogować się do tylko z pary kluczy SSH (ponieważ hasła są wyłączone). Można zainstalować serwer nginx lub host z wieloma adresami, wdrażanie aplikacji sieci web i ruch flow za pośrednictwem modułu równoważenia obciążenia do obu maszyn wirtualnych.

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


## <a name="export-the-environment-as-a-template"></a>Eksportuj środowisko jako szablon
Teraz, gdy został wcześniej utworzony się tego środowiska, co zrobić, jeśli chcesz utworzyć środowisko dodatkowego programowania przy użyciu tych samych parametrach lub do środowiska produkcyjnego, który mu odpowiada? Usługi Resource Manager korzysta z szablonów JSON, które definiują wszystkie parametry dla danego środowiska. Twórz się całych środowisk, odwołując się do tego szablonu JSON. Możesz [ręczne tworzenie szablonów JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub wyeksportować istniejącego środowiska, aby utworzyć szablon JSON dla Ciebie:

```azurecli
azure group export --name myResourceGroup
```

To polecenie umożliwia utworzenie `myResourceGroup.json` plik w bieżącym katalogu roboczym. Po utworzeniu środowiska za pomocą tego szablonu, zostanie wyświetlony monit o wszystkich nazw zasobów, łącznie z nazwami dla modułu równoważenia obciążenia, interfejsów sieciowych lub maszyn wirtualnych. Możesz wypełnić te nazwy w pliku szablonu, dodając `-p` lub `--includeParameterDefaultValue` parametr `azure group export` polecenia, pokazaną wcześniej. Edytuj szablon JSON do określenia nazwy zasobu lub [Tworzenie pliku parameters.json](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) określający nazw zasobów.

Aby utworzyć środowisko z szablonu:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Warto przeczytać [więcej informacji na temat sposobu wdrażania za pomocą szablonów](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Dowiedz się więcej na temat przyrostowe aktualizowanie środowisk przy użyciu pliku parametrów oraz uzyskiwać dostęp do szablonów z lokalizacji pojedynczy magazyn.

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz przystąpić do rozpoczęcia pracy z wieloma składnikami sieciowymi i maszyny wirtualne. To środowisko przykładowe służy do kompilowania aplikacji przy użyciu podstawowych składników wprowadzone w tym miejscu.
