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
ms.openlocfilehash: a4f6cc2af7b9e044e5a72767898f876932fbf973
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66133943"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — interfejs wiersza polecenia platformy Azure

Ten przewodnik Szybki Start dowiesz się, jak utworzyć bramę aplikacji za pomocą wiersza polecenia platformy Azure.  Po utworzeniu bramy application gateway, możesz go przetestować, aby upewnić się, że działa prawidłowo. Za pomocą usługi Azure Application Gateway możesz przekazać ruchu aplikacji sieci web do określonych zasobów, przypisując odbiorników portów, tworząc reguły i dodawanie zasobów do puli zaplecza. Dla uproszczenia w tym artykule używany jest prostą konfigurację obejmującą publicznego adresu IP frontonu, podstawowy odbiornik do hosta jednej lokacji w tej bramy application gateway, dwie maszyny wirtualne, które służy do puli zaplecza i regułę routingu podstawowego żądania.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, uruchom interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby znaleźć wersję, uruchom polecenie **az --version**. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Grupa zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). 

W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupAG* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Wymaganych zasobów sieciowych 

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej.  Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.  Możesz utworzyć nową podsieć dla bramy Application Gateway lub użyć istniejącego. W tym przykładzie utworzysz dwie podsieci, w tym przykładzie: jeden dla bramy application gateway i drugi dla serwerów wewnętrznej bazy danych. Można skonfigurować adresu IP frontonu bramy Application Gateway to publicznych lub prywatnych zgodnie z danego przypadku użycia. W tym przykładzie Wybierzmy publicznego adresu IP frontonu.

Aby utworzyć sieć wirtualną i podsieć, użyj polecenia [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Uruchom polecenie [az network public-ip create](/cli/azure/network/public-ip), aby utworzyć publiczny adres IP.

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

### <a name="backend-servers"></a>Serwerów wewnętrznej bazy danych

Zaplecze może składać się z kartami sieciowymi, zestawy skalowania maszyn wirtualnych, publiczne adresy IP, wewnętrzne adresy IP, w pełni kwalifikowaną nazwę (FQDN), a wielodostępne zaplecza takich jak usługa Azure App Service. W tym przykładzie utworzysz dwie maszyny wirtualne platformy Azure, które będą używane jako serwery zaplecza dla bramy aplikacji. Zainstalujesz również usługi IIS na maszynach wirtualnych, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji.

#### <a name="create-two-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych

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

Utwórz bramę aplikacji za pomocą polecenia [az network application-gateway create](/cli/azure/network/application-gateway). Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Następnie platforma Azure dodaje prywatne adresy IP interfejsów sieciowych jako serwery w puli zaplecza bramy aplikacji.

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

Po odświeżeniu przeglądarki powinna zostać wyświetlona nazwa drugiej maszyny wirtualnej. Prawidłowe odpowiedzi sprawdza, czy brama aplikacji został pomyślnie utworzony, i jest w stanie pomyślnym nawiązaniu połączenia z zapleczem.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete). Usuwając grupę zasobów, usuwasz również bramę aplikacji i wszystkie powiązane z nią zasoby.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)

