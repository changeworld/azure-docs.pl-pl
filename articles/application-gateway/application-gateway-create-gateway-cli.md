---
title: Tworzenie bramy aplikacji — z wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji przy użyciu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: cde06aaf149785f464cd354c95c8f39575bad73b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66136142"
---
# <a name="create-an-application-gateway-using-the-azure-cli"></a>Tworzenie bramy aplikacji przy użyciu wiersza polecenia platformy Azure

Za pomocą wiersza polecenia platformy Azure można tworzyć ani zarządzać nim bram aplikacji z poziomu wiersza polecenia lub skryptów. Ten przewodnik Szybki Start dowiesz się, jak utworzyć zasoby sieciowe, serwerów wewnętrznej bazy danych i bramy aplikacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start będzie wymagać interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupAG* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych 

Utwórz sieć wirtualną i podsieć przy użyciu polecenia [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Utwórz publiczny adres IP przy użyciu polecenia [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie utworzysz dwie maszyny wirtualne, które będą używane jako serwery zaplecza dla bramy aplikacji. Zainstaluj również serwer NGINX na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.

### <a name="create-two-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych

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

Utwórz interfejsy sieciowe za pomocą polecenia [az network nic create](/cli/azure/network/nic#az-network-nic-create). Utwórz maszyny wirtualne za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Utwórz bramę aplikacji za pomocą polecenia [az network application-gateway create](/cli/azure/network/application-gateway). Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Prywatne adresy IP interfejsów sieciowych są dodawane jako serwery w puli zaplecza bramy aplikacji.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Tworzenie bramy aplikacji może potrwać kilka minut. Po utworzeniu bramy aplikacji można wyświetlić jej następujące funkcje:

- *appGatewayBackendPool* — brama aplikacji musi mieć co najmniej jedną pulę adresów zaplecza.
- *appGatewayBackendHttpSettings* — określa, że port 80 i protokół HTTP są używane do komunikacji.
- *appGatewayHttpListener* — domyślny odbiornik skojarzony z pulą *appGatewayBackendPool*.
- *appGatewayFrontendIP* — przypisuje adres *myAGPublicIPAddress* do odbiornika *appGatewayHttpListener*.
- *rule1* — domyślna reguła routingu skojarzona z odbiornikiem *appGatewayHttpListener*.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, użyj polecenia [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![Testowanie bramy aplikacji](./media/application-gateway-create-gateway-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie będą już potrzebne, możesz użyć [usunięcie grupy az](/cli/azure/group#az-group-delete) polecenia, aby usunąć grupę zasobów, usługa application gateway i wszystkie pokrewne zasoby.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start utworzono grupę zasobów, zasoby sieciowe i serwery zaplecza. Te zasoby są następnie używane do utworzenia bramy aplikacji. Aby dowiedzieć się więcej na temat bramy aplikacji i skojarzonych z nimi zasobów, przejdź do artykuły z poradami.

