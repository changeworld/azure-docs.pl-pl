---
title: Utwórz bramę aplikacji z zapory aplikacji sieci web - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji za pomocą zapory aplikacji sieci web przy użyciu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 87125b68c81af07d0ecd9693fdf7e2dc00a93324
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-cli"></a>Utwórz bramę aplikacji za pomocą zapory aplikacji sieci web przy użyciu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia Azure umożliwia tworzenie [brama aplikacji w](application-gateway-introduction.md) z [zapory aplikacji sieci web](application-gateway-web-application-firewall-overview.md) (WAF), która używa [zestaw skali maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Używa zapory aplikacji sieci Web [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) zasady ochrony aplikacji. Reguły obejmują ochronę przed ataki, takie jak iniekcja kodu SQL, atakami skryptów między witrynami i hijacks sesji. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji z zapory aplikacji sieci Web jest włączona
> * Tworzenie zestawu skalowania maszyn wirtualnych
> * Utwórz konto magazynu i skonfigurować diagnostykę

![Przykład zapory aplikacji sieci Web](./media/application-gateway-web-application-firewall-cli/scenario-waf.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Tworzenie grupy zasobów platformy Azure o nazwie *myResourceGroupAG* z [Tworzenie grupy az](/cli/azure/group#az_group_create).

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Sieci wirtualnej i podsieci są używane do zapewnienia możliwości połączenia sieci z bramy aplikacji i jej skojarzonych zasobów. Utwórz sieć wirtualną o nazwie *myVNet* i podsieć o nazwie *myAGSubnet* z [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_create) i [az podsieci sieci wirtualnej Utwórz](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* z [utworzyć az sieci publicznej ip](/cli/azure/network/public-ip#az_network_public_ip_create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway-with-a-waf"></a>Utwórz bramę aplikacji z zapory aplikacji sieci Web

Można użyć [utworzyć az sieci z bramy aplikacji](/cli/azure/application-gateway#az_application_gateway_create) Utwórz bramę aplikacji o nazwie *myAppGateway*. Podczas tworzenia bramy aplikacji przy użyciu wiersza polecenia platformy Azure, należy określić informacje o konfiguracji, takie jak pojemności, jednostki sku i ustawienia protokołu HTTP. Brama aplikacji jest przypisany do *myAGSubnet* i *myPublicIPSddress* wcześniej utworzony.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

Może upłynąć kilka minut dla bramy aplikacji ma zostać utworzony. Po utworzeniu bramy aplikacji, można wyświetlić te nowe funkcje:

- *appGatewayBackendPool* -bramę aplikacji musi mieć co najmniej jedna pula adresów zaplecza.
- *appGatewayBackendHttpSettings* — Określa, że portu 80 oraz protokołu HTTP jest używany do komunikacji.
- *appGatewayHttpListener* -odbiornika domyślne skojarzone z *appGatewayBackendPool*.
- *appGatewayFrontendIP* -przypisuje *myAGPublicIPAddress* do *appGatewayHttpListener*.
- *rule1* — domyślna routingu regułę, która jest skojarzona z *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skali maszyny wirtualnej, która zawiera dwa serwery w puli zaplecza bramy aplikacji. W zestawie skalowania maszyn wirtualnych są skojarzone z *myBackendSubnet* podsieci. Aby utworzyć skali zestaw, możesz użyć [az vmss utworzyć](/cli/azure/vmss#az_vmss_create).

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/vhorne/samplescripts/master/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Utwórz konto magazynu i skonfigurować diagnostykę

W tym samouczku aplikacji przy użyciu brama konta magazynu do przechowywania danych na potrzeby wykrywania i zapobiegania. Analiza dzienników lub Centrum zdarzeń można użyć również do rejestrowania danych. 

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu o nazwie *myagstore1* z [Tworzenie konta magazynu az](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create).

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption blob
```

### <a name="configure-diagnostics"></a>Skonfiguruj diagnostyki

Skonfiguruj diagnostykę, aby zapisać dane w dzienniku ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog i ApplicationGatewayFirewallLog. SUBSTITUTE `<subscriptionId>` z identyfikatorem subskrypcji, a następnie skonfiguruj Diagnostyka z [utworzyć ustawienia diagnostyki az monitora](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_create).

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)
storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)
az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, należy użyć [az sieci ip publicznego Pokaż](/cli/azure/network/public-ip#az_network_public_ip_show). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Podstawowy adres URL testu bramy aplikacji](./media/application-gateway-web-application-firewall-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji z zapory aplikacji sieci Web jest włączona
> * Tworzenie zestawu skalowania maszyn wirtualnych
> * Utwórz konto magazynu i skonfigurować diagnostykę

Aby dowiedzieć się więcej na temat bram aplikacji i ich skojarzonych zasobów, nadal artykuły.