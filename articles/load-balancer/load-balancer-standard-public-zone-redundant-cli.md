---
title: Load balance zone-redundant VMs using Azure CLI
titleSuffix: Azure Load Balancer
description: Learn how to create a public Standard Load Balancer with zone redundant frontend using Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: allensu
ms.openlocfilehash: af327f751a0af67b6d17330dbaeb717df8660bfd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225264"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Load balance VMs across all availability zones using Azure CLI

This article steps through creating a public [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) with a zone-redundant frontend to achieve zone-redundancy without dependency on multiple DNS records. A single front-end IP address is automatically zone-redundant.  Using a zone redundant frontend for your load balancer, with a single IP address you can now reach any VM in a virtual network within a region that is across all Availability Zones. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych.

Aby uzyskać więcej informacji na temat obsługi stref dostępności przy użyciu usługi Load Balancer w warstwie Standardowa, zobacz [Standard Load Balancer and Availability Zones (Usługa Load Balancer w warstwie Standardowa i strefy dostępności)](load-balancer-standard-availability-zones.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

If you choose to install and use the CLI locally, this tutorial requires that you are running Azure CLI version 2.0.17 or higher.  Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Support for Availability Zones is available for select Azure resources and regions, and VM size families. For more information on how to get started, and which Azure resources, regions, and VM size families you can try availability zones with, see [Overview of Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Utwórz grupę zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

The following example creates a resource group named *myResourceGroupSLB* in the *westeurope* location:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Create a zone redundant public IP Standard
Aby uzyskać dostęp do aplikacji za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. A zone-redundant front-end is served by all availability zones in a region simultaneously. Create a zone redundant public IP address with [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). When you create a Standard Public  IP address, it is zone redundant by default.

The following example creates a zone redundant public IP address named *myPublicIP* in the *myResourceGroupLoadBalancer* resource group.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Create Azure Standard Load Balancer
W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:
- Adres IP frontonu, który odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
- Pula adresów IP zaplecza, gdzie pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
- Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
- Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer"></a>Tworzenie modułu równoważenia obciążenia
Create a Standard load balancer with [az network lb create](/cli/azure/network/lb#az-network-lb-create). The following example creates a load balancer named *myLoadBalancer* and assigns the *myPublicIP* address to the front-end IP configuration.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Create health probe on port 80

Sonda kondycji sprawdza wszystkie wystąpienia maszyny wirtualnej, aby upewnić się, że mogą wysyłać ruch sieciowy. Wystąpienie maszyny wirtualnej, w przypadku którego sprawdzanie kondycji za pomocą sondy nie powiodło się, jest usuwane z modułu równoważenia obciążenia do momentu ponownego przejścia do trybu online i pomyślnego sprawdzenia kondycji. Create a health probe with az network lb probe create to monitor the health of the virtual machines. Aby utworzyć sondę kondycji TCP, należy użyć polecenia [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). W poniższym przykładzie zostanie utworzona sonda kondycji o nazwie *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Create load balancer rule for port 80
Reguła modułu równoważenia obciążenia definiuje konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myLoadBalancerRuleWeb* za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) w celu nasłuchiwania na porcie 80 w puli frontonu *myFrontEndPool* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *myBackEndPool*, również przy użyciu portu 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej
Zanim będzie możliwe wdrożenie maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Create a virtual network named *myVnet* with a subnet named *mySubnet* in the myResourceGroup using [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Create network security group named *myNetworkSecurityGroup* to define inbound connections to your virtual network  with [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Create a network security group rule named *myNetworkSecurityGroupRule* for port 80 with [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
--protocol tcp \
--direction inbound \
--source-address-prefix '*' \
--source-port-range '*' \
--destination-address-prefix '*' \
--destination-port-range 80 \
--access allow \
--priority 200
```
### <a name="create-nics"></a>Tworzenie kart sieciowych
Create three virtual NICs with [az network nic create](/cli/azure/network/nic#az-network-nic-create) and associate them with the Public IP address and the network security group. The following example creates six virtual NICs. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach). Możesz w dowolnym momencie utworzyć i dodać do modułu równoważenia obciążenia dodatkowe wirtualne karty sieciowe i maszyny wirtualne:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza
In this example, you create three virtual machines located in zone 1, zone 2, and zone 3 to be used as backend servers for the load balancer. You also install NGINX on the virtual machines to verify that the load balancer was successfully created.

### <a name="create-cloud-init-config"></a>Tworzenie pliku konfiguracji cloud-init

Aby zainstalować serwer NGINX i uruchomić aplikację Node.js „Hello World” na maszynie wirtualnej z systemem Linux, możesz użyć pliku konfiguracji cloud-init. W bieżącej powłoce utwórz plik o nazwie cloud-init.txt, a następnie skopiuj i wklej poniższą konfigurację do powłoki. Upewnij się, że kopiujesz cały plik cloud-init, a szczególnie pierwszy wiersz:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-the-zonal-virtual-machines"></a>Create the zonal virtual machines
Create the VMs with [az vm create](/cli/azure/vm#az-vm-create) in zone 1, zone 2, and zone 3. The following example creates a VM in each zone and generates SSH keys if they do not already exist:

Create a VM in each zone (zone 1, zone2, and zone 3) of the *westeurope* location.

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

Get the public IP address of the load balancer using [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Następnie możesz wprowadzić publiczny adres IP w przeglądarce internetowej. Pamiętaj — kilka minut trwa przygotowanie maszyn wirtualnych zanim moduł równoważenia obciążenia rozpocznie dystrybucję ruchu do nich. Zostanie wyświetlona aplikacja z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch, jak pokazano na poniższym przykładzie:

![Uruchamianie aplikacji Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

To see the load balancer distribute traffic across VMs in all three availability zones running your app, you can stop a VM in a particular zone and refresh your browser.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](./load-balancer-standard-overview.md)



