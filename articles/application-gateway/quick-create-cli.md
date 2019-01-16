---
title: 'Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — interfejs wiersza polecenia platformy Azure | Microsoft Docs'
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć bramę Azure Application Gateway, która kieruje ruch internetowy do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d14b8c9c752c9d41a42f092662c5f3aa88840dc5
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157721"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — interfejs wiersza polecenia platformy Azure

W tym przewodniku Szybki start przedstawiono sposób użycia interfejsu wiersza polecenia platformy Azure do tworzenia bramy aplikacji z dwoma maszynami wirtualnymi w puli zaplecza. Następnie będzie można ją przetestować, aby sprawdzić, czy działa prawidłowo. Dzięki usłudze Azure Application Gateway kierujesz ruch internetowy aplikacji do określonych zasobów, przypisując odbiorniki do portów, tworząc reguły i dodając zasoby do puli zaplecza.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, uruchom interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby znaleźć wersję, uruchom polecenie **az --version**. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). 

W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupAG* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych 

Gdy utworzysz sieć wirtualną, brama aplikacji będzie mogła komunikować się z innymi zasobami. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji. W tym przykładzie tworzysz dwie podsieci: jedna dla bramy aplikacji i druga dla maszyn wirtualnych. Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.

Aby utworzyć sieć wirtualną i podsieć, użyj polecenia [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Uruchom polecenie [az network public-ip create](/cli/azure/network/public-ip#az-public-ip-create), aby utworzyć publiczny adres IP.

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

W tym przykładzie utworzysz dwie maszyny wirtualne, których platforma Azure użyje jako serwerów zaplecza dla bramy aplikacji. 

### <a name="create-two-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych

Zainstaluj [serwer internetowy NGINX](https://docs.nginx.com/nginx/) na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona. Aby zainstalować serwer NGINX i uruchomić aplikację Node.js „Hello World” na maszynie wirtualnej z systemem Linux, możesz użyć pliku konfiguracji cloud-init. Aby uzyskać więcej informacji na temat pliku cloud-init, zobacz [Cloud-init support for virtual machines in Azure (Obsługa pliku cloud-init na potrzeby maszyn wirtualnych na platformie Azure)](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

W usłudze Azure Cloud Shell skopiuj i wklej następującą konfigurację do pliku o nazwie *cloud-init.txt*. Wprowadź ciąg *editor cloud-init.txt* w celu utworzenia pliku.

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

Utwórz interfejsy sieciowe za pomocą polecenia [az network nic create](/cli/azure/network/nic#az-network-nic-create). Aby utworzyć maszyny wirtualne, użyj polecenia [az vm create](/cli/azure/vm#az-vm-create).

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

Utwórz bramę aplikacji za pomocą polecenia [az network application-gateway create](/cli/azure/network/application-gateway#az-application-gateway-create). Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Następnie platforma Azure dodaje prywatne adresy IP interfejsów sieciowych jako serwery w puli zaplecza bramy aplikacji.

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

Tworzenie bramy aplikacji na platformie Azure może potrwać do 30 minut. Po jej utworzeniu można wyświetlić następujące ustawienia w sekcji **Ustawienia** strony **Application Gateway**:

- **appGatewayBackendPool**: Znajduje się na stronie **Pule zaplecza**. Określa wymaganą pulę zaplecza.
- **appGatewayBackendHttpSettings**: Znajduje się na stronie **Ustawienia HTTP**. Określa, że brama aplikacji używa portu 80 i protokołu HTTP na potrzeby komunikacji.
- **appGatewayHttpListener**: Znajduje się na stronie **Odbiorniki**. Określa domyślny odbiornik skojarzony z pulą **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Znajduje się na stronie **Konfiguracje adresu IP frontonu**. Przypisuje on adres *myAGPublicIPAddress* do odbiornika **appGatewayHttpListener**.
- **rule1**: Znajduje się na stronie **Reguły**. Określa domyślną regułę rozsyłania skojarzoną z odbiornikiem **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Mimo że platforma Azure nie wymaga internetowego serwera NGINX do utworzenia bramy aplikacji, zainstalowano go w ramach tego przewodnika Szybki start, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Aby uzyskać publiczny adres IP nowej bramy aplikacji, użyj polecenia [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

Skopiuj i wklej publiczny adres IP na pasku adresu przeglądarki.
    
![Testowanie bramy aplikacji](./media/quick-create-cli/application-gateway-nginxtest.png)

Po odświeżeniu przeglądarki powinna zostać wyświetlona nazwa drugiej maszyny wirtualnej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete). Usuwając grupę zasobów, usuwasz również bramę aplikacji i wszystkie powiązane z nią zasoby.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)

