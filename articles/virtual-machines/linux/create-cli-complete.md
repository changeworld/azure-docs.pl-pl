---
title: Tworzenie środowiska systemu Linux za pomocą wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Utwórz magazyn, maszyny Wirtualnej z systemem Linux, sieci wirtualnej i podsieci, modułu równoważenia obciążenia, kartę Sieciową, publiczny adres IP i sieciową grupę zabezpieczeń, wszystkie od podstaw przy użyciu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: bcaa3ae105490fe4f38a9de47ba0450c33da5ee1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671633"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Utwórz pełną maszyny wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure
Aby szybko utworzyć maszynę wirtualną (VM) na platformie Azure, można użyć jednego polecenia wiersza polecenia platformy Azure, która używa domyślnych wartości, aby utworzyć wszystkie wymagane zasoby pomocnicze. Zasoby, takie jak sieć wirtualną, publiczny adres IP i reguły sieciowej grupy zabezpieczeń są tworzone automatycznie. Aby uzyskać większą kontrolę środowiska w środowisku produkcyjnym należy użyć, możesz utworzyć te zasoby, które wcześniej i następnie dodać maszyny wirtualne do nich. Ten artykuł przeprowadzi Cię przez tworzenie maszyny Wirtualnej, a każdy z pomocnicze zasoby pojedynczo.

Upewnij się, że zainstalowano najnowszy [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index).

W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru zawierają *myResourceGroup*, *myVnet*, i *myVM*.

## <a name="create-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Przed maszynę wirtualną i pomocnicze zasoby sieci wirtualnej należy utworzyć grupę zasobów. Utwórz grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Domyślnie dane wyjściowe poleceń interfejsu wiersza polecenia platformy Azure jest w formacie JSON (JavaScript Object Notation). Aby zmienić domyślne dane wyjściowe do listy lub tabeli, na przykład użyć [skonfigurować az--dane wyjściowe](/cli/azure/reference-index). Można również dodać `--output` do dowolnego polecenia jeden raz, Zmień format danych wyjściowych. Poniższy przykład przedstawia dane wyjściowe JSON z `az group create` polecenia:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci
Dalej utworzonej sieci wirtualnej na platformie Azure i podsieć, w którym można utworzyć maszyny wirtualne. Użyj [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet) Aby utworzyć sieć wirtualną o nazwie *myVnet* z *192.168.0.0/16* prefiksu adresu. Możesz również dodać podsieć o nazwie *mySubnet* z prefiksem adresu *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Dane wyjściowe pokazują, że podsieć logicznie jest tworzony w sieci wirtualnej:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP
Teraz Utwórzmy publiczny adres IP z [tworzenie sieci az public-ip](/cli/azure/network/public-ip). Ten publiczny adres IP umożliwia łączenie maszyn wirtualnych z Internetu. Ponieważ adresem domyślnym jest dynamiczny, utworzyć wpis DNS o nazwie z `--domain-name-label` parametru. Poniższy przykład tworzy publiczny adres IP o nazwie *myPublicIP* nazwą DNS *mypublicdns*. Ponieważ nazwa DNS musi być unikatowa, należy podać swoją własną unikatową nazwę DNS:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Dane wyjściowe:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Aby sterować przepływem ruchu sieciowego do i z maszyn wirtualnych, należy zastosować sieciową grupę zabezpieczeń do wirtualnych kart Sieciowych lub podsieci. W poniższym przykładzie użyto [tworzenie az sieciowej](/cli/azure/network/nsg) utworzyć sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Należy zdefiniować regułę, które blokują lub zezwalają określonego ruchu. Aby zezwolić na połączenia przychodzące na porcie 22 (Aby włączyć dostęp protokołu SSH), należy utworzyć regułę ruchu przychodzącego z [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule). Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRuleSSH*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

Aby zezwolić na połączenia przychodzące na porcie 80 (dla ruchu w sieci web), należy dodać inny reguły sieciowej grupy zabezpieczeń. Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRuleHTTP*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Sprawdź sieciowej grupy zabezpieczeń i reguły z [Pokaż sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Dane wyjściowe:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Tworzenie wirtualnej karty Sieciowej
Wirtualne karty sieciowe (NIC) są programowane dostępne, ponieważ możliwe jest stosowanie reguł do ich wykorzystania. W zależności od [rozmiar maszyny Wirtualnej](sizes.md), można dołączyć wiele wirtualnych kart sieciowych do maszyny Wirtualnej. W następującym [tworzenie az sieciowego](/cli/azure/network/nic) polecenia Utwórz kartę Sieciową o nazwie *myNic* i skojarzyć ją z sieciową grupę zabezpieczeń. Publiczny adres IP *myPublicIP* jest powiązany z wirtualnej karty sieciowej.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Dane wyjściowe:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności
Zestawy dostępności pomocy rozprzestrzeniania się maszyny wirtualne w domenach błędów i domenach aktualizacji. Mimo że możesz utworzyć tylko jedną z maszyn wirtualnych teraz, jest najlepszym rozwiązaniem jest użycie zestawów dostępności, aby ułatwić w przyszłości. 

Domeny błędów definiują grupę maszyn wirtualnych, które mają wspólnego źródła zasilania i sieci przełącznika. Domyślnie maszyny wirtualne, które są skonfigurowane w zestawie dostępności są rozdzielane między maksymalnie trzy domeny błędów. Problem ze sprzętem w jednym z tych domen błędów nie ma wpływu na każdej maszynie Wirtualnej, na którym działa aplikacja.

Domeny aktualizacji wskazanie grup maszyn wirtualnych i odpowiedniego sprzętu fizycznego, które mogą być uruchamiane w tym samym czasie. Podczas zaplanowanej konserwacji kolejność, w aktualizacji, które są ponownie uruchamiane domeny może nie być sekwencyjne, ale tylko jedna domena aktualizacji jest uruchamiana ponownie w danym momencie.

Podczas umieszczania ich w zestawie dostępności platformy Azure automatycznie rozdziela maszyny wirtualne w domenach błędów i aktualizacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych](manage-availability.md).

Utwórz zestaw dostępności dla maszyny Wirtualnej za pomocą [az vm w zestawie dostępności Utwórz](/cli/azure/vm/availability-set). W poniższym przykładzie zostanie utworzony zestaw dostępności o nazwie *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

Informacje o danych wyjściowych domeny błędów i domenach aktualizacji:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
Utworzono zasobów sieciowych do obsługi maszyn wirtualnych dostępne za pośrednictwem Internetu. Teraz Utwórz Maszynę wirtualną i zabezpiecz ją przy użyciu klucza SSH. W tym przykładzie utworzymy maszynę Wirtualną opartą na najnowszych LTS Ubuntu. Możesz znaleźć dodatkowe obrazy z [listy obrazów maszyn wirtualnych az](/cli/azure/vm/image), zgodnie z opisem w [znajdowanie obrazów maszyn wirtualnych Azure](cli-ps-findimage.md).

Określ klucz SSH do uwierzytelniania. Jeśli nie masz pary kluczy publicznych SSH, możesz to zrobić [je utworzyć](mac-create-ssh-keys.md) lub użyj `--generate-ssh-keys` parametru, aby je utworzyć za Ciebie. Jeśli masz już parę kluczy, ten parametr używa istniejących kluczy w `~/.ssh`.

Tworzenie maszyny Wirtualnej, przenosząc wszystkie zasoby i informacje wraz z [tworzenie az vm](/cli/azure/vm) polecenia. W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH z maszyną Wirtualną z wpisem DNS podany podczas tworzenia publicznego adresu IP. To `fqdn` jest wyświetlany w danych wyjściowych, jak utworzyć maszynę Wirtualną:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Dane wyjściowe:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

Możesz zainstalować rozwiązanie NGINX i ruch przepływ do maszyny Wirtualnej. Zainstaluj serwer NGINX w następujący sposób:

```bash
sudo apt-get install -y nginx
```

Aby wyświetlić domyślna witryna serwera NGINX w akcji, otwórz przeglądarkę internetową i wprowadź nazwę FQDN:

![Domyślna witryna serwera NGINX na maszynie Wirtualnej](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Eksportuj jako szablon
Co zrobić, jeśli teraz chcesz utworzyć środowisko dodatkowego programowania z tymi samymi parametrami lub w środowisku produkcyjnym, które odpowiadają go? Usługi Resource Manager korzysta z szablonów JSON, które definiują wszystkie parametry dla danego środowiska. Twórz się całych środowisk, odwołując się do tego szablonu JSON. Możesz [ręczne tworzenie szablonów JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub wyeksportować istniejącego środowiska, aby utworzyć szablon JSON dla Ciebie. Użyj [eksportowanie grupy az](/cli/azure/group) Aby wyeksportować grupy zasobów w następujący sposób:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

To polecenie umożliwia utworzenie `myResourceGroup.json` plik w bieżącym katalogu roboczym. Po utworzeniu środowiska za pomocą tego szablonu, zostanie wyświetlony monit o wszystkich nazw zasobów. Możesz wypełnić te nazwy w pliku szablonu, dodając `--include-parameter-default-value` parametr `az group export` polecenia. Edytuj szablon JSON do określenia nazwy zasobu lub [Tworzenie pliku parameters.json](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) określający nazw zasobów.

Aby utworzyć środowisko z szablonu, należy użyć [Utwórz wdrożenie grupy az](/cli/azure/group/deployment) w następujący sposób:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Warto przeczytać [więcej informacji na temat sposobu wdrażania za pomocą szablonów](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Dowiedz się więcej na temat przyrostowe aktualizowanie środowisk przy użyciu pliku parametrów oraz uzyskiwać dostęp do szablonów z lokalizacji pojedynczy magazyn.

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz przystąpić do rozpoczęcia pracy z wieloma składnikami sieciowymi i maszyny wirtualne. To środowisko przykładowe służy do kompilowania aplikacji przy użyciu podstawowych składników wprowadzone w tym miejscu.
