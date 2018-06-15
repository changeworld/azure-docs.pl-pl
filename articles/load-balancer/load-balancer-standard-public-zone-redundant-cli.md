---
title: Równoważenie obciążenia strefowo nadmiarowy maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o tworzeniu publicznego obciążenia standardowego równoważenia z frontonu nadmiarowe strefy przy użyciu wiersza polecenia platformy Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: kumud
ms.openlocfilehash: e469311609909e3453015702fca7d015a4e72398
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34273970"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Równoważenie obciążenia maszyn wirtualnych we wszystkich strefach dostępności przy użyciu wiersza polecenia platformy Azure

W tym artykule opisano przez proces tworzenia publiczny [standardowe usługi równoważenia obciążenia](https://aka.ms/azureloadbalancerstandard) z strefowo nadmiarowy frontonu, aby osiągnąć redundancję strefy bez zależności na wiele rekordów DNS. Pojedynczy adres IP frontonu jest automatycznie strefowo nadmiarowy.  Dla Twojej usługi równoważenia obciążenia, z jednym adresem IP przy użyciu frontonu nadmiarowe strefy można teraz osiągnąć żadnej maszyny Wirtualnej w sieci wirtualnej w regionie, który jest we wszystkich strefach dostępności. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych.

Aby uzyskać więcej informacji o używaniu stref dostępności z modułem równoważenia obciążenia standardowego, zobacz [standardowego modułu równoważenia obciążenia i dostępności stref](load-balancer-standard-availability-zones.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli wybierzesz do zainstalowania i używania interfejsu wiersza polecenia lokalnie, w tym samouczku wymaga czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.17 lub nowszej.  Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Obsługa stref dostępności jest dostępna dla wybierz zasobów platformy Azure i regiony i rodziny rozmiar maszyny Wirtualnej. Więcej informacji na temat rozpocząć, które zasobów platformy Azure, regiony i rodziny rozmiar maszyny Wirtualnej można spróbować stref dostępności przy, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroupSLB* w *westeurope* lokalizacji:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Utwórz strefę nadmiarowe publicznego adresu IP standardowe
Aby uzyskać dostęp do aplikacji za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Strefowo nadmiarowy frontonu jest obsługiwana przez wszystkie strefy dostępności w regionie jednocześnie. Tworzenie strefy nadmiarowe publiczny adres IP z [AzureRmPublicIpAddress nowy](/powershell/module/azurerm.network/new-azurermpublicipaddress). Podczas tworzenia standardowych publiczny adres IP jest obszar strefowo nadmiarowy domyślnie.

Poniższy przykład tworzy strefę nadmiarowe publiczny adres IP o nazwie *myPublicIP* w *myResourceGroupLoadBalancer* grupy zasobów.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-load-balancer-standard"></a>Utwórz standardowy moduł równoważenia obciążenia Azure
W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:
- Adres IP frontonu, który odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
- Pula adresów IP zaplecza, gdzie pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
- Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
- Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer"></a>Tworzenie modułu równoważenia obciążenia
Tworzenie modułu równoważenia obciążenia standardowego z [utworzyć równoważeniem obciążenia sieciowego az](/cli/azure/network/lb#az_network_lb_create). Poniższy przykład tworzy moduł równoważenia obciążenia o nazwie *myLoadBalancer* i przypisuje *myPublicIP* adres do konfiguracji IP frontonu.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Utworzyć sondy kondycji na porcie 80

Sonda kondycji sprawdza wszystkie wystąpienia maszyny wirtualnej, aby upewnić się, że mogą wysyłać ruch sieciowy. Wystąpienie maszyny wirtualnej, w przypadku którego sprawdzanie kondycji za pomocą sondy nie powiodło się, jest usuwane z modułu równoważenia obciążenia do momentu ponownego przejścia do trybu online i pomyślnego sprawdzenia kondycji. Utworzyć sondy kondycji z badania równoważeniem obciążenia sieciowego az utworzyć w celu monitorowania kondycji maszyn wirtualnych. Aby utworzyć sondę kondycji TCP, należy użyć polecenia [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). W poniższym przykładzie zostanie utworzona sonda kondycji o nazwie *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Tworzenie reguły modułu równoważenia obciążenia dla portu 80
Reguła modułu równoważenia obciążenia definiuje konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myLoadBalancerRuleWeb* za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) w celu nasłuchiwania na porcie 80 w puli frontonu *myFrontEndPool* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *myBackEndPool*, również przy użyciu portu 80.

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

Tworzenie sieci wirtualnej o nazwie *myVnet* z podsiecią o nazwie *mySubnet* za pomocą myResourceGroup [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz grupę zabezpieczeń sieci o nazwie *myNetworkSecurityGroup* do definiowania połączenia przychodzące do sieci wirtualnej z [utworzyć nsg sieci az](/cli/azure/network/nsg#az_network_nsg_create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Tworzenie reguły grupy zabezpieczeń sieci o nazwie *myNetworkSecurityGroupRule* dla portu 80 z [Tworzenie reguły nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

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
Utwórz trzy wirtualne karty sieciowe z [tworzenie kart interfejsu sieciowego az](/cli/azure/network/nic#az_network_nic_create) i kojarzyć je przy użyciu adresu publicznego adresu IP i grupy zabezpieczeń sieci. Poniższy przykład tworzy sześciu wirtualnych kart sieciowych. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach). Możesz w dowolnym momencie utworzyć i dodać do modułu równoważenia obciążenia dodatkowe wirtualne karty sieciowe i maszyny wirtualne:

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
W tym przykładzie utworzysz trzech maszyn wirtualnych znajdujących się w strefy 1, 2 strefy i 3, aby pełnić rolę serwerów wewnętrznej bazy danych dla modułu równoważenia obciążenia. Należy również zainstalować NGINX na maszynach wirtualnych, aby sprawdzić, czy usługa równoważenia obciążenia została pomyślnie utworzona.

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

### <a name="create-the-zonal-virtual-machines"></a>Tworzenie zonal maszyn wirtualnych
Tworzenie maszyn wirtualnych o [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) w strefy 1, 2 strefy i 3. Poniższy przykład tworzy Maszynę wirtualną w każdej strefie i generuje klucze SSH, jeśli jeszcze nie istnieje:

Utwórz maszynę Wirtualną w każdej strefie (strefy 1, zone2 i 3) z *westeurope* lokalizacji.

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

Pobierz publiczny adres IP przy użyciu usługi równoważenia obciążenia [az sieci ip publicznego Pokaż](/cli/azure/network/public-ip#az_network_public_ip_show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Następnie możesz wprowadzić publiczny adres IP w przeglądarce internetowej. Pamiętaj — kilka minut trwa przygotowanie maszyn wirtualnych zanim moduł równoważenia obciążenia rozpocznie dystrybucję ruchu do nich. Zostanie wyświetlona aplikacja z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch, jak pokazano na poniższym przykładzie:

![Uruchamianie aplikacji Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Aby zobaczyć dystrybucji ruchu na maszynach wirtualnych we wszystkich strefach dostępności trzy z tą aplikacją usługi równoważenia obciążenia, można zatrzymać maszyny Wirtualnej w ramach określonej strefy i odświeżyć przeglądarkę.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](./load-balancer-standard-overview.md)



