---
title: Konfigurowanie niestandardowych reguł zapory aplikacji sieci Web v2 przy użyciu Azure PowerShell
description: Informacje o konfigurowaniu niestandardowych reguł zapory aplikacji sieci Web w wersji 2 przy użyciu Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 9e50b47e22f5760c213cd0cafad82ecca592dec8
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263746"
---
# <a name="configure-web-application-firewall-v2-custom-rules-by-using-azure-powershell"></a>Konfigurowanie niestandardowych reguł zapory aplikacji sieci Web v2 przy użyciu Azure PowerShell

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Za pomocą reguł niestandardowych można utworzyć własne reguły, które są oceniane dla każdego żądania, które przechodzą przez zaporę aplikacji sieci Web (WAF). Reguły te mają wyższy priorytet niż pozostałe reguły w zarządzanych zestawach reguł. Aby zezwolić na pełne dostosowanie, reguły niestandardowe mają akcję (do zezwalania lub blokowania), warunek dopasowania i operatora.

W tym artykule opisano tworzenie Application Gateway platformy Azure w wersji 2, która używa reguły niestandardowej. Reguła niestandardowa blokuje ruch, jeśli nagłówek żądania zawiera *Evilbot*User-Agent.

Aby wyświetlić więcej przykładów niestandardowych reguł, zobacz [Tworzenie i używanie niestandardowych reguł zapory aplikacji sieci Web](create-custom-waf-rules.md).

Aby uruchomić kod Azure PowerShell w tym artykule w jednym ciągłym skrypcie, który można skopiować, wkleić i uruchomić, zobacz [przykłady dla programu PowerShell w usłudze Azure Application Gateway](powershell-samples.md).

## <a name="prerequisites"></a>Wymagania wstępne
* [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Potrzebny jest moduł Azure PowerShell. Jeśli zdecydujesz się zainstalować program Azure PowerShell i używać go lokalnie, ten skrypt wymaga Azure PowerShell module w wersji 2.1.0 lub nowszej. Wykonaj następujące czynności:

  1. Aby znaleźć wersję, uruchom `Get-Module -ListAvailable Az`. Jeśli musisz przeprowadzić uaktualnienie, zobacz [Install Azure PowerShell module](/powershell/azure/install-az-ps).
  2. Aby utworzyć połączenie z platformą Azure, uruchom `Connect-AzAccount`.

## <a name="example-script"></a>Przykładowy skrypt

### <a name="set-up-variables"></a>Konfigurowanie zmiennych

Uruchom następujący kod:

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Uruchom następujący kod:

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Uruchom następujący kod:

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Tworzenie statycznego publicznego adresu VIP

Uruchom następujący kod:

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-a-pool-and-front-end-port"></a>Tworzenie puli i portu frontonu

Uruchom następujący kod:

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Tworzenie odbiornika, ustawienia protokołu HTTP, reguły i automatycznego skalowania

Uruchom następujący kod:

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>Utwórz regułę niestandardową i Zastosuj ją do zasad WAF

Uruchom następujący kod:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Uruchom następujący kod:

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
   -Location $location -BackendAddressPools $pool `
   -BackendHttpSettingsCollection  $poolSetting01 `
   -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
   -FrontendPorts $fp01 -HttpListeners $listener01 `
   -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
   -WebApplicationFirewallConfig $wafConfig `
   -FirewallPolicy $wafPolicy
```

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o zaporze aplikacji sieci Web](waf-overview.md)
