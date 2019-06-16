---
title: Tworzenie bramy aplikacji przy użyciu certyfikatu — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji i dodać certyfikat terminacji SSL przy użyciu interfejsu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.openlocfilehash: fb7d84264b8de8833d7dec2a8b22dc23995c17bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133604"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Tworzenie bramy aplikacji za pośrednictwem protokołu HTTP do przekierowania protokołu HTTPS przy użyciu wiersza polecenia platformy Azure

Można użyć wiersza polecenia platformy Azure, aby utworzyć [bramy application gateway](application-gateway-introduction.md) przy użyciu certyfikatu dla kończenia żądań SSL. Reguła routingu jest używany do przekierowywania ruchu HTTP do portu HTTPS w Twojej bramy application gateway. W tym przykładzie tworzymy [zestawu skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) dla puli zaplecza usługi application gateway, która zawiera dwa wystąpienia maszyn wirtualnych.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji z certyfikatem
> * Dodaj regułę odbiornik i Przekierowanie
> * Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu domyślnej puli zaplecza

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Do użycia w środowisku produkcyjnym należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę. W tym samouczku utworzysz certyfikat z podpisem własnym i plik pfx za pomocą polecenia biblioteki openssl.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Wprowadź wartości odpowiednie dla Twojego certyfikatu. Możesz zaakceptować wartości domyślne.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Wprowadź hasło certyfikatu. W tym przykładzie używane jest hasło *Azure123456!* .

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group).

W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupAG* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Utwórz sieć wirtualną o nazwie *myVNet* i podsieć o nazwie *myAGSubnet* przy użyciu polecenia [az network vnet create](/cli/azure/network/vnet). Następnie możesz dodać podsieć o nazwie *myBackendSubnet* wymaganą przez serwery zaplecza przy użyciu polecenia [az network vnet subnet create](/cli/azure/network/vnet/subnet). Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu polecenia [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create).

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

Można użyć polecenia [az network application-gateway create](/cli/azure/network/application-gateway) w celu utworzenia bramy aplikacji o nazwie *myAppGateway*. Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. 

Brama aplikacji jest przypisywana do wcześniej utworzonej podsieci *myAGSubnet* i adresu *myAGPublicIPAddress*. W tym przykładzie podczas tworzenia bramy aplikacji należy skojarzyć utworzony certyfikat i jego hasło. 

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

 Tworzenie bramy aplikacji może potrwać kilka minut. Po utworzeniu bramy aplikacji możesz zauważyć następujące nowe funkcje:

- *appGatewayBackendPool* — brama aplikacji musi mieć co najmniej jedną pulę adresów zaplecza.
- *appGatewayBackendHttpSettings* — określa, że port 80 i protokół HTTP są używane do komunikacji.
- *appGatewayHttpListener* — domyślny odbiornik skojarzony z pulą *appGatewayBackendPool*.
- *appGatewayFrontendIP* — przypisuje adres *myAGPublicIPAddress* do odbiornika *appGatewayHttpListener*.
- *rule1* — domyślna reguła routingu skojarzona z odbiornikiem *appGatewayHttpListener*.

## <a name="add-a-listener-and-redirection-rule"></a>Dodaj regułę odbiornik i Przekierowanie

### <a name="add-the-http-port"></a>Dodaj HTTP port

Możesz użyć [tworzenie az sieci application-gateway frontend-port](/cli/azure/network/application-gateway/frontend-port) Aby dodać HTTP port do usługi application gateway.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>Dodawanie odbiornika HTTP

Możesz użyć [tworzenie az sieci application-gateway http-listener](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) do dodania odbiornika o nazwie *myListener* do usługi application gateway.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Dodaj konfigurację przekierowania

Dodaj HTTP do HTTPS konfiguracji przekierowania do bramy aplikacji przy użyciu [tworzenie az sieci bramy application gateway redirect-config](/cli/azure/network/application-gateway/redirect-config).

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

Dodaj regułę routingu o nazwie *reguły rule2* z konfiguracją przekierowania do bramy aplikacji przy użyciu [Tworzenie reguły bramy aplikacji sieciowej az](/cli/azure/network/application-gateway/rule).

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

W tym przykładzie tworzenia maszyny wirtualnej zestawu skalowania o nazwie *myvmss* zapewnia serwerów w puli zaplecza w usłudze application gateway. Maszyny wirtualne w zestawie skalowania są kojarzone z podsiecią *myBackendSubnet* i pulą *appGatewayBackendPool*. Aby utworzyć zestaw skalowania, możesz użyć polecenia [az vmss create](/cli/azure/vmss#az-vmss-create).

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, możesz użyć polecenia [az network public-ip show](/cli/azure/network/public-ip). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Ostrzeżenie o zabezpieczeniach](./media/tutorial-http-redirect-cli/application-gateway-secure.png)

Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz pozycję **Szczegóły**, a następnie pozycję **Przejdź do strony internetowej**. Zostanie wyświetlona zabezpieczona witryna serwera NGINX, tak jak w poniższym przykładzie:

![Testowanie podstawowego adresu URL w bramie aplikacji](./media/tutorial-http-redirect-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji z certyfikatem
> * Dodaj regułę odbiornik i Przekierowanie
> * Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu domyślnej puli zaplecza

> [!div class="nextstepaction"]
> [Więcej informacji na temat funkcji bramy aplikacji](application-gateway-introduction.md)
