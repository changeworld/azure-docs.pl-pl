---
title: Utwórz bramę aplikacji z kończenia żądań SSL - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji i Dodaj certyfikat dla zakończenia połączenia SSL przy użyciu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/18/2018
ms.author: victorh
ms.openlocfilehash: cdc24d0b95e30f762eb202ce08222ccde34424e9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-ssl-termination-using-the-azure-cli"></a>Utwórz bramę aplikacji z kończenia żądań SSL przy użyciu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia Azure umożliwia tworzenie [brama aplikacji w](application-gateway-introduction.md) przy użyciu certyfikatu dla [kończenia żądań SSL](application-gateway-backend-ssl.md) używającą [zestaw skali maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) dla serwerów wewnętrznej bazy danych. W tym przykładzie zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, które są dodawane do domyślnej puli zaplecza bramy aplikacji.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz certyfikat z podpisem własnym
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji przy użyciu certyfikatu
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

Można użyć [utworzyć az sieci z bramy aplikacji](/cli/azure/application-gateway#create) Utwórz bramę aplikacji. Podczas tworzenia bramy aplikacji przy użyciu wiersza polecenia platformy Azure, należy określić informacje o konfiguracji, takie jak pojemności, jednostki sku i ustawienia protokołu HTTP. 

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

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skali maszyny wirtualnej, zapewniający serwerów domyślnej puli zaplecza bramy aplikacji. W zestawie skalowania maszyn wirtualnych są skojarzone z *myBackendSubnet* i *appGatewayBackendPool*. Aby utworzyć skali zestaw, możesz użyć [az vmss utworzyć](/cli/azure/vmss#az_vmss_create).

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

![Ostrzeżenie bezpieczne](./media/application-gateway-ssl-cli/application-gateway-secure.png)

Aby zaakceptować zabezpieczeń ostrzeżenie, jeśli używasz certyfikatu z podpisem własnym, wybierz **szczegóły** , a następnie **przejdź do strony sieci Web**. Zostanie wyświetlona zabezpieczona witryna serwera NGINX, tak jak w poniższym przykładzie:

![Podstawowy adres URL testu bramy aplikacji](./media/application-gateway-ssl-cli/application-gateway-nginx.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz certyfikat z podpisem własnym
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji przy użyciu certyfikatu
> * Utwórz zestaw z domyślnej puli zaplecza skali maszyny wirtualnej

Aby dowiedzieć się więcej na temat bram aplikacji i ich skojarzonych zasobów, nadal artykuły.
