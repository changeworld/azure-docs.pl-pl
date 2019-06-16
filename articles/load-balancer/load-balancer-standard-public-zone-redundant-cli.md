---
title: Równoważenie obciążenia strefowo nadmiarowe maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć publiczny moduł równoważenia obciążenia standardowego przy użyciu strefy nadmiarowe serwera sieci Web przy użyciu wiersza polecenia platformy Azure
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: kumud
ms.openlocfilehash: 8f1bf9b9070f2db2376de9cb0a0602eaea98b47e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147675"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Równoważenie obciążenia maszyn wirtualnych we wszystkich strefach dostępności przy użyciu wiersza polecenia platformy Azure

W tym artykule opisano proces tworzenia publicznego [Balancer w warstwie standardowa](https://aka.ms/azureloadbalancerstandard) z strefowo nadmiarowe serwera sieci Web do strefy — nadmiarowość, bez konieczności korzystania z wielu rekordów DNS. Pojedynczy adres IP frontonu jest automatycznie strefowo nadmiarowe.  Za pomocą frontonu nadmiarowe strefy dla modułu równoważenia obciążenia, za pomocą pojedynczego adresu IP możesz teraz uzyskiwać dostęp dowolnej maszyny Wirtualnej w sieci wirtualnej w danym regionie, który jest we wszystkich strefach dostępności. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych.

Aby uzyskać więcej informacji na temat obsługi stref dostępności przy użyciu usługi Load Balancer w warstwie Standardowa, zobacz [Standard Load Balancer and Availability Zones (Usługa Load Balancer w warstwie Standardowa i strefy dostępności)](load-balancer-standard-availability-zones.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten samouczek wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.17 lub nowszej.  Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Obsługa strefy dostępności jest dostępna dla wybieranych zasobów platformy Azure i regionami i rodzinami rozmiarów maszyn wirtualnych. Aby uzyskać więcej informacji na temat rozpocząć pracę i które zasoby platformy Azure, regionów i rodzinami rozmiarów maszyn wirtualnych można wypróbować strefy dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroupSLB* w *westeurope* lokalizacji:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Tworzenie strefowo nadmiarowej publicznych adresów IP w warstwie standardowa
Aby uzyskać dostęp do aplikacji za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Strefowo nadmiarowe frontonu jest obsługiwany przez wszystkie strefy dostępności w regionie jednocześnie. Tworzenie strefy nadmiarowy publiczny adres IP z [tworzenie sieci az public-ip](/cli/azure/network/public-ip#az-network-public-ip-create). Podczas tworzenia standardowego publicznego adresu IP jest domyślnie strefowo nadmiarowe.

Poniższy przykład tworzy strefę nadmiarowy publiczny adres IP o nazwie *myPublicIP* w *myResourceGroupLoadBalancer* grupy zasobów.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Tworzenie modułu równoważenia obciążenia platformy Azure Standard
W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:
- Adres IP frontonu, który odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
- Pula adresów IP zaplecza, gdzie pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
- Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
- Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer"></a>Tworzenie modułu równoważenia obciążenia
Tworzenie standardowego modułu równoważenia obciążenia za pomocą [tworzenie równoważenia obciążenia sieciowego az](/cli/azure/network/lb#az-network-lb-create). Poniższy przykład tworzy moduł równoważenia obciążenia o nazwie *myLoadBalancer* i przypisuje *myPublicIP* adresu z konfiguracją IP frontonu.

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

Sonda kondycji sprawdza wszystkie wystąpienia maszyny wirtualnej, aby upewnić się, że mogą wysyłać ruch sieciowy. Wystąpienie maszyny wirtualnej, w przypadku którego sprawdzanie kondycji za pomocą sondy nie powiodło się, jest usuwane z modułu równoważenia obciążenia do momentu ponownego przejścia do trybu online i pomyślnego sprawdzenia kondycji. Utwórz sondę kondycji z sondą modułu równoważenia obciążenia sieciowego az utworzyć w celu monitorowania kondycji maszyn wirtualnych. Aby utworzyć sondę kondycji TCP, należy użyć polecenia [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). W poniższym przykładzie zostanie utworzona sonda kondycji o nazwie *myHealthProbe*:

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

Tworzenie sieci wirtualnej o nazwie *myVnet* z podsiecią o nazwie *mySubnet* za pomocą myResourceGroup [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup* do zdefiniowania połączeń przychodzących do sieci wirtualnej przy użyciu [tworzenie az sieciowej](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Utwórz regułę sieciowej grupy zabezpieczeń o nazwie *myNetworkSecurityGroupRule* dla portu 80 z [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

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
Utwórz trzy wirtualne karty sieciowe z [tworzenie az sieciowego](/cli/azure/network/nic#az-network-nic-create) i skojarz je z publicznym adresem IP i sieciową grupą zabezpieczeń. Poniższy przykład tworzy sześć wirtualnych kart sieciowych. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach). Możesz w dowolnym momencie utworzyć i dodać do modułu równoważenia obciążenia dodatkowe wirtualne karty sieciowe i maszyny wirtualne:

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
W tym przykładzie utworzysz trzy maszyny wirtualne znajdujące się w strefie 1, (strefa 2) i w strefie 3, aby służyć jako serwery zaplecza dla modułu równoważenia obciążenia. Zainstalujesz także serwer NGINX na maszynach wirtualnych, aby sprawdzić, czy moduł równoważenia obciążenia została pomyślnie utworzona.

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

### <a name="create-the-zonal-virtual-machines"></a>Tworzenie strefowej maszyn wirtualnych
Tworzenie maszyn wirtualnych za pomocą [tworzenie az vm](/cli/azure/vm#az-vm-create) w strefie 1, strefa 2 i w strefie 3. Poniższy przykład tworzy maszynę Wirtualną w każdej strefie i generowanie kluczy SSH, jeśli jeszcze nie istnieje:

Utwórz Maszynę wirtualną w każdej strefie (strefa 1, w strefie 2 i Strefa 3) *westeurope* lokalizacji.

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

Uzyskaj publiczny adres IP modułu równoważenia obciążenia przy użyciu [az sieci public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Następnie możesz wprowadzić publiczny adres IP w przeglądarce internetowej. Pamiętaj — kilka minut trwa przygotowanie maszyn wirtualnych zanim moduł równoważenia obciążenia rozpocznie dystrybucję ruchu do nich. Zostanie wyświetlona aplikacja z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch, jak pokazano na poniższym przykładzie:

![Uruchamianie aplikacji Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Aby zobaczyć moduł równoważenia obciążenia rozdziela ruch między maszynami wirtualnymi w wszystkich trzech strefach dostępności, z tą aplikacją, można zatrzymać maszyny Wirtualnej w określonej strefy i odświeżyć przeglądarkę.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](./load-balancer-standard-overview.md)



