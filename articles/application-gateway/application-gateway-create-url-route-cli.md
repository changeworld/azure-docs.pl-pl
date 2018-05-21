---
title: Utwórz bramę aplikacji z adresu URL na podstawie ścieżki reguły routingu - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć adres URL na podstawie ścieżki reguły routingu dla aplikacji bramy i maszyny wirtualnej zestaw skalowania przy użyciu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: ff02688d59f32641319e6816dc55744422c62eeb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-the-azure-cli"></a>Utwórz bramę aplikacji z adresu URL na podstawie ścieżki reguły routingu przy użyciu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwiają skonfigurowanie [reguł routingu na podstawie ścieżki adresu URL](application-gateway-url-route-overview.md) podczas tworzenia [brama aplikacji w](application-gateway-introduction.md). W tym samouczku, tworzenia pul zaplecza przy użyciu [zestaw skali maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Następnie można utworzyć reguły routingu, które upewnij się, że ruch w sieci web dociera do odpowiednich serwerów w pulach.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji z mapą adresu URL
> * Utwórz zestawy skalowania maszyny wirtualnej z pul zaplecza

![Przykład routingu adresów URL](./media/application-gateway-create-url-route-cli/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Tworzenie grupy zasobów przy użyciu [Tworzenie grupy az](/cli/azure/group#create).

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroupAG* w *eastus* lokalizacji.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych 

Utwórz sieć wirtualną o nazwie *myVNet* i podsieć o nazwie *myAGSubnet* przy użyciu [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_net). Następnie można dodać podsieci o nazwie *myBackendSubnet* są one wymagane serwerów wewnętrznej bazy danych przy użyciu [Utwórz podsieć sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu [utworzyć az sieci publicznej ip](/cli/azure/public-ip#az_network_public_ip_create).

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
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway-with-url-map"></a>Utwórz bramę aplikacji z adresu URL mapy

Można użyć [utworzyć az sieci z bramy aplikacji](/cli/azure/application-gateway#create) Utwórz bramę aplikacji o nazwie *myAppGateway*. Podczas tworzenia bramy aplikacji przy użyciu wiersza polecenia platformy Azure, należy określić informacje o konfiguracji, takie jak pojemności, jednostki sku i ustawienia protokołu HTTP. Brama aplikacji jest przypisany do *myAGSubnet* i *myAGPublicIPAddress* wcześniej utworzony. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Może upłynąć kilka minut dla bramy aplikacji ma zostać utworzony. Po utworzeniu bramy aplikacji, można wyświetlić te nowe funkcje:

- *appGatewayBackendPool* -bramę aplikacji musi mieć co najmniej jedna pula adresów zaplecza.
- *appGatewayBackendHttpSettings* — Określa, że portu 80 oraz protokołu HTTP jest używany do komunikacji.
- *appGatewayHttpListener* -odbiornika domyślne skojarzone z *appGatewayBackendPool*.
- *appGatewayFrontendIP* -przypisuje *myAGPublicIPAddress* do *appGatewayHttpListener*.
- *rule1* — domyślna routingu regułę, która jest skojarzona z *appGatewayHttpListener*.


### <a name="add-image-and-video-backend-pools-and-port"></a>Dodawanie obrazu i wideo pul zaplecza i portu

Można dodać puli wewnętrznej bazy danych o nazwie *imagesBackendPool* i *videoBackendPool* z bramą aplikacji przy użyciu [az brama aplikacji w puli adresów sieciowych — tworzenie](/cli/azure/application-gateway#az_network_application_gateway_address-pool_create). Dodawanie portu frontonu dla pul przy użyciu [tworzenie frontonu — port az brama aplikacji w sieci](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool
az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>Dodaj odbiornika wewnętrznej bazy danych

Dodaj odbiornika wewnętrznej bazy danych o nazwie *backendListener* który jest potrzebne do kierowania ruchu przy użyciu [utworzyć az sieci bramy aplikacji odbiornik http](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Dodaj mapę ścieżki adresu URL

Adres URL ścieżki mapy upewnij się, że określone adresy URL są kierowane do pul zaplecza określone. Można utworzyć mapowania ścieżki adresu URL o nazwie *imagePathRule* i *videoPathRule* przy użyciu [utworzyć az sieci Brama aplikacji w url ścieżka map](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_create) i [az sieci Tworzenie reguły adresu url ścieżki mapy bramy aplikacji](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_rule_create)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule
az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>Dodaj regułę routingu

Reguły routingu kojarzy mapy adresu URL przy użyciu odbiornika, który został utworzony. Można dodać reguły o nazwie *rule2* przy użyciu [Tworzenie reguły brama aplikacji w sieci az](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Utwórz zestawy skalowania maszyny wirtualnej

W tym przykładzie utworzysz trzy zestawy skalowania maszyn wirtualnych, które obsługują trzy pule zaplecza, które zostały utworzone. Zestawy skalowania, które możesz utworzyć są nazywane *myvmss1*, *myvmss2*, i *myvmss3*. Każdy zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, na które należy zainstalować NGINX.

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Instalowanie serwera NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, można użyć [az sieci ip publicznego Pokaż](/cli/azure/network/public-ip#az_network_public_ip_show). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Takie jak *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm*, lub *http://40.121.222.19:8080/video/test.htm*.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Podstawowy adres URL testu bramy aplikacji](./media/application-gateway-create-url-route-cli/application-gateway-nginx.png)

Zmień adres URL do http://<ip-address>:8080/video/test.html na końcu podstawowy adres URL i powinny zostać wyświetlone informacje, jak w następującym przykładzie:

![Adres URL obrazów testu w bramy aplikacji](./media/application-gateway-create-url-route-cli/application-gateway-nginx-images.png)

Zmień adres URL do http://<ip-address>:8080/video/test.html i powinny zostać wyświetlone informacje, jak w następującym przykładzie.

![Testuj adres URL wideo w bramy aplikacji](./media/application-gateway-create-url-route-cli/application-gateway-nginx-video.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji z mapą adresu URL
> * Utwórz zestawy skalowania maszyny wirtualnej z pul zaplecza

Aby dowiedzieć się więcej na temat bram aplikacji i ich skojarzonych zasobów, nadal artykuły.
