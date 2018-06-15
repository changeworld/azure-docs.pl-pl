---
title: Utwórz bramę aplikacji z wewnętrznego przekierowania - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji, który przekierowuje ruch w wewnętrznej sieci web do odpowiedniej puli przy użyciu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: victorh
ms.openlocfilehash: 42b45d07c8ea326f0daa8f0e6efd7cf567dbfd1b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355818"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Utwórz bramę aplikacji z wewnętrznego przekierowania przy użyciu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwiają skonfigurowanie [przekierowania ruchu w sieci web](application-gateway-multi-site-overview.md) podczas tworzenia [brama aplikacji w](application-gateway-introduction.md). W tym samouczku utworzysz puli wewnętrznej bazy danych przy użyciu zestawu skalowania maszyn wirtualnych. Następnie skonfiguruj odbiorników i reguły na podstawie domen, do których należą do upewnij się, że ruch w sieci web dociera do odpowiedniej puli. Ten samouczek zakłada, że masz wiele domen i używa przykłady *www.contoso.com* i *www.contoso.org*.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji
> * Dodaj odbiorników i reguły przekierowania
> * Utwórz zestaw z puli zaplecza skali maszyny wirtualnej
> * Utwórz rekord CNAME w domenie

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

Utwórz sieć wirtualną o nazwie *myVNet* i podsieć o nazwie *myAGSubnet* przy użyciu [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_net). Następnie można dodać podsieci o nazwie *myBackendSubnet* są one wymagane przez pulę zaplecza serwerów za pomocą [Utwórz podsieć sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu [utworzyć az sieci publicznej ip](/cli/azure/public-ip#az_network_public_ip_create).

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


## <a name="add-listeners-and-rules"></a>Dodaj odbiorników i reguł 

Odbiornik wymaganego do włączenia usługi Brama aplikacji w celu kierowania ruchu odpowiednio do puli wewnętrznej bazy danych. W tym samouczku utworzysz dwa odbiorniki dla dwóch domen. W tym przykładzie odbiorników są tworzone dla domen z *www.contoso.com* i *www.contoso.org*.

Dodaj odbiorników wewnętrznej bazy danych, które są niezbędne do kierowania ruchu przy użyciu [utworzyć az sieci bramy aplikacji odbiornik http](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>Dodawanie konfiguracji przekierowania

Dodawanie konfiguracji przekierowania, który wysyła ruch z *www.consoto.org* odbiornika dla *www.contoso.com* w bramy aplikacji przy użyciu [az sieci bramy aplikacji Tworzenie konfiguracji przekierowania](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoComListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Dodaj zasady routingu

Reguły są przetwarzane w kolejności, w której zostały utworzone, a ruch jest przekierowywany przy użyciu pierwszej reguły, która odpowiada adresowi URL wysłanych do bramy aplikacji. Podstawowe reguły domyślne, który został utworzony, nie jest wymagana w tym samouczku. W tym przykładzie zostanie utworzenie dwóch nowych reguł o nazwie *contosoComRule* i *contosoOrgRule* i usunąć domyślnej reguły, który został utworzony.  Można dodać reguły za pomocą [Tworzenie reguły brama aplikacji w sieci az](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Utwórz zestawy skalowania maszyny wirtualnej

W tym przykładzie utworzysz zestaw skali maszyny wirtualnej, który obsługuje domyślnej puli wewnętrznej bazy danych, który został utworzony. Zestaw skalowania, utworzonej nazwie *myvmss* i zawiera dwa wystąpienia maszyny wirtualnej, na które należy zainstalować NGINX.

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Instalowanie serwera NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>Utwórz rekord CNAME w domenie

Po utworzeniu bramy aplikacji z publicznego adresu IP można pobrać adresu DNS i użyj go, aby utworzyć rekord CNAME w domenie. Można użyć [az sieci ip publicznego Pokaż](/cli/azure/network/public-ip#az_network_public_ip_show) można pobrać adresu DNS bramy aplikacji. Kopiuj *fqdn* wartość DNSSettings i używać go jako wartość rekord CNAME, który utworzono. Użycie rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Wpisz nazwę domeny na pasku adresu przeglądarki. Takie jak http://www.contoso.com.

![Lokacja contoso testu bramy aplikacji](./media/tutorial-internal-site-redirect-cli/application-gateway-nginxtest.png)

Zmień adres do Twojej domeny, na przykład http://www.contoso.org i należy sprawdzić, czy ruch został przekierowany do odbiornika dla www.contoso.com.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji
> * Dodaj odbiorników i reguły przekierowania
> * Utwórz zestaw z puli zaplecza skali maszyny wirtualnej
> * Utwórz rekord CNAME w domenie

> [!div class="nextstepaction"]
> [Dowiedz się więcej o co można zrobić z bramy aplikacji](./application-gateway-introduction.md)