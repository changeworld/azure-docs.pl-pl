---
title: Przekierowanie zewnętrzne za pomocą programu PowerShell
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak utworzyć bramę aplikacji, która przekierowuje ruch internetowy do witryny zewnętrznej przy użyciu programu Azure Powershell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 6596cdb2df0a916c49086f80466db60b02a81467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047752"
---
# <a name="create-an-application-gateway-with-external-redirection-using-azure-powershell"></a>Tworzenie bramy aplikacji z przekierowaniem zewnętrznym przy użyciu programu Azure PowerShell

Za pomocą programu Azure Powershell można skonfigurować [przekierowanie ruchu internetowego](multiple-site-overview.md) podczas tworzenia [bramy aplikacji](overview.md). W tym samouczku można skonfigurować odbiornik i regułę, która przekierowuje ruch sieci web, który dociera do bramy aplikacji do lokacji zewnętrznej.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie reguły odbiornika i przekierowania
> * Tworzenie bramy aplikacji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten samouczek wymaga modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów platformy Azure przy użyciu [programu New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Utwórz konfigurację podsieci *myAGSubnet* przy użyciu [new-azVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Utwórz sieć wirtualną o nazwie *myVNet* przy użyciu [sieci New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) z konfiguracją podsieci. I wreszcie, utworzyć publiczny adres IP za pomocą [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Te zasoby służą do zapewniania łączności sieciowej z bramą aplikacji i skojarzonymi z nią zasobami.

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i portu frontonu

Skojarz *myAGSubnet,* który został wcześniej utworzony z bramą aplikacji przy użyciu [new-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Przypisz publiczny adres IP do bramy aplikacji za pomocą [new-azApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig). Następnie można utworzyć port HTTP za pomocą [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>Tworzenie puli zaplecza i ustawień

Utwórz pulę wewnętrznej bazy danych o nazwie *defaultPool* dla bramy aplikacji przy użyciu [new-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Skonfiguruj ustawienia puli za pomocą [funkcji New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name defaultPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-rule"></a>Tworzenie odbiornika i reguły

Odbiornik jest wymagany, aby włączyć bramę aplikacji, aby odpowiednio kierować ruch. Utwórz odbiornik przy użyciu [new-azApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) z konfiguracją frontendu i portem frontendu, który został wcześniej utworzony. Reguła jest wymagana dla odbiornika wiedzieć, gdzie wysłać ruch przychodzący. Utwórz podstawową regułę o nazwie *redirectRule* przy użyciu [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$redirectConfig = New-AzApplicationGatewayRedirectConfiguration `
  -Name myredirect `
  -RedirectType Temporary `
  -TargetUrl "https://bing.com"
$redirectRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name redirectRule `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -RedirectConfiguration $redirectConfig
```

### <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Teraz, gdy utworzono niezbędne zasoby pomocnicze, określ parametry bramy aplikacji o nazwie *myAppGateway* za pomocą [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), a następnie utwórz ją za pomocą [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $redirectRule `
  -RedirectConfigurations $redirectConfig `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Można użyć [Get-AzPublicIPAddress,](/powershell/module/az.network/get-azpublicipaddress) aby uzyskać publiczny adres IP bramy aplikacji. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

Powinieneś *zobaczyć bing.com* pojawiają się w przeglądarce.

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie reguły odbiornika i przekierowania
> * Tworzenie bramy aplikacji
