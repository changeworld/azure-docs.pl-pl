---
title: Utwórz bramę aplikacji z zewnętrznego ruchu przekierowania - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: a0132fa43f28997d80e391b7445eeb1809c19167
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Utwórz bramę aplikacji z zewnętrznego przekierowania przy użyciu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwiają skonfigurowanie [przekierowania ruchu w sieci web](multiple-site-overview.md) podczas tworzenia [brama aplikacji w](overview.md). W tym samouczku skonfigurujesz regułę, która przekierowuje ruch w sieci web, która pojawi się na bramie aplikacji do zewnętrznej witryny i odbiornika.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Utwórz regułę odbiornika i Przekierowanie
> * Tworzenie bramy aplikacji

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

Utwórz sieć wirtualną o nazwie *myVNet* i podsieć o nazwie *myAGSubnet* przy użyciu [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_net). Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu [utworzyć az sieci publicznej ip](/cli/azure/public-ip#az_network_public_ip_create). Te zasoby są używane do zapewnienia możliwości połączenia sieci z bramy aplikacji i jej skojarzonych zasobów.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Można użyć [utworzyć az sieci z bramy aplikacji](/cli/azure/application-gateway#create) Utwórz bramę aplikacji o nazwie *myAppGateway*. Podczas tworzenia bramy aplikacji przy użyciu wiersza polecenia platformy Azure, należy określić informacje o konfiguracji, takie jak pojemności, jednostki sku i ustawienia protokołu HTTP. Brama aplikacji jest przypisany do *myAGSubnet* i *myPublicIPSddress* wcześniej utworzony. 

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
  --frontend-port 8080 \
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

### <a name="add-the-redirection-configuration"></a>Dodawanie konfiguracji przekierowania

Dodawanie konfiguracji przekierowania, który wysyła ruch z *www.consoto.org* odbiornika dla *www.contoso.com* do bramy aplikacji przy użyciu [az sieci bramy aplikacji Tworzenie konfiguracji przekierowania](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "http://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Dodaj regułę routingu i odbiornika

Odbiornik wymaganego do włączenia usługi Brama aplikacji w celu odpowiednio kierować ruchem. Utworzenie obiektu nasłuchującego przy użyciu [utworzyć az sieci bramy aplikacji odbiornik http](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create) portów frontonu utworzone za pomocą [tworzenie frontonu — port az brama aplikacji w sieci](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). Reguła jest wymagana dla odbiornika wiedzieć, gdzie na wysyłanie ruchu przychodzącego. Utwórz podstawowe reguły o nazwie *redirectRule* przy użyciu [Tworzenie reguły brama aplikacji w sieci az](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, można użyć [az sieci ip publicznego Pokaż](/cli/azure/network/public-ip#az_network_public_ip_show). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

Powinny pojawić się *bing.com* są wyświetlane w przeglądarce.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> * Konfigurowanie sieci
> * Utwórz regułę odbiornika i Przekierowanie
> * Tworzenie bramy aplikacji