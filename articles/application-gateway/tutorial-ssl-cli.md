---
title: Zakończenie protokołu TLS przy użyciu interfejsu wiersza polecenia — brama aplikacji platformy Azure
description: Dowiedz się, jak utworzyć bramę aplikacji i dodać certyfikat dla zakończenia protokołu TLS przy użyciu interfejsu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 6cd8cca65762de3da6a0e69e93c8d79bbe498dde
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311972"
---
# <a name="create-an-application-gateway-with-tls-termination-using-the-azure-cli"></a>Tworzenie bramy aplikacji z zakończeniem protokołu TLS przy użyciu interfejsu wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure można utworzyć [bramę aplikacji](overview.md) z certyfikatem zakończenia [protokołu TLS.](ssl-overview.md) W przypadku serwerów zaplecza można użyć [zestawu skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) . W tym przykładzie zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, które są dodawane do domyślnej puli zaplecza bramy aplikacji.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji z certyfikatem
> * Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu domyślnej puli zaplecza

Jeśli wolisz, możesz wykonać tę procedurę przy użyciu [programu Azure PowerShell](tutorial-ssl-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Do użycia w środowisku produkcyjnym należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę. W tym artykule można utworzyć certyfikat z podpisem własnym i plik pfx za pomocą polecenia openssl.

```console
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Wprowadź wartości odpowiednie dla Twojego certyfikatu. Możesz zaakceptować wartości domyślne.

```console
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

Utwórz sieć wirtualną o nazwie *myVNet* i podsieć o nazwie *myAGSubnet* przy użyciu polecenia [az network vnet create](/cli/azure/network/vnet). Następnie możesz dodać podsieć o nazwie *myBackendSubnet* wymaganą przez serwery zaplecza przy użyciu polecenia [az network vnet subnet create](/cli/azure/network/vnet/subnet). Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu polecenia [az network public-ip create](/cli/azure/network/public-ip).

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Aby utworzyć bramę aplikacji, możesz użyć polecenia [az network application-gateway create](/cli/azure/network/application-gateway). Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. 

Brama aplikacji jest przypisywana do wcześniej utworzonej podsieci *myAGSubnet* i adresu *myAGPublicIPAddress*. W tym przykładzie podczas tworzenia bramy aplikacji należy skojarzyć utworzony certyfikat i jego hasło. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
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

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skalowania maszyn wirtualnych, który zapewnia serwery dla domyślnej puli zaplecza w bramie aplikacji. Maszyny wirtualne w zestawie skalowania są kojarzone z podsiecią *myBackendSubnet* i pulą *appGatewayBackendPool*. Aby utworzyć zestaw skalowania, możesz użyć polecenia [az vmss create](/cli/azure/vmss#az-vmss-create).

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, możesz użyć polecenia [az network public-ip show](/cli/azure/network/public-ip).

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W tym przykładzie adres **https://52.170.203.149**URL to: .

![Ostrzeżenie o zabezpieczeniach](./media/tutorial-ssl-cli/application-gateway-secure.png)

Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz pozycję **Szczegóły**, a następnie pozycję **Przejdź do strony internetowej**. Zostanie wyświetlona zabezpieczona witryna serwera NGINX, tak jak w poniższym przykładzie:

![Testowanie podstawowego adresu URL w bramie aplikacji](./media/tutorial-ssl-cli/application-gateway-nginx.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Następne kroki

[Tworzenie bramy aplikacji hostującej wiele witryn internetowych](./tutorial-multiple-sites-cli.md)
