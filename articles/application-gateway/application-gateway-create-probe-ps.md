---
title: Tworzenie niestandardowej sondy przy użyciu programu PowerShell
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak utworzyć niestandardową sondę bramy aplikacji przy użyciu programu PowerShell w Menedżerze zasobów
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: f720a94d3467ce15ea5d58a8ece6de2a669f6258
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312586"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Tworzenie niestandardowej sondy dla bramy aplikacji platformy Azure przy użyciu programu PowerShell for Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-probe-ps.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-probe-classic-ps.md)

W tym artykule należy dodać niestandardową sondę do istniejącej bramy aplikacji z programem PowerShell. Sondy niestandardowe są przydatne dla aplikacji, które mają określoną stronę sprawdzania kondycji lub dla aplikacji, które nie zapewniają pomyślnej odpowiedzi w domyślnej aplikacji sieci web.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Tworzenie bramy aplikacji za pomocą sondy niestandardowej

### <a name="sign-in-and-create-resource-group"></a>Zaloguj się i utwórz grupę zasobów

1. Służy `Connect-AzAccount` do uwierzytelniania.

   ```powershell
   Connect-AzAccount
   ```

1. Pobierz subskrypcje dla konta.

   ```powershell
   Get-AzSubscription
   ```

1. Wybierz subskrypcję platformy Azure do użycia.

   ```powershell
   Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
   ```

1. Utwórz grupę zasobów. Ten krok można pominąć, jeśli masz istniejącą grupę zasobów.

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location 'West US'
   ```

Usługa Azure Resource Manager wymaga, żeby wszystkie grupy zasobów miały lokalizację. Ta lokalizacja będzie używana jako domyślna lokalizacja dla zasobów w danej grupie zasobów. Upewnij się, że wszystkie polecenia do utworzenia bramy aplikacji używają tej samej grupy zasobów.

W poprzednim przykładzie utworzyliśmy grupę zasobów o nazwie **appgw-RG** w lokalizacji **Zachodnie stany USA**.

### <a name="create-a-virtual-network-and-a-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Poniższy przykład tworzy sieć wirtualną i podsieć bramy aplikacji. Brama aplikacji wymaga własnej podsieci do użytku. Z tego powodu podsieć utworzona dla bramy aplikacji powinna być mniejsza niż przestrzeń adresowa sieci wirtualnej, aby umożliwić tworzenie i używane inne podsieci.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz publiczny zasób IP **publicIP01** w **appgw-rg** grupy zasobów dla regionu Zachodnie stany USA. W tym przykładzie użyto publicznego adresu IP dla adresu IP front-end bramy aplikacji.  Brama aplikacji wymaga publicznego adresu IP, aby mieć `-DomainNameLabel` dynamicznie utworzoną nazwę DNS, dlatego nie można określić podczas tworzenia publicznego adresu IP.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Przed utworzeniem bramy aplikacji należy skonfigurować wszystkie elementy konfiguracji. Poniższy przykład tworzy elementy konfiguracji, które są potrzebne dla zasobu bramy aplikacji.

| **Składnik** | **Opis** |
|---|---|
| **Konfiguracja adresu IP bramy** | Konfiguracja IP bramy aplikacji.|
| **Pula zaplecza** | Pula adresów IP, nazw FQDN lub kart sieciowych, które są do serwerów aplikacji obsługujących aplikację sieci web|
| **Sonda kondycji** | Niestandardowa sonda używana do monitorowania kondycji elementów członkowskich puli wewnętrznej bazy danych|
| **Ustawienia HTTP** | Kolekcja ustawień, w tym, port, protokół, koligacja oparta na plikach cookie, sondy i limit czasu.  Te ustawienia określają sposób kierowania ruchu do członków puli zaplecza|
| **Port frontendu** | Port, który brama aplikacji nasłuchuje ruchu na|
| **Odbiornika** | Połączenie protokołu, konfiguracji IP frontu i portu frontendu. To jest to, co nasłuchuje przychodzących żądań.
|**Reguła**| Kieruje ruch do odpowiedniego zaplecza na podstawie ustawień HTTP.|

```powershell
# Creates an application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Dodawanie sondy do istniejącej bramy aplikacji

Poniższy fragment kodu dodaje sondę do istniejącej bramy aplikacji.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Usuwanie sondy z istniejącej bramy aplikacji

Poniższy fragment kodu usuwa sondę z istniejącej bramy aplikacji.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Pobieranie nazwy DNS bramy aplikacji

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontonu na potrzeby komunikacji. Gdy jest używany publiczny adres IP, brama aplikacji wymaga dynamicznie przypisywanej nazwy DNS, która nie jest przyjazna. Aby upewnić się, że użytkownicy końcowi mogą trafić bramę aplikacji, można użyć rekordu CNAME w celu wskazania publicznego punktu końcowego bramy aplikacji. [Konfigurowanie niestandardowej nazwy domeny dla platformy Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Aby to zrobić, pobierz szczegóły bramy aplikacji i skojarzony adres IP oraz nazwę DNS, używając elementu PublicIPAddress dołączonego do bramy aplikacji. Nazwa DNS bramy aplikacji powinna zostać użyta w celu utworzenia rekordu CNAME, który wskazuje dwóm aplikacjom internetowym tę nazwę DNS. Korzystanie z rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
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

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować odciążanie TLS przez odwiedzając: [Konfigurowanie odciążania TLS](application-gateway-ssl-arm.md)

