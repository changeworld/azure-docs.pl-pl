---
title: Tworzenie środowiska systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure
description: Tworzenie magazynu, maszyny Wirtualnej systemu Linux, sieci wirtualnej i podsieci, modułu równoważenia obciążenia, karty sieciowej, publicznego adresu IP i sieciowej grupy zabezpieczeń, a wszystko to od podstaw przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 7ee4674f5e7c04709256459c3417a1379a65aedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969558"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Tworzenie kompletnej maszyny wirtualnej systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure
Aby szybko utworzyć maszynę wirtualną (VM) na platformie Azure, można użyć pojedynczego polecenia interfejsu wiersza polecenia platformy Azure, które używa wartości domyślnych do tworzenia wszelkich wymaganych zasobów pomocniczych. Zasoby, takie jak sieć wirtualna, publiczny adres IP i reguły sieciowej grupy zabezpieczeń są tworzone automatycznie. Aby uzyskać większą kontrolę nad środowiskiem w użyciu produkcyjnym, można utworzyć te zasoby z wyprzedzeniem, a następnie dodać maszyny wirtualne do nich. W tym artykule opisano, jak utworzyć maszynę wirtualną i każdy z zasobów pomocniczych jeden po drugim.

Upewnij się, że zainstalowano najnowszą [platformę Interfejsu wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogowano się do konta platformy Azure przy [logowaniu az.](/cli/azure/reference-index)

W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów to *myResourceGroup*, *myVnet*i *myVM*.

## <a name="create-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupa zasobów musi zostać utworzona przed maszyną wirtualną i obsługującymi zasoby sieci wirtualnej. Utwórz grupę zasobów z [utworzeniem grupy AZ](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Domyślnie dane wyjściowe poleceń interfejsu wiersza polecenia platformy Azure są w języku JSON (Notacja obiektu JavaScript). Aby zmienić domyślne dane wyjściowe na listę lub tabelę, użyj [az configure --output](/cli/azure/reference-index). Można również `--output` dodać do dowolnego polecenia jednorazową zmianę formatu wyjściowego. W poniższym przykładzie przedstawiono `az group create` dane wyjściowe JSON z polecenia:

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
Następnie należy utworzyć sieć wirtualną na platformie Azure i podsieć, w którym można utworzyć maszyny wirtualne. Tworzenie [sieci wirtualnej az](/cli/azure/network/vnet) umożliwia utworzenie sieci wirtualnej o nazwie *myVnet* z prefiksem adresu *192.168.0.0/16.* Dodasz również podsieć o nazwie *mySubnet* z prefiksem adresu *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Dane wyjściowe pokazują, że podsieć jest logicznie tworzony wewnątrz sieci wirtualnej:

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
Teraz stwórzmy publiczny adres IP z [siecią az public-ip create](/cli/azure/network/public-ip). Ten publiczny adres IP umożliwia łączenie się z maszynami wirtualnymi z Internetu. Ponieważ adres domyślny jest dynamiczny, utwórz `--domain-name-label` nazwany wpis DNS z parametrem. Poniższy przykład tworzy publiczny adres IP o nazwie *myPublicIP* z nazwą DNS *mypublicdns*. Ponieważ nazwa DNS musi być unikatowa, podaj własną unikatową nazwę DNS:

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
Aby kontrolować przepływ ruchu do i z maszyn wirtualnych, należy zastosować grupę zabezpieczeń sieci do wirtualnej karty sieciowej lub podsieci. W poniższym przykładzie użyto [nsg sieci AZ](/cli/azure/network/nsg) do utworzenia sieciowej grupy zabezpieczeń o nazwie *myNetworkSecurityGroup:*

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Definiowanie reguł, które zezwalają lub odmawiają określonego ruchu. Aby zezwolić na połączenia przychodzące na porcie 22 (aby włączyć dostęp SSH), utwórz regułę przychodzącą z [regułą nsg sieci az .](/cli/azure/network/nsg/rule) Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRuleSSH:*

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

Aby zezwolić na połączenia przychodzące na porcie 80 (dla ruchu internetowego), dodaj inną regułę sieciowej grupy zabezpieczeń. Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRuleHTTP:*

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

Sprawdź grupę zabezpieczeń sieci i reguły za pomocą [programu NSG sieci AZ:](/cli/azure/network/nsg)

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

## <a name="create-a-virtual-nic"></a>Tworzenie wirtualnej karty sieciowej
Karty interfejsu sieci wirtualnej (NIC) są programowo dostępne, ponieważ można zastosować reguły do ich użycia. W zależności od [rozmiaru maszyny Wirtualnej](sizes.md)można dołączyć wiele wirtualnych kart sieciowych do maszyny Wirtualnej. W poniższym [poleceniu tworzenia sieci AZ należy](/cli/azure/network/nic) utworzyć kartę sieciową o nazwie *myNic* i skojarzyć ją z sieciową grupą zabezpieczeń. Publiczny adres IP *myPublicIP* jest również skojarzony z wirtualną kartą sieciową.

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
Zestawy dostępności pomagają rozłożyć maszyny wirtualne na domeny błędów i zaktualizować domeny. Mimo że obecnie tworzysz tylko jedną maszynę wirtualną, najlepszym rozwiązaniem jest użycie zestawów dostępności, aby ułatwić rozwijanie w przyszłości. 

Domeny błędów definiują grupowanie maszyn wirtualnych, które współużytkują wspólne źródło zasilania i przełącznik sieciowy. Domyślnie maszyny wirtualne skonfigurowane w ramach zestawu dostępności są podzielone na maksymalnie trzy domeny błędów. Problem ze sprzętem w jednej z tych domen błędów nie wpływa na każdą maszynę wirtualną z uruchomieniem aplikacji.

Domeny aktualizacji wskazują grupy maszyn wirtualnych i podstawowy sprzęt fizyczny, który można ponownie uruchomić w tym samym czasie. Podczas planowanej konserwacji kolejność ponownego uruchamiania domen aktualizacji może nie być sekwencyjna, ale tylko jedna domena aktualizacji jest ponownie uruchamiana jednocześnie.

Platforma Azure automatycznie dystrybuuje maszyny wirtualne między usterkami i aktualizuje domeny podczas umieszczania ich w zestawie dostępności. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępnością maszyn wirtualnych](manage-availability.md).

Utwórz zestaw dostępności dla maszyny Wirtualnej z [az vm dostępności zestaw create](/cli/azure/vm/availability-set). W poniższym przykładzie zostanie utworzony zestaw dostępności o nazwie *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

Domeny błędów notatek wyjściowych i domeny aktualizacji:

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
Utworzono zasoby sieciowe do obsługi maszyn wirtualnych dostępnych w Internecie. Teraz utwórz maszynę wirtualną i zabezpiecz ją za pomocą klucza SSH. W tym przykładzie utwórzmy maszynę wirtualną Ubuntu na podstawie najnowszego LTS. Dodatkowe obrazy z [listą obrazów az vm](/cli/azure/vm/image)można znaleźć, zgodnie z opisem [w znajdowaniu obrazów maszyn wirtualnych platformy Azure.](cli-ps-findimage.md)

Określ klucz SSH, który ma być używany do uwierzytelniania. Jeśli nie masz pary kluczy publicznych SSH, możesz `--generate-ssh-keys` je [utworzyć](mac-create-ssh-keys.md) lub użyć parametru, aby utworzyć je dla Ciebie. Jeśli masz już parę kluczy, ten parametr `~/.ssh`używa istniejących kluczy w pliku .

Utwórz maszynę wirtualną, łącząc wszystkie zasoby i informacje wraz z [poleceniem az vm create.](/cli/azure/vm) Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM:*

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

SSH do maszyny Wirtualnej z wpisem DNS podanym podczas tworzenia publicznego adresu IP. Jest `fqdn` to pokazane na danych wyjściowych podczas tworzenia maszyny Wirtualnej:

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

Można zainstalować NGINX i zobaczyć przepływ ruchu do maszyny Wirtualnej. Zainstaluj NGINX w następujący sposób:

```bash
sudo apt-get install -y nginx
```

Aby wyświetlić domyślną witrynę NGINX w akcji, otwórz przeglądarkę internetową i wprowadź fqdn:

![Domyślna witryna NGINX na maszynie wirtualnej](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Eksportowanie jako szablonu
Co zrobić, jeśli teraz chcesz utworzyć dodatkowe środowisko programistyczne o tych samych parametrach lub środowisko produkcyjne, które je pasuje? Menedżer zasobów używa szablonów JSON, które definiują wszystkie parametry dla środowiska. Tworzenie całych środowisk przez odwoływanie się do tego szablonu JSON. Szablony [JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) można tworzyć ręcznie lub eksportować istniejące środowisko, aby utworzyć szablon JSON. [Eksport grupy AZ](/cli/azure/group) służy do eksportowania grupy zasobów w następujący sposób:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

To polecenie `myResourceGroup.json` tworzy plik w bieżącym katalogu roboczym. Podczas tworzenia środowiska z tego szablonu, zostanie wyświetlony monit o wszystkie nazwy zasobów. Nazwy te można wypełnić w pliku szablonu, dodając `--include-parameter-default-value` parametr do `az group export` polecenia. Edytuj szablon JSON, aby określić nazwy zasobów, lub [utwórz plik parameters.json](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) określający nazwy zasobów.

Aby utworzyć środowisko na podstawie szablonu, należy użyć [tworzenia wdrożenia grupy az](/cli/azure/group/deployment) w następujący sposób:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Możesz przeczytać [więcej informacji o tym, jak wdrożyć z szablonów](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Dowiedz się, jak stopniowo aktualizować środowiska, używać pliku parametrów i uzyskiwać dostęp do szablonów z jednej lokalizacji magazynu.

## <a name="next-steps"></a>Następne kroki
Teraz możesz rozpocząć pracę z wieloma składnikami sieciowymi i maszynami wirtualnymi. Tego przykładowego środowiska można użyć do utworzenia aplikacji przy użyciu podstawowych składników wprowadzonych w tym miejscu.
