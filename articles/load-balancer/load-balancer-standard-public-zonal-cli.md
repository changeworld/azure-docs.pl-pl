---
title: Utwórz publiczny obciążenia standardowego równoważenia z zonal frontonu adres publiczny adres IP przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o tworzeniu publicznego obciążenia standardowego równoważenia z zonal frontonu adres publiczny adres IP przy użyciu wiersza polecenia platformy Azure
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
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 0932195bb95ab9610f723245bfed7fedb01001f9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30324096"
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-frontend-using-azure-cli"></a>Utwórz publiczny obciążenia standardowego równoważenia z zonal frontonu przy użyciu wiersza polecenia platformy Azure

W tym artykule opisano przez proces tworzenia publiczny [standardowe usługi równoważenia obciążenia](https://aka.ms/azureloadbalancerstandard) z zonal frontonu przy użyciu adresu publicznego adresu IP standardowa. W tym scenariuszu należy określić strefę określonego dla swoich wystąpień frontonu i zaplecza, aby były wyrównane z ścieżki danych i zasobów z określonej strefy.

Aby uzyskać więcej informacji o używaniu stref dostępności z modułem równoważenia obciążenia standardowego, zobacz [standardowego modułu równoważenia obciążenia i dostępności stref](load-balancer-standard-availability-zones.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli wybierzesz do zainstalowania i używania interfejsu wiersza polecenia lokalnie, upewnij się, że zainstalowano najnowszą [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i zalogować się do konta platformy Azure z [logowania az](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
 Obsługa stref dostępności jest dostępna dla wybierz zasobów platformy Azure i regiony i rodziny rozmiar maszyny Wirtualnej. Więcej informacji na temat rozpocząć, które zasobów platformy Azure, regiony i rodziny rozmiar maszyny Wirtualnej można spróbować stref dostępności przy, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroupLB* w *westeurope* lokalizacji:

```azurecli-interactive
az group create \
--name myResourceGroupLB \
--location westeurope
```

## <a name="create-a-zonal-public-ip-standard"></a>Utwórz zonal publicznego adresu IP standardowe
Aby uzyskać dostęp do aplikacji za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Do publicznego adresu IP utworzonego w określonej strefy zawsze istnieje tylko w tej strefie. Nie jest możliwa zmiana strefy do publicznego adresu IP.

Utwórz publiczny adres IP przy użyciu polecenia [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Poniższy przykład tworzy zonal publiczny adres IP o nazwie *myPublicIP* w *myResourceGroupLoadBalancer* grupy zasobów w strefie 1.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupLB \
--name myPublicIP \
--sku Standard \
--zone 1
```

## <a name="create-azure-load-balancer-standard"></a>Utwórz standardowy moduł równoważenia obciążenia Azure
W tej sekcji Szczegóły, jak utworzyć i skonfigurować następujące składniki usługi równoważenia obciążenia:
- Pula adresów IP frontonu, która odbiera przychodzącego ruchu sieciowego w ramach usługi równoważenia obciążenia.
- Pula IP zaplecza, gdzie puli frontonu wysyła obciążenia zrównoważonym ruchu sieciowego.
- Badanie kondycji, które określa kondycji wystąpień maszyn wirtualnych wewnętrznej bazy danych.
- reguły modułu równoważenia obciążenia, który definiuje rozkład ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer"></a>Tworzenie usługi równoważenia obciążenia
Tworzenie modułu równoważenia obciążenia standardowego z [utworzyć równoważeniem obciążenia sieciowego az](/cli/azure/network/lb#az_network_lb_create). Poniższy przykład tworzy moduł równoważenia obciążenia o nazwie *myLoadBalancer* i przypisuje *myPublicIP* adres do konfiguracji IP frontonu.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Utworzyć sondy kondycji na porcie 80

Sonda kondycji sprawdza wszystkie wystąpienia maszyny wirtualnej, aby upewnić się, że mogą wysyłać ruch sieciowy. Wystąpienie maszyny wirtualnej, w przypadku którego sprawdzanie kondycji za pomocą sondy nie powiodło się, jest usuwane z modułu równoważenia obciążenia do momentu ponownego przejścia do trybu online i pomyślnego sprawdzenia kondycji. Utworzyć sondy kondycji z badania równoważeniem obciążenia sieciowego az utworzyć w celu monitorowania kondycji maszyn wirtualnych. Aby utworzyć sondę kondycji TCP, należy użyć polecenia [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). W poniższym przykładzie zostanie utworzona sonda kondycji o nazwie *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Tworzenie reguły modułu równoważenia obciążenia dla portu 80
Reguły modułu równoważenia obciążenia definiuje konfigurację IP frontonu dla ruchu przychodzącego i puli adresów IP zaplecza, aby odbierać ruch, wraz z wymagany port źródłowy i docelowy. Tworzenie reguły modułu równoważenia obciążenia *myLoadBalancerRuleWeb* z [utworzyć regułę równoważeniem obciążenia sieciowego az](/cli/azure/network/lb/rule#az_network_lb_rule_create) do nasłuchiwania na porcie 80 w puli frontonu *myFrontEndPool* i wysyłania równoważeniem obciążenia ruchu sieciowego do puli adresów zaplecza *myBackEndPool* również przy użyciu portu 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej
Przed wdrożeniem niektórych maszyn wirtualnych i przetestować przez moduł równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Tworzenie sieci wirtualnej o nazwie *myVnet* z podsiecią o nazwie *mySubnet* za pomocą myResourceGroup [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz grupę zabezpieczeń sieci o nazwie *myNetworkSecurityGroup* do definiowania połączenia przychodzące do sieci wirtualnej z [utworzyć nsg sieci az](/cli/azure/network/nsg#az_network_nsg_create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupLB \
--name myNetworkSecurityGroup
```

Tworzenie reguły grupy zabezpieczeń sieci o nazwie *myNetworkSecurityGroupRule* dla portu 80 z [Tworzenie reguły nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupLB \
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
Utwórz trzy wirtualne karty sieciowe z [tworzenie kart interfejsu sieciowego az](/cli/azure/network/nic#az_network_nic_create) i kojarzyć je przy użyciu adresu publicznego adresu IP i grupy zabezpieczeń sieci. W poniższym przykładzie zostaną utworzone trzy wirtualne karty sieciowe. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach). Możesz w dowolnym momencie utworzyć i dodać do modułu równoważenia obciążenia dodatkowe wirtualne karty sieciowe i maszyny wirtualne:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Utwórz serwerów wewnętrznej bazy danych
W tym przykładzie utworzysz trzech maszyn wirtualnych znajdujących się w strefie 1, aby pełnić rolę serwerów wewnętrznej bazy danych dla modułu równoważenia obciążenia. Należy również zainstalować NGINX na maszynach wirtualnych, aby sprawdzić, czy usługa równoważenia obciążenia została pomyślnie utworzona.

### <a name="create-cloud-init-config"></a>Tworzenie pliku konfiguracji cloud-init

Plik konfiguracji chmury init służy do instalowania NGINX i uruchamianie aplikacji Node.js "Hello World" w maszynie wirtualnej systemu Linux. W bieżącym powłoki Utwórz plik o nazwie init.txt chmury i skopiuj i wklej następującą konfigurację w powłoce. Upewnij się, że należy skopiować cały init chmury pliku poprawnie, szczególnie pierwszy wiersz:

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
Tworzenie maszyn wirtualnych o [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy trzy maszyny wirtualne w strefie 1 i generuje klucze SSH, jeśli jeszcze nie istnieje:

```azurecli-interactive
for i in `seq 1 3`; do
 az vm create \
--resource-group myResourceGroupLB \
--name myVM$i \
--nics myNic$i \
--image UbuntuLTS \
--generate-ssh-keys \
--zone 1 \
--custom-data cloud-init.txt
done
```

## <a name="test-the-load-balancer"></a>Testowanie usługi równoważenia obciążenia
Pobierz publiczny adres IP przy użyciu usługi równoważenia obciążenia [az sieci ip publicznego Pokaż](/cli/azure/network/public-ip#az_network_public_ip_show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 

Następnie możesz wprowadzić publiczny adres IP w przeglądarce internetowej. Pamiętaj — kilka minut trwa przygotowanie maszyn wirtualnych zanim moduł równoważenia obciążenia rozpocznie dystrybucję ruchu do nich. Zostanie wyświetlona aplikacja z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch, jak pokazano na poniższym przykładzie:

![Uruchamianie aplikacji Node.js](./media/load-balancer-standard-public-zonal-cli/running-nodejs-app.png)

Aby wyświetlić moduł równoważenia obciążenia dystrybucji ruchu do maszyn wirtualnych w strefie 1 uruchomionych aplikacji, możesz można życie odświeżania przeglądarki sieci web.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia](./load-balancer-standard-overview.md).



