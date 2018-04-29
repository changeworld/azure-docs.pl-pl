---
title: Tworzenie niestandardowych sondowania — brama usługi aplikacji Azure — PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć niestandardowe sondowania bramy aplikacji za pomocą programu PowerShell w Menedżerze zasobów
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: davidmu
ms.openlocfilehash: 5d17a05f964367ff12a58c3e301a1741181003fc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Tworzenie niestandardowych sondowania bramy aplikacji Azure za pomocą programu PowerShell dla usługi Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-probe-ps.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-probe-classic-ps.md)

W tym artykule możesz dodać niestandardowe sondowania istniejącą bramę aplikacji przy użyciu programu PowerShell. Niestandardowe sond są przydatne dla aplikacji, które mają strona sprawdzania kondycji określonych lub aplikacji, które nie mają pomyślnej odpowiedzi na domyślnej aplikacji sieci web.

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md).  Ten artykuł dotyczy używania modelu wdrażania usługi Resource Manager zalecanego przez firmę Microsoft w przypadku większości nowych wdrożeń zamiast [klasycznego modelu wdrażania](application-gateway-create-probe-classic-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Utwórz bramę aplikacji z niestandardowego sondy

### <a name="sign-in-and-create-resource-group"></a>Zaloguj się i utworzyć grupę zasobów

1. Użyj `Connect-AzureRmAccount` do uwierzytelniania.

  ```powershell
  Connect-AzureRmAccount
  ```

1. Pobierz subskrypcji dla konta.

  ```powershell
  Get-AzureRmSubscription
  ```

1. Wybierz subskrypcję platformy Azure do użycia.

  ```powershell
  Select-AzureRmSubscription -Subscriptionid '{subscriptionGuid}'
  ```

1. Utwórz grupę zasobów. Ten krok można pominąć, jeśli masz istniejącą grupę zasobów.

  ```powershell
  New-AzureRmResourceGroup -Name appgw-rg -Location 'West US'
  ```

Usługa Azure Resource Manager wymaga, aby wszystkie grupy zasobów określały lokalizację. Ta lokalizacja będzie używana jako domyślna lokalizacja dla zasobów w danej grupie zasobów. Upewnij się, że wszystkie polecenia, aby utworzyć bramę aplikacji używać tej samej grupie zasobów.

W powyższym przykładzie utworzono grupę zasobów o nazwie **zarządcy zasobów appgw** w lokalizacji **zachodnie stany USA**.

### <a name="create-a-virtual-network-and-a-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Poniższy przykład tworzy sieć wirtualną i podsieć bramy aplikacji. Brama aplikacji wymaga jego własnej podsieci do użycia. Z tego powodu podsieci utworzone dla bramy aplikacji powinna być krótsza od przestrzeni adresowej sieci wirtualnej, aby umożliwić innych podsieciach utworzyć i używać.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz zasób publicznego adresu IP **publicIP01** w grupie zasobów **appgw-rg** dla regionu Zachodnie stany USA. W tym przykładzie używa publicznego adresu IP dla adresu IP frontonu bramy aplikacji.  Brama aplikacji wymaga publiczny adres IP, w związku z tym mają nazwy DNS utworzony dynamicznie `-DomainNameLabel` nie można określić podczas tworzenia publicznego adresu IP.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Wszystkie elementy konfiguracji należy zdefiniować przed utworzeniem bramy aplikacji. Poniższy przykład tworzy elementy konfiguracji, które są potrzebne dla zasobu bramy aplikacji.

| **Składnik** | **Opis** |
|---|---|
| **Konfiguracja adresu IP bramy** | Konfigurację adresu IP dla bramy aplikacji.|
| **Puli wewnętrznej bazy danych** | Pula adresów IP, nazw FQDN lub kart sieciowych, które są na serwery aplikacji, których hostem aplikacji sieci web|
| **Badania kondycji** | Badanie niestandardowych służy do monitorowania kondycji członków puli wewnętrznej bazy danych|
| **Ustawienia HTTP** | Kolekcja ustawień w tym, port protokołu, na podstawie plików cookie koligacji, sondy i limit czasu.  Te ustawienia określają, w jaki sposób ruch jest kierowany do elementów członkowskich puli wewnętrznej bazy danych|
| **Port serwera sieci Web** | Brama aplikacji w nasłuchuje ruchu na port|
| **Odbiornik** | Kombinacja protokołu, konfiguracja adresu IP frontonu i port serwera sieci Web. Jest to, co nasłuchuje przychodzących żądań.
|**Reguły**| Tras ruchu do odpowiedniej wewnętrznej bazy danych na podstawie HTTP ustawień.|

```powershell
# Creates a application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Dodać badanie do istniejącej bramy aplikacji

Poniższy fragment kodu dodaje badanie istniejącą bramę aplikacji.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzureRmApplicationGateway.
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Usuń sondę z istniejącą bramę aplikacji

Poniższy fragment kodu usuwa sondę z istniejącą bramę aplikacji.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzureRmApplicationGateway.
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Pobieranie nazwy DNS bramy aplikacji

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontonu na potrzeby komunikacji. Gdy jest używany publiczny adres IP, brama aplikacji wymaga dynamicznie przypisywanej nazwy DNS, która nie jest przyjazna. Aby upewnić się, że użytkownicy końcowi mogą trafić bramę aplikacji, można użyć rekordu CNAME w celu wskazania publicznego punktu końcowego bramy aplikacji. [Konfigurowanie niestandardowej nazwy domeny dla platformy Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Aby to zrobić, pobierz szczegóły bramy aplikacji i skojarzony adres IP oraz nazwę DNS, używając elementu PublicIPAddress dołączonego do bramy aplikacji. Nazwa DNS bramy aplikacji powinna zostać użyta w celu utworzenia rekordu CNAME, który wskazuje dwóm aplikacjom sieci Web tę nazwę DNS. Korzystanie z rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak skonfigurować odciążanie protokołu SSL, przechodząc: [skonfigurować odciążanie protokołu SSL](application-gateway-ssl-arm.md)

