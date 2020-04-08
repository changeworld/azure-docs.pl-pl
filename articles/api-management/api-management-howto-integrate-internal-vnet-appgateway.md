---
title: Jak korzystać z zarządzania interfejsami API w sieci wirtualnej z bramą aplikacji
titleSuffix: Azure API Management
description: Dowiedz się, jak skonfigurować i skonfigurować usługę Azure API Management w wewnętrznej sieci wirtualnej z bramą aplikacji (WAF) jako frontend
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 733f4b74ca7643476586189b36f4e1d3e446968b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811175"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integracja zarządzania interfejsami API w wewnętrznej sieci wirtualnej z bramą aplikacji

## <a name="overview"></a><a name="overview"> </a> Przegląd

Usługę zarządzania interfejsami API można skonfigurować w sieci wirtualnej w trybie wewnętrznym, co sprawia, że jest dostępna tylko z poziomu sieci wirtualnej. Brama aplikacji platformy Azure to usługa PAAS, która zapewnia moduł równoważenia obciążenia warstwy 7. Działa jako usługa odwrotnego serwera proxy i zapewnia wśród swojej oferty zapory aplikacji sieci Web (WAF).

Połączenie usługi Api Management aprowizowana w wewnętrznej sieci wirtualnej z frontendem bramy aplikacji umożliwia następujące scenariusze:

* Użyj tego samego zasobu zarządzania interfejsami API do użycia zarówno przez konsumentów wewnętrznych, jak i zewnętrznych.
* Użyj jednego zasobu zarządzania interfejsami API i mieć podzbiór interfejsów API zdefiniowanych w usłudze API Management dostępnych dla konsumentów zewnętrznych.
* Podaj pod klucz sposób przełączania dostępu do zarządzania interfejsami API z publicznego Internetu włączonego i wyłączonego.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby wykonać kroki opisane w tym artykule, musisz mieć:

* Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certyfikaty — pfx i cer dla nazwy hosta interfejsu API i pfx dla nazwy hosta portalu dewelopera.

## <a name="scenario"></a><a name="scenario"> </a> Scenariusz

W tym artykule opisano, jak używać jednej usługi zarządzania interfejsami API dla konsumentów wewnętrznych i zewnętrznych i uczynić ją działać jako pojedynczy interfejs dla interfejsów api zarówno w środowisku interfejsowym w chmurze, jak i w chmurze. Zobaczysz również, jak udostępnić tylko podzbiór interfejsów API (w przykładzie są one wyróżnione na zielono) dla zużycia zewnętrznego przy użyciu funkcji routingu dostępnych w bramie aplikacji.

W przykładzie pierwszej konfiguracji wszystkie interfejsy API są zarządzane tylko z poziomu sieci wirtualnej. Konsumenci wewnętrzni (wyróżnioni na pomarańczowo) mogą uzyskać dostęp do wszystkich wewnętrznych i zewnętrznych interfejsów API. Ruch nigdy nie wychodzi do Internetu. Łączność o wysokiej wydajności jest dostarczana za pośrednictwem obwodów Express Route.

![trasa url](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"> </a> Przed rozpoczęciem

* Upewnij się, że używasz najnowszej wersji programu Azure PowerShell. Zapoznaj się z instrukcjami instalacji w [witrynie Install Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Co jest wymagane do utworzenia integracji między zarządzaniem interfejsami API a bramą aplikacji?

* **Pula serwerów zaplecza:** Jest to wewnętrzny wirtualny adres IP usługi api Management.
* **Ustawienia puli serwerów zaplecza:** Każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są stosowane do wszystkich serwerów w puli.
* **Port front-end:** Jest to port publiczny, który jest otwarty na bramie aplikacji. Ruch, który go uderza, zostanie przekierowany do jednego z serwerów zaplecza.
* **Słuchacz:** Odbiornik ma port front-end, protokół (Http lub Https, te wartości są rozróżniane wielkość liter) i nazwę certyfikatu TLS/SSL (jeśli konfiguruje odciążanie TLS).
* **Zasada:** Reguła wiąże odbiornika z pulą serwerów zaplecza.
* **Niestandardowa sonda kondycji:** Brama aplikacji domyślnie używa sond opartych na adresie IP, aby dowiedzieć się, które serwery w backendAddressPool są aktywne. Usługa api Management odpowiada tylko na żądania z nagłówkiem hosta poprawne, w związku z tym domyślne sondy nie powiodą się. Niestandardowa sonda kondycji musi zostać zdefiniowana, aby brama aplikacji nie była w stanie określić, że usługa jest żywa i powinna przesyłać dalej żądania.
* **Certyfikaty domeny niestandardowej:** Aby uzyskać dostęp do zarządzania interfejsami API z Internetu, należy utworzyć mapowanie CNAME jego nazwy hosta na nazwę DNS frontonia bramy aplikacji. Gwarantuje to, że nagłówek nazwy hosta i certyfikat wysłany do bramy aplikacji, który jest przekazytywał do usługi API Management jest jeden interfejs APIM można rozpoznać jako prawidłowy. W tym przykładzie użyjemy dwóch certyfikatów — dla wewnętrznej bazy danych i portalu dewelopera.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"> </a> Kroki wymagane do integracji zarządzania interfejsami API i bramy aplikacji

1. Utworzenie grupy zasobów dla usługi Resource Manager.
2. Utwórz sieć wirtualną, podsieć i publiczny adres IP dla bramy aplikacji. Utwórz inną podsieć do zarządzania interfejsami API.
3. Utwórz usługę zarządzania interfejsami API wewnątrz podsieci sieci wirtualnej utworzonej powyżej i upewnij się, że używasz trybu wewnętrznego.
4. Konfigurowanie niestandardowej nazwy domeny w usłudze zarządzania interfejsami API.
5. Utwórz obiekt konfiguracji bramy aplikacji.
6. Tworzenie zasobu bramy aplikacji.
7. Utwórz CNAME z publicznej nazwy DNS bramy aplikacji do nazwy hosta serwera proxy zarządzania interfejsami API.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Udostępnianie portalu dewelopera zewnętrznie za pośrednictwem bramy aplikacji

W tym przewodniku udostępnimy również **portal dewelopera** zewnętrznym odbiorcom za pośrednictwem bramy aplikacji. Wymaga dodatkowych kroków, aby utworzyć detektor, sondę, ustawienia i reguły portalu dewelopera. Wszystkie szczegóły podane są w odpowiednich krokach.

> [!WARNING]
> Jeśli używasz usługi Azure AD lub uwierzytelniania innych firm, włącz funkcję [koligacji sesji opartej na plikach cookie](../application-gateway/features.md#session-affinity) w bramie aplikacji.

> [!WARNING]
> Aby zapobiec przerwaniu pobierania specyfikacji OpenAPI w portalu dewelopera przez usługę `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`WAF bramy aplikacji, należy wyłączyć regułę zapory .

## <a name="create-a-resource-group-for-resource-manager"></a>Tworzenie grupy zasobów dla usługi Resource Manager

### <a name="step-1"></a>Krok 1

Zaloguj się do platformy Azure.

```powershell
Connect-AzAccount
```

Uwierzytelnij się przy użyciu poświadczeń.

### <a name="step-2"></a>Krok 2

Wybierz żądaną subskrypcję.

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

Usługa Azure Resource Manager wymaga, żeby wszystkie grupy zasobów miały lokalizację. Będzie ona używana jako domyślna lokalizacja zasobów w danej grupie. Upewnij się, że wszystkie polecenia do utworzenia bramy aplikacji używają tej samej grupy zasobów.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Tworzenie sieci wirtualnej i podsieci dla bramy aplikacji

W poniższym przykładzie pokazano, jak utworzyć sieć wirtualną przy użyciu Menedżera zasobów.

### <a name="step-1"></a>Krok 1

Przypisz zakres adresów 10.0.0.0/24 do zmiennej podsieci, która ma być używana dla bramy aplikacji podczas tworzenia sieci wirtualnej.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Krok 2

Przypisz zakres adresów 10.0.1.0/24 do zmiennej podsieci, która ma być używana do zarządzania interfejsami API podczas tworzenia sieci wirtualnej.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Krok 3

Utwórz sieć wirtualną o nazwie **appgwvnet** w grupie zasobów **apim-appGw-RG** dla regionu Zachodnie stany USA. Użyj prefiksu 10.0.0.0/16 z podsieciami 10.0.0.0/24 i 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Krok 4

Przypisywanie zmiennej podsieci dla następnych kroków

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Tworzenie usługi zarządzania interfejsami API wewnątrz sieci wirtualnej skonfigurowanej w trybie wewnętrznym

W poniższym przykładzie pokazano, jak utworzyć usługę zarządzania interfejsami API w sieci wirtualnej skonfigurowanej tylko dla dostępu wewnętrznego.

### <a name="step-1"></a>Krok 1

Utwórz obiekt sieci wirtualnej zarządzania interfejsami API przy użyciu $apimsubnetdata podsieci utworzonej powyżej.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Krok 2

Utwórz usługę zarządzania interfejsami API w sieci wirtualnej.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Po powyższych polecenia odnosi się do [konfiguracji DNS wymagane do uzyskania dostępu do wewnętrznej usługi zarządzania interfejsami API sieci wirtualnej,](api-management-using-with-internal-vnet.md#apim-dns-configuration) aby uzyskać do niego dostęp. Ten krok może potrwać ponad pół godziny.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Konfigurowanie niestandardowej nazwy domeny w usłudze Api Management

> [!IMPORTANT]
> [Nowy portal dla deweloperów](api-management-howto-developer-portal.md) wymaga również włączenia łączności z punktem końcowym zarządzania usługą API Management oprócz poniższych kroków.

### <a name="step-1"></a>Krok 1

Zainicjować następujące zmienne ze szczegółami certyfikatów z kluczami prywatnymi dla domen. W tym przykładzie `api.contoso.net` użyjemy i `portal.contoso.net`.  

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

Tworzenie i ustawianie obiektów konfiguracji nazwy hosta dla serwera proxy i portalu.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Aby skonfigurować łączność starszego portalu `-HostnameType DeveloperPortal` dewelopera, należy zastąpić programem `-HostnameType Portal`.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz publiczny zasób IP **publicIP01** w grupie zasobów.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Adres IP jest przypisywany do bramy aplikacji w chwili uruchamiania usługi.

## <a name="create-application-gateway-configuration"></a>Tworzenie konfiguracji bramy aplikacji

Wszystkie elementy konfiguracji muszą zostać skonfigurowane przed utworzeniem bramy aplikacji. Poniższe kroki umożliwiają utworzenie elementów konfiguracji wymaganych w przypadku zasobu bramy aplikacji.

### <a name="step-1"></a>Krok 1

Tworzenie konfiguracji IP bramy aplikacji o nazwie **gatewayIP01**. Uruchomiona usługa Application Gateway wybierze adres IP ze skonfigurowanej podsieci i skieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Krok 2

Skonfiguruj port IP front-end dla publicznego punktu końcowego IP. Ten port jest portem, z który łączą się użytkownicy końcowi.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Krok 3

Skonfiguruj adres IP frontonu z punktem końcowym publicznego adresu IP.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Krok 4

Skonfiguruj certyfikaty bramy aplikacji, która będzie używana do odszyfrowywania i ponownego szyfrowania ruchu przechodzącego.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Krok 5

Tworzenie odbiorników HTTP dla bramy aplikacji. Przypisz do nich konfigurację frontu IP, port i certyfikaty TLS/SSL.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Krok 6

Tworzenie niestandardowych sond do `ContosoApi` punktu końcowego domeny serwera proxy usługi api Management. Ścieżka `/status-0123456789abcdef` jest domyślnym punktem końcowym kondycji hostowanym we wszystkich usługach zarządzania interfejsami API. Ustaw `api.contoso.net` jako niestandardową nazwa hosta sondy, aby zabezpieczyć ją za pomocą certyfikatu TLS/SSL.

> [!NOTE]
> Nazwa hosta `contosoapi.azure-api.net` jest domyślną nazwę hosta serwera `contosoapi` proxy skonfigurowany, gdy usługa o nazwie jest tworzona w publicznej platformy Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Krok 7

Przekaż certyfikat, który ma być używany w zasobach puli wewnętrznej bazy danych z obsługą protokołu TLS. Jest to ten sam certyfikat, który został podany w kroku 4 powyżej.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Krok 8

Konfigurowanie ustawień wewnętrznej bazy danych HTTP dla bramy aplikacji. Obejmuje to ustawienie limitu limitu czasu dla żądania zaplecza, po którym są one anulowane. Ta wartość różni się od limitu czasu sondy.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Krok 9

Skonfiguruj zapleczową pulę adresów IP o nazwie **apimbackend** z wewnętrznym wirtualnym adresem IP usługi api Management utworzonej powyżej.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Krok 10

Utwórz reguły bramy aplikacji, aby używać routingu podstawowego.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Zmień -RuleType i routingu, aby ograniczyć dostęp do niektórych stron portalu dewelopera.

### <a name="step-11"></a>Krok 11

Skonfiguruj liczbę wystąpień i rozmiar bramy aplikacji. W tym przykładzie używamy jednostki [SKU WAF](../application-gateway/application-gateway-webapplicationfirewall-overview.md) dla zwiększenia bezpieczeństwa zasobu zarządzania interfejsami API.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Krok 12

Skonfiguruj WAF tak, aby była w trybie "Zapobieganie".

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Tworzenie bramy aplikacji

Utwórz bramę aplikacji ze wszystkimi obiektami konfiguracji z poprzednich kroków.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME nazwa hosta serwera proxy zarządzania interfejsami API do publicznej nazwy DNS zasobu Bramy aplikacji

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontonu na potrzeby komunikacji. W przypadku korzystania z publicznego adresu IP brama aplikacji wymaga dynamicznie przypisanej nazwy DNS, która może nie być łatwa w użyciu.

Nazwa DNS bramy aplikacji powinna być używana do tworzenia rekordu CNAME, który wskazuje nazwę `api.contoso.net` hosta serwera proxy APIM (np. w powyższych przykładach) na tę nazwę DNS. Aby skonfigurować rekord CNAME IP w interfejsie, pobierz szczegóły bramy aplikacji i skojarzonej z nią nazwy IP/DNS przy użyciu elementu PublicIPAddress. Użycie rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"> </a> Podsumowanie
Usługa Azure API Management skonfigurowana w sieci wirtualnej zapewnia interfejs jednej bramy dla wszystkich skonfigurowanych interfejsów API, niezależnie od tego, czy są one hostowane lokalnie, czy w chmurze. Integracja bramy aplikacji z zarządzaniem interfejsami API zapewnia elastyczność selektywnego włączania określonych interfejsów API, które mają być dostępne w Internecie, a także dostarczania zapory aplikacji sieci Web jako frontonu do wystąpienia zarządzania interfejsem interfejsu API.

## <a name="next-steps"></a><a name="next-steps"> </a> Kolejne kroki
* Dowiedz się więcej o usłudze Azure Application Gateway
  * [Omówienie bramy aplikacji](../application-gateway/application-gateway-introduction.md)
  * [Zapora aplikacji bramy aplikacji sieci Web](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Brama aplikacji przy użyciu routingu opartego na ścieżce](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Dowiedz się więcej o zarządzaniu api i wirtualnych firmach wirtualnych
  * [Korzystanie z usługi API Management dostępne tylko w sieci wirtualnej](api-management-using-with-internal-vnet.md)
  * [Korzystanie z zarządzania interfejsami API w sieci VNET](api-management-using-with-vnet.md)
