---
title: Utwórz bramę aplikacji z wielu lokacji hosting - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramy aplikacji, która obsługuje wiele lokacji przy użyciu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 7728ade5f84cd97f3ff0cebd0d5545d68d0ebd79
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Utwórz bramę aplikacji z wielu lokacji hostingu za pomocą wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwiają skonfigurowanie [obsługujący wiele witryn sieci web](application-gateway-multi-site-overview.md) podczas tworzenia [brama aplikacji w](application-gateway-introduction.md). W tym samouczku utworzysz pul zaplecza przy użyciu zestawów skalowania maszyn wirtualnych. Następnie skonfiguruj odbiorników i reguły na podstawie domen, do których należą do upewnij się, że ruch w sieci web dociera do odpowiednich serwerów w pulach. Ten samouczek zakłada, że masz wiele domen i używa przykłady *www.contoso.com* i *www.fabrikam.com*.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji
> * Tworzenie odbiorników i reguły routingu
> * Utwórz zestawy skalowania maszyny wirtualnej z pul zaplecza
> * Utwórz rekord CNAME w domenie

![Przykład routingu obejmujący wiele lokacji](./media/tutorial-multisite-cli/scenario.png)

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

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

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

### <a name="add-the-backend-pools"></a>Dodawanie pul zaplecza

Dodawanie pul zaplecza o nazwie *contosoPool* i *fabrikamPool* niezbędnych do serwerów wewnętrznej bazy danych przy użyciu zawierają [az brama aplikacji w puli adresów sieciowych — tworzenie](/cli/azure/application-gateway#az_network_application_gateway_address_pool_create).

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Dodawanie obiektów nasłuchujących

Odbiornik wymaganego do włączenia usługi Brama aplikacji w celu kierowania ruchu odpowiednio do puli wewnętrznej bazy danych. W tym samouczku utworzysz dwa odbiorniki dla dwóch domen. W tym przykładzie odbiorników są tworzone dla domen z *www.contoso.com* i *www.fabrikam.com*. 

Dodaj odbiorników o nazwie *contosoListener* i *fabrikamListener* niezbędnych do kierowania ruchu przy użyciu [utworzyć az sieci bramy aplikacji odbiornik http](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Dodaj zasady routingu

Reguły są przetwarzane w kolejności, w której zostały utworzone, a ruch jest przekierowywany przy użyciu pierwszej reguły, która odpowiada adresowi URL wysłanych do bramy aplikacji. Na przykład jeśli utworzono regułę przy użyciu odbiornika podstawowe i regułę przy użyciu odbiornika obejmujący wiele lokacji zarówno w tym samym porcie, reguły z wieloma lokacjami odbiornika musi być wymieniona przed regułę przy użyciu podstawowego odbiornika w kolejności reguły obejmujący wiele lokacji, aby działać zgodnie z oczekiwaniami. 

W tym przykładzie utworzenie dwóch nowych reguł i usunąć domyślnej reguły, który został utworzony podczas tworzenia bramy aplikacji. Można dodać reguły przy użyciu [Tworzenie reguły brama aplikacji w sieci az](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Utwórz zestawy skalowania maszyny wirtualnej

W tym przykładzie utworzysz trzy zestawy skalowania maszyn wirtualnych obsługujących trzy pule zaplecza bramy aplikacji. Zestawy skalowania, które możesz utworzyć są nazywane *myvmss1*, *myvmss2*, i *myvmss3*. Każdy zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, na które należy zainstalować NGINX.

```azurecli-interactive
for i in `seq 1 2`; do
  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi
  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
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
for i in `seq 1 2`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>Utwórz rekord CNAME w domenie

Po utworzeniu bramy aplikacji z publicznego adresu IP można pobrać adresu DNS i użyj go, aby utworzyć rekord CNAME w domenie. Można użyć [az sieci ip publicznego Pokaż](/cli/azure/network/public-ip#az_network_public_ip_show) można pobrać adresu DNS bramy aplikacji. Kopiuj *fqdn* wartość DNSSettings i używać go jako wartość rekord CNAME, który utworzono. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Użycie rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Wpisz nazwę domeny na pasku adresu przeglądarki. Takie jak http://www.contoso.com.

![Lokacja contoso testu bramy aplikacji](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

Zmienić adres na inne domeny i powinny zostać wyświetlone informacje, jak w następującym przykładzie:

![Testowanie witryny firmy fabrikam w bramy aplikacji](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji
> * Tworzenie odbiorników i reguły routingu
> * Utwórz zestawy skalowania maszyny wirtualnej z pul zaplecza
> * Utwórz rekord CNAME w domenie

> [!div class="nextstepaction"]
> [Dowiedz się więcej o co można zrobić z bramy aplikacji](application-gateway-introduction.md)