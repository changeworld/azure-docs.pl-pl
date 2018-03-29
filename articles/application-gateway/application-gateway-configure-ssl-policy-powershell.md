---
title: Konfigurowanie zasad protokołu SSL dla bramy aplikacji Azure — PowerShell
description: Ta strona zawiera instrukcje dotyczące konfigurowania zasad protokołu SSL dla bramy aplikacji Azure
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/27/2018
ms.author: victorh
ms.openlocfilehash: 4c9ca5cee14603fb39115defc574aa7e956886ba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="configure-ssl-policy-versions-and-cipher-suites-on-application-gateway"></a>Konfigurowanie zasad zabezpieczeń SSL w wersjach i mechanizmy w bramie aplikacji szyfrowania

Informacje o sposobie konfigurowania zasad zabezpieczeń SSL w wersjach i mechanizmy w bramie aplikacji szyfrowania. Możesz wybrać z [listy wstępnie zdefiniowanych zasad](#predefined-ssl-policies) czy zawierają różne konfiguracje wersji zasad protokołu SSL i włączone mechanizmów szyfrowania. Istnieje również możliwość definiowania [niestandardowe zasady SSL](#configure-a-custom-ssl-policy) zgodnie z wymaganiami.

## <a name="get-available-ssl-options"></a>Dostępne opcje protokołu SSL

`Get-AzureRMApplicationGatewayAvailableSslOptions` Polecenie cmdlet udostępnia listę dostępnych wstępnie zdefiniowane zasady, mechanizmów szyfrowania dostępne i wersji protokołu, które można skonfigurować. Poniższy przykład przedstawia przykładowe dane wyjściowe z polecenia cmdlet.

```
DefaultPolicy: AppGwSslPolicy20150501
PredefinedPolicies:
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20150501
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401S

AvailableCipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
    TLS_RSA_WITH_AES_128_GCM_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA256
    TLS_RSA_WITH_AES_128_CBC_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA
    TLS_RSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_3DES_EDE_CBC_SHA
    TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

AvailableProtocols:
    TLSv1_0
    TLSv1_1
    TLSv1_2
```

## <a name="list-pre-defined-ssl-policies"></a>Lista wstępnie zdefiniowane zasady SSL

Brama aplikacji w zawiera trzy wstępnie zdefiniowane zasady, które mogą być używane. `Get-AzureRmApplicationGatewaySslPredefinedPolicy` Polecenie cmdlet pobiera tych zasad. Wszystkie zasady mają różnych protokołów i mechanizmów szyfrowania włączone. Te wstępnie zdefiniowane zasady można szybko skonfigurować zasadę SSL na bramie aplikacji. Domyślnie **AppGwSslPolicy20150501** jest zaznaczone, jeśli nie określonych SSL zdefiniowano zasad.

Następujące dane wyjściowe jest przykładem uruchomiona `Get-AzureRmApplicationGatewaySslPredefinedPolicy`.

```
Name: AppGwSslPolicy20150501
MinProtocolVersion: TLSv1_0
CipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
 ...
Name: AppGwSslPolicy20170401
MinProtocolVersion: TLSv1_1
CipherSuites:
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
...
```

## <a name="configure-a-custom-ssl-policy"></a>Skonfiguruj niestandardowe zasady SSL

W przypadku konfigurowania niestandardowych zasad protokołu SSL, Przekaż następujące parametry: PolicyType, MinProtocolVersion CipherSuite i bramy aplikacji. Przy próbie przekazania innych parametrów, wystąpi błąd podczas tworzenia lub aktualizowania bramy aplikacji. 

Poniższy przykład przedstawia zasady niestandardowe SSL na bramy aplikacji. Ustawia wersję protokołu minimalna `TLSv1_1` i umożliwia następujące mechanizmy szyfrowania:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

> [!IMPORTANT]
> Mechanizmy szyfrowania co najmniej jeden z poniższej listy należy wybrać podczas konfigurowania niestandardowych zasad SSL. Brama aplikacji w używa mechanizmów szyfrowania RSA SHA256 zarządzania wewnętrznej bazy danych.
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
> * TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_RSA_WITH_AES_128_GCM_SHA256
> * TLS_RSA_WITH_AES_256_CBC_SHA256
> * TLS_RSA_WITH_AES_128_CBC_SHA256

```powershell
# get an application gateway resource
$gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroup AdatumAppGatewayRG

# set the SSL policy on the application gateway
Set-AzureRmApplicationGatewaySslPolicy -ApplicationGateway $gw -PolicyType Custom -MinProtocolVersion TLSv1_1 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"

# validate the SSL policy locally
Get-AzureRmApplicationGatewaySslPolicy -ApplicationGateway $gw

# update the gateway with validated SSL policy
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

## <a name="create-an-application-gateway-with-a-pre-defined-ssl-policy"></a>Utwórz bramę aplikacji za pomocą wstępnie zdefiniowanych zasad protokołu SSL

Podczas konfigurowania zasad wstępnie zdefiniowane SSL, Przekaż następujące parametry: PolicyType Nazwa_zasady i bramy aplikacji. Przy próbie przekazania innych parametrów, wystąpi błąd podczas tworzenia lub aktualizowania bramy aplikacji.

Poniższy przykład tworzy nową bramę aplikacji za pomocą wstępnie zdefiniowanych zasad SSL.

```powershell
# Create a resource group
$rg = New-AzureRmResourceGroup -Name ContosoRG -Location "East US"

# Create a subnet for the application gateway
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network with a 10.0.0.0/16 address space
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName $rg.ResourceGroupName -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the subnet object for later use
$subnet = $vnet.Subnets[0]

# Create a public IP address
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -name publicIP01 -location "East US" -AllocationMethod Dynamic

# Create a ip configuration object
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Create a backend pool for backend web servers
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

# Define the backend http settings to be used.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

# Create a new port for SSL
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

# Upload an existing pfx certificate for SSL offload
$password = ConvertTo-SecureString -String "P@ssw0rd" -AsPlainText -Force
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile C:\folder\contoso.pfx -Password $password

# Create a frontend IP configuration for the public IP address
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Create a new listener with the certificate, port, and frontend ip.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a new rule for backend traffic routing
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Define the size of the application gateway
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Configure the SSL policy to use a different pre-defined policy
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Create the application gateway.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName $rg.ResourceGroupName -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

## <a name="update-an-existing-application-gateway-with-a-pre-defined-ssl-policy"></a>Zaktualizuj istniejącą bramę aplikacji za pomocą wstępnie zdefiniowanych zasad protokołu SSL

Aby skonfigurować niestandardowe zasady protokołu SSL, należy przekazać następujące parametry: **PolicyType**, **MinProtocolVersion**, **CipherSuite**, i **bramyaplikacji**. Aby skonfigurować zasady wstępnie zdefiniowane SSL, należy skonfigurować następujące ustawienia: **PolicyType**, **PolicyName**, i **bramy aplikacji**. Przy próbie przekazania innych parametrów, wystąpi błąd podczas tworzenia lub aktualizowania bramy aplikacji.

W poniższym przykładzie są przykłady kodu dla zasady niestandardowe i wstępnie zdefiniowanych zasad. Usuń znaczniki komentarza zasadę, której chcesz użyć.

```powershell
# You have to change these parameters to match your environment.
$AppGWname = "YourAppGwName"
$RG = "YourResourceGroupName"

$AppGw = get-azurermapplicationgateway -Name $AppGWname -ResourceGroupName $RG

# Choose either custom policy or prefedined policy and uncomment the one you want to use.

# SSL Custom Policy
# Set-AzureRmApplicationGatewaySslPolicy -PolicyType Custom -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_RSA_WITH_AES_128_CBC_SHA256" -ApplicationGateway $AppGw

# SSL Predefined Policy
# Set-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S" -ApplicationGateway $AppGW

# Update AppGW
# The SSL policy options are not validated or updated on the Application Gateway until this cmdlet is executed.
$SetGW = Set-AzureRmApplicationGateway -ApplicationGateway $AppGW
```

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę [omówienie przekierowania aplikacji bramy](application-gateway-redirect-overview.md) więcej informacji na temat do przekierowywania ruchu HTTP na punkt końcowy HTTPS.
