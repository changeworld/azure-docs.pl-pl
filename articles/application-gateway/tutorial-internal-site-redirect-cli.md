---
title: Tworzenie bramy aplikacji za pomocą wewnętrznego przekierowania — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
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
ms.date: 7/14/2018
ms.author: victorh
ms.openlocfilehash: 186d0bb9161d70d9e458d25dc1b9cbe518bb790e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133551"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Tworzenie bramy aplikacji za pomocą wewnętrznego przekierowania przy użyciu wiersza polecenia platformy Azure

Można użyć wiersza polecenia platformy Azure, aby skonfigurować [przekierowywanie ruchu w sieci web](application-gateway-multi-site-overview.md) po utworzeniu [bramy application gateway](application-gateway-introduction.md). W tym samouczku utworzysz puli zaplecza przy użyciu zestawu skalowania maszyn wirtualnych. Następnie należy skonfigurować detektory i reguły na podstawie domen, których jesteś właścicielem, aby upewnić się, że ruch w sieci web dociera do odpowiedniej puli. W tym samouczku założono, że posiadasz wiele domen i używa przykłady *www\.contoso.com* i *www\.contoso.org*.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji
> * Dodaj regułę przekierowywania i odbiorników
> * Tworzenie maszyny wirtualnej zestawu skalowania z puli zaplecza
> * Tworzenie rekordu CNAME w domenie

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group).

W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupAG* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych 

Utwórz sieć wirtualną o nazwie *myVNet* i podsieć o nazwie *myAGSubnet* przy użyciu polecenia [az network vnet create](/cli/azure/network/vnet). Następnie można dodać podsieci o nazwie *myBackendSubnet* są one wymagane przez pulę zaplecza serwerów przy użyciu [az podsieci sieci wirtualnej Utwórz](/cli/azure/network/vnet/subnet). Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu polecenia [az network public-ip create](/cli/azure/network/public-ip).

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

Można użyć polecenia [az network application-gateway create](/cli/azure/network/application-gateway) w celu utworzenia bramy aplikacji o nazwie *myAppGateway*. Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Brama aplikacji jest przypisywana do wcześniej utworzonej podsieci *myAGSubnet* i adresu *myAGPublicIPAddress*. 

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

Tworzenie bramy aplikacji może potrwać kilka minut. Po utworzeniu bramy aplikacji możesz zauważyć następujące nowe funkcje:

- *appGatewayBackendPool* — brama aplikacji musi mieć co najmniej jedną pulę adresów zaplecza.
- *appGatewayBackendHttpSettings* — określa, że port 80 i protokół HTTP są używane do komunikacji.
- *appGatewayHttpListener* — domyślny odbiornik skojarzony z pulą *appGatewayBackendPool*.
- *appGatewayFrontendIP* — przypisuje adres *myAGPublicIPAddress* do odbiornika *appGatewayHttpListener*.
- *rule1* — domyślna reguła routingu skojarzona z odbiornikiem *appGatewayHttpListener*.


## <a name="add-listeners-and-rules"></a>Dodawanie odbiorników i reguł 

Odbiornik jest wymagany, aby brama aplikacji mogła właściwie kierować ruch do puli zaplecza. W tym samouczku utworzysz dwa odbiorniki dla swoich dwóch domen. W tym przykładzie odbiorniki są tworzone dla domen z *www\.contoso.com* i *www\.contoso.org*.

Dodaj odbiorniki zaplecza, które są wymagane do kierowania ruchu, używając polecenia [az network application-gateway http-listener create](/cli/azure/network/application-gateway).

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

### <a name="add-the-redirection-configuration"></a>Dodaj konfigurację przekierowania

Dodaj konfigurację przekierowania, który wysyła ruch sieciowy z *www\.consoto.org* do odbiornika dla *www\.contoso.com* w usłudze application gateway przy użyciu [tworzenie az sieci bramy application gateway redirect-config](/cli/azure/network/application-gateway/redirect-config).

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

### <a name="add-routing-rules"></a>Dodawanie reguł routingu

Reguły są przetwarzane w kolejności, w którym są tworzone, a ruch jest kierowany za pomocą pierwszej reguły, który jest zgodny z adresem URL wysłanych do usługi application gateway. Podstawową regułę domyślną, który został utworzony, nie jest wymagana w tym samouczku. W tym przykładzie utworzysz dwa nowe reguły o nazwie *contosoComRule* i *contosoOrgRule* i usunąć domyślnej reguły, który został utworzony.  Możesz dodać reguły za pomocą [Tworzenie reguły bramy aplikacji sieciowej az](/cli/azure/network/application-gateway).

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

## <a name="create-virtual-machine-scale-sets"></a>Tworzenie zestawów skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skalowania maszyn wirtualnych, który obsługuje domyślna pula zaplecza, który został utworzony. Nosi nazwę zestawu skalowania, którą tworzysz *myvmss* i zawiera dwa wystąpienia maszyn wirtualnych, na których możesz zainstalować rozwiązanie NGINX.

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

## <a name="create-cname-record-in-your-domain"></a>Tworzenie rekordu CNAME w domenie

Po utworzeniu bramy aplikacji z publicznym adresem IP można pobrać adres DNS i użyć go w celu utworzenia rekordu CNAME w domenie. Aby uzyskać adres DNS bramy aplikacji, możesz użyć polecenia [az network public-ip show](/cli/azure/network/public-ip). Skopiuj wartość *fqdn* ustawienia DNSSettings i użyj jej jako wartości tworzonego rekordu CNAME. Korzystanie z rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Wpisz nazwę swojej domeny na pasku adresu przeglądarki. Na przykład http://www.contoso.com.

![Testowanie witryny contoso w bramie aplikacji](./media/tutorial-internal-site-redirect-cli/application-gateway-nginxtest.png)

Na przykład zmienić adres do Twojej domeny http://www.contoso.org i należy sprawdzić, czy ruch został przekierowany do odbiornika dla www\. contoso.com.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji
> * Dodaj regułę przekierowywania i odbiorników
> * Tworzenie maszyny wirtualnej zestawu skalowania z puli zaplecza
> * Tworzenie rekordu CNAME w domenie

> [!div class="nextstepaction"]
> [Więcej informacji na temat funkcji bramy aplikacji](./application-gateway-introduction.md)
