---
title: Jak używać usługi Azure API Management w sieci wirtualnej z usługą Application Gateway | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować usługi Azure API Management w wewnętrznej sieci wirtualnej za pomocą aplikacji bramy (WAF) jako serwera sieci Web
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: sasolank
ms.openlocfilehash: 4ee970f14a6da3d65849a79ff4afae68601f106f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66141669"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integracja usługi API Management w wewnętrznej sieci Wirtualnej z usługą Application Gateway

## <a name="overview"> </a> Przegląd

Usługa API Management można skonfigurować w sieci wirtualnej w trybie wewnętrznego, dzięki czemu dostępny tylko w obrębie sieci wirtualnej. Usługa Azure Application Gateway to usługa PAAS, co zapewnia modułu równoważenia obciążenia warstwy 7. Ona działa jako usługa zwrotnego serwera proxy i zapewnia między oferty zapory aplikacji sieci Web (WAF).

Łączenie usługi API Management aprowizowane w wewnętrznej sieci Wirtualnej za pomocą frontonu bramy Application Gateway umożliwia następujące scenariusze:

* Użyj tego samego zasobu usługi API Management do użycia przez klientów wewnętrznych i zewnętrznych klientów.
* Użyj pojedynczego zasobu usługi API Management i podzestawu interfejsów API zdefiniowane w usłudze API Management jest dostępna dla klientów zewnętrznych.
* Zapewnia sposób setką kompleksowych włączenie i wyłączenie z publicznej sieci Internet. dostęp do usługi API Management.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby wykonać kroki opisane w tym artykule, musisz mieć:

* Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certyfikaty — plik pfx i podaj cer dla nazwy hosta interfejsu API i pfx dla nazwy hosta portalu dla deweloperów.

## <a name="scenario"> </a> Scenariusz

W tym artykule opisano, jak używać jednej usługi API Management dla wewnętrznych i zewnętrznych klientów i przypisz ją pełnić rolę pojedynczego frontonu dla obu lokalnie i w chmurze interfejsów API. Zobaczysz również jak udostępniać tylko podzbioru interfejsów API (w tym przykładzie, które zostaną one wyróżnione w kolorze zielonym) do użytku zewnętrznego za pomocą funkcji routingu, które są dostępne w usłudze Application Gateway.

W pierwszym przykładzie konfiguracji wszystkie interfejsy API są zarządzane tylko z w ramach sieci wirtualnej. Wewnętrzny konsumentów (wyróżnione w kolorze pomarańczowym) mają dostęp do wszystkich wewnętrznych i zewnętrznych interfejsów API. Ruch nigdy nie trafia do sieci internet. Łączność o wysokiej wydajności są dostarczane za pośrednictwem obwodów Expressroute.

![trasy adresu URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Przed rozpoczęciem

* Upewnij się, że używasz najnowszej wersji programu Azure PowerShell. Zapoznaj się z instrukcjami instalacji na [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Co to jest wymagane do utworzenia integrację usługi API Management i Application Gateway?

* **Pula serwerów zaplecza:** Jest to wewnętrzny wirtualny adres IP usługi API Management.
* **Ustawienia puli serwerów zaplecza:** każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są stosowane do wszystkich serwerów w puli.
* **Port frontonu:** Jest to port publiczny, który jest otwierany w bramie aplikacji. Ruch osiągnięcia go jest przekierowywany do jednego z serwerów zaplecza.
* **Odbiornik:** odbiornik ma port frontonu, protokół (Http lub Https, z uwzględnieniem wielkości liter) oraz nazwę certyfikatu SSL (w przypadku konfigurowania odciążania protokołu SSL).
* **Reguła:** Reguła wiąże odbiornik z pulą serwerów zaplecza.
* **Sonda kondycji niestandardowe:** Application Gateway, domyślnie używa sondy na podstawie adresów IP do zorientować się w BackendAddressPool serwerów, które są aktywne. API Management, którego usługa będzie odpowiadać tylko na żądania z nagłówkiem właściwy host, dlatego sondy domyślne zakończyć się niepowodzeniem. Sonda kondycji niestandardowy musi można zdefiniować, aby ułatwić usługa application gateway, sprawdzić, czy usługa jest aktywny i należy go przekazywania żądań.
* **Certyfikaty domeny niestandardowej:** Dostęp do usługi API Management z Internetu, należy utworzyć mapowanie rekordu CNAME jego nazwy hosta na nazwę DNS frontonu bramy aplikacji. To zapewnia, że nagłówek nazwy hosta i certyfikatu wysłanego do usługi Application Gateway, który jest przekazywany do usługi API Management jest jedna APIM rozpoznać jako prawidłowy. W tym przykładzie użyjemy dwóch certyfikatów — dla wewnętrznej bazy danych i portalu dla deweloperów.  

## <a name="overview-steps"> </a> Kroki wymagane do integrowania usługi API Management i usługa Application Gateway

1. Utworzenie grupy zasobów dla usługi Resource Manager.
2. Tworzenie sieci wirtualnej, podsieci i publicznego adresu IP dla bramy Application Gateway. Utwórz inną podsieć dla usługi API Management.
3. Tworzenie usługi API Management w podsieci sieci Wirtualnej utworzonej powyżej i upewnij się, że korzystania z trybu wewnętrznego.
4. Konfigurowanie niestandardowej nazwy domeny w usłudze API Management.
5. Tworzenie obiektu konfiguracji bramy aplikacji.
6. Utwórz zasób usługi Application Gateway.
7. Utwórz rekord CNAME z publicznej nazwy DNS bramy aplikacji do nazwy hosta serwera proxy usługi API Management.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Udostępnianie portalu dla deweloperów zewnętrznie za pośrednictwem bramy aplikacji

W tym przewodniku możemy także udostępni **portalu dla deweloperów** do zewnętrznego liczby odbiorców za pośrednictwem bramy aplikacji. Wymaga dodatkowych czynności w celu tworzenia odbiornika portalu dla deweloperów, sondy, ustawienia i zasady. Wszystkie szczegółowe informacje znajdują się w odpowiednich kroków.

> [!WARNING]
> Jeśli używasz usługi Azure AD lub uwierzytelniania innych firm, Włącz [koligacji sesji na podstawie pliku cookie](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity) funkcji w usłudze Application Gateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Tworzenie grupy zasobów dla usługi Resource Manager

### <a name="step-1"></a>Krok 1

Zaloguj się do platformy Azure.

```powershell
Connect-AzAccount
```

Uwierzytelnianie przy użyciu swoich poświadczeń.

### <a name="step-2"></a>Krok 2

Wybierz odpowiednią subskrypcję.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Krok 3

Utwórz grupę zasobów (ten krok można pominąć, jeśli używasz istniejącej grupy zasobów).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Usługa Azure Resource Manager wymaga, aby wszystkie grupy zasobów określały lokalizację. Będzie ona używana jako domyślna lokalizacja dla zasobów w danej grupie zasobów. Upewnij się, że wszystkich poleceniach służących do tworzenia bramy aplikacji użyj tej samej grupie zasobów.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Utwórz sieć wirtualną i podsieć dla bramy application gateway

Poniższy przykład pokazuje, jak utworzyć sieć wirtualną przy użyciu zasobu manager.

### <a name="step-1"></a>Krok 1

Przypisz zakres adresów 10.0.0.0/24 do zmiennej podsieci do użycia podczas tworzenia sieci wirtualnej dla usługi Application Gateway.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Krok 2

Przypisz 10.0.1.0/24 zakresu adresów do zmiennej podsieci do użycia podczas tworzenia sieci wirtualnej dla usługi API Management.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Krok 3

Tworzenie sieci wirtualnej o nazwie **appgwvnet** w grupie zasobów **apim-appGw-RG** dla regionu zachodnie stany USA. Użyj prefiksu 10.0.0.0/16 z podsieci 10.0.0.0/24 i 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Krok 4

Przypisz zmienną podsieci dla następnych kroków

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Tworzenie usługi API Management w sieci Wirtualnej w trybie wewnętrznego

Poniższy przykład pokazuje sposób tworzenia usługi API Management w sieci Wirtualnej skonfigurowana dla tylko wewnętrzne dostęp.

### <a name="step-1"></a>Krok 1

Utwórz obiekt sieci wirtualnej usługi API Management przy użyciu podsieci $apimsubnetdata utworzonego powyżej.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Krok 2

Tworzenie usługi API Management w sieci wirtualnej.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Po pomyślnym powyższego polecenia dotyczą [konfiguracji DNS wymagane do uzyskania dostępu usługi wewnętrznej sieci Wirtualnej usługa API Management](api-management-using-with-internal-vnet.md#apim-dns-configuration) do niego dostęp. W tym kroku może potrwać ponad pół godziny.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Konfigurowanie niestandardowej nazwy domeny w usłudze API Management

### <a name="step-1"></a>Krok 1

Zainicjuj poniższe zmienne z danymi szczegółowymi dotyczącymi certyfikatów przy użyciu kluczy prywatnych dla domen. W tym przykładzie użyjemy `api.contoso.net` i `portal.contoso.net`.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Krok 2

Utwórz i ustaw nazwę hosta obiektów konfiguracji serwera proxy i dla portalu.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType Portal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz zasób publicznego adresu IP **publicIP01** w grupie zasobów.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Adres IP jest przypisywany do bramy aplikacji w chwili uruchamiania usługi.

## <a name="create-application-gateway-configuration"></a>Utwórz konfigurację bramy aplikacji

Wszystkie elementy konfiguracji muszą zostać skonfigurowane przed utworzeniem bramy aplikacji. Poniższe kroki umożliwiają utworzenie elementów konfiguracji wymaganych w przypadku zasobu bramy aplikacji.

### <a name="step-1"></a>Krok 1

Utwórz konfigurację adresu IP bramy aplikacji o nazwie **gatewayIP01**. Uruchomiona usługa Application Gateway wybierze adres IP ze skonfigurowanej podsieci i skieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Krok 2

Skonfiguruj port adresu IP frontonu dla punktu końcowego publicznego adresu IP. Ten port jest numer portu, którego użytkownicy końcowi nawiązywać połączenie.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Krok 3

Skonfiguruj adres IP frontonu z punktem końcowym publicznego adresu IP.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Krok 4

Konfigurowanie certyfikatów dla usługi Application Gateway, który będzie używany do odszyfrowania i ponownie zaszyfrować ruch przechodzi przez.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Krok 5

Utwórz odbiorniki HTTP bramy Application Gateway. Przypisać adresu IP frontonu certyfikatów konfiguracji, portu i protokołu ssl do nich.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Krok 6

Tworzenie niestandardowej sondy do usługi API Management `ContosoApi` punktu końcowego domeny serwera proxy. Ścieżka `/status-0123456789abcdef` jest punkt końcowy kondycji na domyślne hostowanych na wszystkie usługi API Management. Ustaw `api.contoso.net` jako nazwę hosta niestandardowej sondy, aby zabezpieczyć go za pomocą certyfikatu SSL.

> [!NOTE]
> Nazwa hosta `contosoapi.azure-api.net` jest domyślną nazwę hosta serwera proxy, skonfigurować usługi o nazwie `contosoapi` jest tworzony w publicznej Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Krok 7

Przekaż certyfikat do użycia na zasobów w puli zaplecza z włączonym protokołem SSL. Jest to ten sam certyfikat, który podana w kroku 4 powyżej.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Krok 8

Skonfiguruj ustawienia zaplecza HTTP bramy Application Gateway. Dotyczy to również ustawienie limitu czasu dla żądania wewnętrznej bazy danych, po upływie którego jest anulowane. Ta wartość jest inna niż limit czasu sondy.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Krok 9

Skonfiguruj pulę adresów IP zaplecza o nazwie **apimbackend** za pomocą wewnętrznego wirtualnego adresu IP adres usługi API Management utworzonego powyżej.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Krok 10

Tworzenie reguł w usłudze Application Gateway użyć podstawowa routingu.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Zmień typ RuleType — i routingu, aby ograniczyć dostęp do niektórych stron portalu dla deweloperów.

### <a name="step-11"></a>Krok 11

Skonfiguruj liczbę wystąpień i rozmiar bramy aplikacji. W tym przykładzie używamy [jednostki SKU zapory aplikacji sieci Web](../application-gateway/application-gateway-webapplicationfirewall-overview.md) Aby zwiększyć bezpieczeństwo zasobu usługi API Management.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Krok 12

Konfigurowanie zapory aplikacji sieci Web, aby być w trybie "Zapobieganie".

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Create Application Gateway

Tworzenie bramy aplikacji przy użyciu obiektów konfiguracji z poprzednich kroków.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME nazwę hosta serwera proxy usługi API Management do publicznej nazwy DNS zasobu usługi Application Gateway

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontonu na potrzeby komunikacji. Korzystając z publicznym adresem IP, brama aplikacji wymaga dynamicznie przypisywanej nazwy DNS, które nie mogą być łatwe w użyciu.

Aby utworzyć rekord CNAME, który wskazuje nazwę hosta serwera proxy usługi APIM, oznacza należy użyć nazwy DNS bramy aplikacji (np. `api.contoso.net` w powyższych przykładach) do tej nazwy DNS. Aby skonfigurować rekord CNAME dla adresu IP frontonu, Pobierz szczegóły bramy aplikacji i skojarzone nazwy DNS adresu IP używając elementu PublicIPAddress. Korzystanie z rekordów A nie jest zalecane, ponieważ wirtualne adresy IP mogą ulec zmianie po ponownym uruchomieniu bramy.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"> </a> Podsumowanie
Usługa Azure API Management skonfigurowany w sieci Wirtualnej zapewnia interfejs jednej bramy dla wszystkich skonfigurowanych interfejsów API, czy są one hostowane lokalnie lub w chmurze. Integrowanie bramy aplikacji z usługą API Management zapewnia elastyczność selektywnie Włączanie określonego interfejsy API dostępne w Internecie, a także zapewnianie zapory aplikacji sieci Web jako frontonu do Twojego wystąpienia usługi API Management.

## <a name="next-steps"> </a> Następne kroki
* Dowiedz się więcej o usłudze Azure Application Gateway
  * [Application Gateway — omówienie](../application-gateway/application-gateway-introduction.md)
  * [Zapory aplikacji sieci Web usługi Application Gateway](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Bramy aplikacji przy użyciu routingu opartego na ścieżkach](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Dowiedz się więcej na temat usługi API Management i sieciami wirtualnymi
  * [Korzystanie z API Management jest dostępna tylko w obrębie sieci Wirtualnej](api-management-using-with-internal-vnet.md)
  * [Za pomocą usługi API Management w sieci Wirtualnej](api-management-using-with-vnet.md)
