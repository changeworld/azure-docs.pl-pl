---
title: 'Szybki start: bezpośredni ruch internetowy przy użyciu interfejsu wiersza polecenia'
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia bramy aplikacji platformy Azure, która kieruje ruch internetowy do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f60b26756c0affffbd45c8596fdf73d11ffa8e81
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239507"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — interfejs wiersza polecenia platformy Azure

W tym przewodniku Szybki start można użyć interfejsu wiersza polecenia platformy Azure do utworzenia bramy aplikacji. Następnie przetestować go, aby upewnić się, że działa poprawnie. 

Brama aplikacji kieruje ruch sieci web aplikacji do określonych zasobów w puli wewnętrznej bazy danych. Przypisywanie odbiorników do portów, tworzenie reguł i dodawanie zasobów do puli zaplecza. Dla uproszczenia w tym artykule używa prostej konfiguracji z publicznym adresem IP front-end, odbiornika podstawowego do hostowania pojedynczej lokacji w bramie aplikacji, podstawowej reguły routingu żądań i dwóch maszyn wirtualnych w puli wewnętrznej bazy danych.

Ten przewodnik Szybki start można również ukończyć przy użyciu [programu Azure PowerShell](quick-create-powershell.md) lub [witryny Azure Portal.](quick-create-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej](/cli/azure/install-azure-cli) (jeśli uruchomisz platformę Azure CLI lokalnie).

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Tworzenie grupy zasobów `az group create`przy użyciu programu . 

W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupAG* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych 

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej.  Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.  Można utworzyć nową podsieć bramy aplikacji lub użyć istniejącej. W tym przykładzie utworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów wewnętrznej bazy danych. Można skonfigurować adres IP frontu bramy aplikacji jako publiczny lub prywatny, zgodnie z przypadkiem użycia. W tym przykładzie wybierzesz adres IP frontu publicznego.

Aby utworzyć sieć wirtualną i `az network vnet create`podsieć, użyj programu . Uruchom, `az network public-ip create` aby utworzyć publiczny adres IP.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-backend-servers"></a>Tworzenie serwerów zaplecza

Zaplecze może mieć karty sieciowe, zestawy skalowania maszyn wirtualnych, publiczne adresy IP, wewnętrzne adresy IP, w pełni kwalifikowane nazwy domen (FQDN) i zaplecze wielu dzierżawców, takie jak usługa Azure App Service. W tym przykładzie utworzysz dwie maszyny wirtualne do użycia jako serwery wewnętrznej bazy danych dla bramy aplikacji. Można również zainstalować usługi IIS na maszynach wirtualnych, aby przetestować bramę aplikacji.

#### <a name="create-two-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych

Zainstaluj serwer internetowy NGINX na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona. Aby zainstalować serwer NGINX i uruchomić aplikację Node.js „Hello World” na maszynie wirtualnej z systemem Linux, możesz użyć pliku konfiguracji cloud-init. Aby uzyskać więcej informacji na temat pliku cloud-init, zobacz [Cloud-init support for virtual machines in Azure (Obsługa pliku cloud-init na potrzeby maszyn wirtualnych na platformie Azure)](../virtual-machines/linux/using-cloud-init.md).

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

Tworzenie interfejsów sieciowych za pomocą programu `az network nic create`. Aby utworzyć maszyny wirtualne, należy użyć programu `az vm create`.

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

Tworzenie bramy aplikacji `az network application-gateway create`przy użyciu programu . Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Następnie platforma Azure dodaje prywatne adresy IP interfejsów sieciowych jako serwery w puli zaplecza bramy aplikacji.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Tworzenie bramy aplikacji na platformie Azure może potrwać do 30 minut. Po jej utworzeniu można wyświetlić następujące ustawienia w sekcji **Ustawienia** strony **Application Gateway**:

- **appGatewayBackendPool**: Znajduje się na stronie **Baseny zaplecza.** Określa wymaganą pulę zaplecza.
- **appGatewayBackendHttpSettings**: Znajduje się na stronie **ustawień HTTP.** Określa, że brama aplikacji używa portu 80 i protokołu HTTP na potrzeby komunikacji.
- **appGatewayHttpListener**: Znajduje się na **stronie Detektory**. Określa domyślny odbiornik skojarzony z pulą **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Znajduje się na stronie **konfiguracje IP frontu.** Przypisuje *myAGPublicIPAddress* do **appGatewayHttpListener**.
- **rule1**: Znajduje się na stronie **Regulamin.** Określa domyślną regułę rozsyłania skojarzoną z odbiornikiem **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Mimo że platforma Azure nie wymaga internetowego serwera NGINX do utworzenia bramy aplikacji, zainstalowano go w ramach tego przewodnika Szybki start, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Aby uzyskać publiczny adres IP nowej bramy `az network public-ip show`aplikacji, użyj programu . 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Skopiuj i wklej publiczny adres IP na pasku adresu przeglądarki.
    
![Testowanie bramy aplikacji](./media/quick-create-cli/application-gateway-nginxtest.png)

Po odświeżeniu przeglądarki powinna zostać wyświetlona nazwa drugiej maszyny wirtualnej. Oznacza to, że brama aplikacji została pomyślnie utworzona i może łączyć się z zapleczem.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne zasoby utworzone za pomocą `az group delete` bramy aplikacji, użyj polecenia, aby usunąć grupę zasobów. Po usunięciu grupy zasobów można również usunąć bramę aplikacji i wszystkie powiązane z nią zasoby.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)

