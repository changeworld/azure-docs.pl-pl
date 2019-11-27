---
title: Równoważenie obciążenia strefowo nadmiarowe maszyny wirtualne przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć publiczną usługa Load Balancer w warstwie Standardowa ze strefowo nadmiarowy fronton przy użyciu interfejsu wiersza polecenia platformy Azure
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
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Równoważenie obciążenia maszyn wirtualnych we wszystkich strefach dostępności przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób tworzenia publicznej [Usługa Load Balancer w warstwie Standardowa](https://aka.ms/azureloadbalancerstandard) ze Strefowo nadmiarowego frontonu w celu osiągnięcia nadmiarowości strefy bez zależności w wielu rekordach DNS. Pojedynczy adres IP frontonu jest automatycznie strefowo nadmiarowy.  Korzystając ze strefy nadmiarowego frontonu dla modułu równoważenia obciążenia, za pomocą pojedynczego adresu IP można teraz dotarcie do dowolnej maszyny wirtualnej w sieci wirtualnej w regionie, który znajduje się na wszystkich Strefy dostępności. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych.

Aby uzyskać więcej informacji na temat obsługi stref dostępności przy użyciu usługi Load Balancer w warstwie Standardowa, zobacz [Standard Load Balancer and Availability Zones (Usługa Load Balancer w warstwie Standardowa i strefy dostępności)](load-balancer-standard-availability-zones.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.17 lub nowszej.  Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Obsługa strefy dostępności jest dostępna dla wybieranych zasobów platformy Azure i regionami i rodzinami rozmiarów maszyn wirtualnych. Aby uzyskać więcej informacji na temat rozpoczynania pracy oraz zasobów platformy Azure, regionów i rodzin rozmiarów maszyn wirtualnych, można wypróbować strefy dostępności za pomocą programu, zobacz [omówienie strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroupSLB* w lokalizacji *westeurope* :

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Tworzenie strefy nadmiarowej publicznego adresu IP
Aby uzyskać dostęp do aplikacji za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Strefa — nadmiarowy fronton jest obsługiwany przez wszystkie strefy dostępności w regionie jednocześnie. Utwórz strefowo nadmiarowy publiczny adres IP za pomocą [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create). Podczas tworzenia standardowego publicznego adresu IP jest on domyślnie strefowo nadmiarowy.

Poniższy przykład tworzy strefowo nadmiarowy publiczny adres IP o nazwie *myPublicIP* w grupie zasobów *myResourceGroupLoadBalancer* .

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Tworzenie usługa Load Balancer w warstwie Standardowa platformy Azure
W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:
- Adres IP frontonu, który odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
- Pula adresów IP zaplecza, gdzie pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
- Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
- Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer"></a>Tworzenie modułu równoważenia obciążenia
Utwórz standardową usługę równoważenia obciążenia za pomocą [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). Poniższy przykład tworzy moduł równoważenia obciążenia o nazwie *myLoadBalancer* i przypisuje adres *myPublicIP* do konfiguracji adresu IP frontonu.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Utwórz sondę kondycji na porcie 80

Sonda kondycji sprawdza wszystkie wystąpienia maszyny wirtualnej, aby upewnić się, że mogą wysyłać ruch sieciowy. Wystąpienie maszyny wirtualnej, w przypadku którego sprawdzanie kondycji za pomocą sondy nie powiodło się, jest usuwane z modułu równoważenia obciążenia do momentu ponownego przejścia do trybu online i pomyślnego sprawdzenia kondycji. Utwórz sondę kondycji za pomocą AZ Network lb Probe Create, aby monitorować kondycję maszyn wirtualnych. Aby utworzyć sondę kondycji TCP, należy użyć polecenia [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). W poniższym przykładzie zostanie utworzona sonda kondycji o nazwie *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Utwórz regułę modułu równoważenia obciążenia dla portu 80
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

Utwórz sieć wirtualną o nazwie *myVnet* z podsiecią o nazwie Moja *podsieć* w liście zasobów przy użyciu polecenia [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup* , aby zdefiniować połączenia przychodzące do sieci wirtualnej za pomocą [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Utwórz regułę sieciowej grupy zabezpieczeń o nazwie *myNetworkSecurityGroupRule* dla portu 80 przy użyciu [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

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
Utwórz trzy wirtualne karty sieciowe za pomocą [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create) i skojarz je z publicznym adresem IP i grupą zabezpieczeń sieci. Poniższy przykład tworzy sześć wirtualnych kart sieciowych. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach). Możesz w dowolnym momencie utworzyć i dodać do modułu równoważenia obciążenia dodatkowe wirtualne karty sieciowe i maszyny wirtualne:

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
W tym przykładzie utworzysz trzy maszyny wirtualne znajdujące się w strefie 1, 2 i 3, które będą używane jako serwery zaplecza dla modułu równoważenia obciążenia. Należy również zainstalować NGINX na maszynach wirtualnych, aby sprawdzić, czy moduł równoważenia obciążenia został pomyślnie utworzony.

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

### <a name="create-the-zonal-virtual-machines"></a>Tworzenie maszyn wirtualnych z strefą
Utwórz maszyny wirtualne za pomocą [AZ VM Create](/cli/azure/vm#az-vm-create) w strefie 1, 2 i 3. Poniższy przykład tworzy maszynę wirtualną w każdej strefie i generuje klucze SSH, jeśli jeszcze nie istnieją:

Utwórz maszynę wirtualną w każdej strefie (Strefa 1, strefie 2 i strefa 3) lokalizacji *westeurope* .

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

Uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Następnie możesz wprowadzić publiczny adres IP w przeglądarce internetowej. Pamiętaj — kilka minut trwa przygotowanie maszyn wirtualnych zanim moduł równoważenia obciążenia rozpocznie dystrybucję ruchu do nich. Zostanie wyświetlona aplikacja z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch, jak pokazano na poniższym przykładzie:

![Uruchamianie aplikacji Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch między maszynami wirtualnymi we wszystkich trzech strefach dostępności z uruchomioną aplikacją, możesz zatrzymać maszynę wirtualną w określonej strefie i odświeżyć przeglądarkę.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](./load-balancer-standard-overview.md)



