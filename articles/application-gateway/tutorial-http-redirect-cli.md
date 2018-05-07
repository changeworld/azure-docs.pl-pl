---
title: Utwórz bramę aplikacji przy użyciu certyfikatu — wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji i Dodaj certyfikat dla zakończenia połączenia SSL przy użyciu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: victorh
ms.openlocfilehash: 645ccad03997256a050b48aab7104e9fe9ae7ef3
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Utwórz bramę aplikacji protokołu HTTP do przekierowywania HTTPS przy użyciu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia Azure umożliwia tworzenie [brama aplikacji w](application-gateway-introduction.md) certyfikat dla zakończenia połączenia SSL. Reguły routingu jest używana do przekierowywania ruchu HTTP z portem HTTPS w Centrum aplikacji. W tym przykładzie tworzymy [zestaw skali maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) puli zaplecza bramy aplikacji, która zawiera dwa wystąpienia maszyny wirtualnej.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz certyfikat z podpisem własnym
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji przy użyciu certyfikatu
> * Dodaj regułę odbiornika i Przekierowanie
> * Utwórz zestaw z domyślnej puli zaplecza skali maszyny wirtualnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Utwórz certyfikat z podpisem własnym

W środowisku produkcyjnym należy zaimportować certyfikat podpisane przez zaufanego dostawcę. W tym samouczku Utwórz certyfikat z podpisem własnym i pliku pfx za pomocą polecenia biblioteki openssl.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Wprowadź wartości odpowiednich dla certyfikatu. Można zaakceptować wartości domyślne.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Wprowadź hasło dla certyfikatu. W tym przykładzie *Azure123456!* jest używany.

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

Można użyć [utworzyć az sieci z bramy aplikacji](/cli/azure/network/application-gateway#az_network_application_gateway_create) Utwórz bramę aplikacji o nazwie *myAppGateway*. Podczas tworzenia bramy aplikacji przy użyciu wiersza polecenia platformy Azure, należy określić informacje o konfiguracji, takie jak pojemności, jednostki sku i ustawienia protokołu HTTP. 

Brama aplikacji jest przypisany do *myAGSubnet* i *myAGPublicIPAddress* wcześniej utworzony. W tym przykładzie należy skojarzyć utworzony certyfikat i jego hasło po utworzeniu bramy aplikacji. 

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
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Może upłynąć kilka minut dla bramy aplikacji ma zostać utworzony. Po utworzeniu bramy aplikacji, można wyświetlić te nowe funkcje:

- *appGatewayBackendPool* -bramę aplikacji musi mieć co najmniej jedna pula adresów zaplecza.
- *appGatewayBackendHttpSettings* — Określa, że portu 80 oraz protokołu HTTP jest używany do komunikacji.
- *appGatewayHttpListener* -odbiornika domyślne skojarzone z *appGatewayBackendPool*.
- *appGatewayFrontendIP* -przypisuje *myAGPublicIPAddress* do *appGatewayHttpListener*.
- *rule1* — domyślna routingu regułę, która jest skojarzona z *appGatewayHttpListener*.

## <a name="add-a-listener-and-redirection-rule"></a>Dodaj regułę odbiornika i Przekierowanie

### <a name="add-the-http-port"></a>Dodaj HTTP port

Można użyć [tworzenie frontonu — port az brama aplikacji w sieci](/cli/azure/network/application-gateway/frontend-port#az_network_application_gateway_frontend_port_create) Aby dodać HTTP port na bramie aplikacji.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>Dodaj odbiornika HTTP

Można użyć [utworzyć az sieci bramy aplikacji odbiornik http](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create) można dodać odbiornik o nazwie *myListener* na bramie aplikacji.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Dodawanie konfiguracji przekierowania

Dodaj do konfiguracji przekierowania protokołu HTTPS przy użyciu bramy aplikacji HTTP [utworzyć brama aplikacji w sieci az przekierowania config](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>Dodaj regułę routingu

Dodaj regułę routingu o nazwie *rule2* z konfiguracją przekierowania do bramy aplikacji przy użyciu [Tworzenie reguły brama aplikacji w sieci az](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie, Utwórz zestaw o nazwie skalowania maszyny wirtualnej *myvmss* zapewnia serwery w puli zaplecza bramy aplikacji. W zestawie skalowania maszyn wirtualnych są skojarzone z *myBackendSubnet* i *appGatewayBackendPool*. Aby utworzyć skali zestaw, możesz użyć [az vmss utworzyć](/cli/azure/vmss#az_vmss_create).

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

Uruchom następujące polecenie w oknie powłoki:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/vhorne/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, można użyć [az sieci ip publicznego Pokaż](/cli/azure/network/public-ip#az_network_public_ip_show). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Ostrzeżenie bezpieczne](./media/tutorial-http-redirect-cli/application-gateway-secure.png)

Aby zaakceptować zabezpieczeń ostrzeżenie, jeśli używasz certyfikatu z podpisem własnym, wybierz **szczegóły** , a następnie **przejdź do strony sieci Web**. Zostanie wyświetlona zabezpieczona witryna serwera NGINX, tak jak w poniższym przykładzie:

![Podstawowy adres URL testu bramy aplikacji](./media/tutorial-http-redirect-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz certyfikat z podpisem własnym
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji przy użyciu certyfikatu
> * Dodaj regułę odbiornika i Przekierowanie
> * Utwórz zestaw z domyślnej puli zaplecza skali maszyny wirtualnej

> [!div class="nextstepaction"]
> [Dowiedz się więcej o co można zrobić z bramy aplikacji](application-gateway-introduction.md)
