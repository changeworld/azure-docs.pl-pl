---
title: Jak używać usługi Azure API Management w sieci wirtualnej z bramą aplikacji | Dokumentacja firmy Microsoft
description: Informacje o sposobie instalacji i konfiguracji usługi Azure API Management w wewnętrznej sieci wirtualnej z aplikacji bramy (WAF) jako serwera sieci Web
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: 9a6e63e95b833c960356b82a19127ec91a791b98
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Zintegrować zarządzanie interfejsami API w wewnętrznej sieci Wirtualnej z bramy aplikacji 

##<a name="overview"> </a> Omówienie
 
Usługi interfejsu API zarządzania można skonfigurować w sieci wirtualnej w trybie wewnętrznej, dzięki czemu dostępny tylko w obrębie sieci wirtualnej. Bramy aplikacji Azure to usługa PAAS, która udostępnia usługi równoważenia obciążenia warstwy 7. Pełni rolę usługi proxy odwrotnie, a udostępnia między oferty zapory aplikacji sieci Web (WAF).

Łączenie usługi API Management udostępniane w wewnętrznej sieci Wirtualnej z frontonu bramy aplikacji umożliwia następujące scenariusze:

* Użyj tego samego zasobu interfejsu API zarządzania zużycia zarówno przez użytkowników wewnętrznych i zewnętrznych konsumentów.
* Użyj pojedynczego zasobu interfejsu API zarządzania i podzestawu interfejsów API zdefiniowanych w usłudze API Management dostępne dla użytkowników zewnętrznych.
* Umożliwiają gotowe włączenie i wyłączenie z publicznego Internetu dostępu do interfejsu API zarządzania. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musi mieć:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie APIM. Aby uzyskać więcej informacji, zobacz [utworzenia wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

##<a name="scenario"> </a> Scenariusz
W tym artykule opisano sposób używania jednej usługi Zarządzanie interfejsami API dla użytkowników wewnętrznych i zewnętrznych i stał się działać jako pojedynczy frontonu dla obu lokalnego i w chmurze interfejsów API. Widoczne będzie także sposób ujawniać tylko podzestaw swoje interfejsy API (w tym przykładzie, które są wyróżnione zielony) wykorzystania zewnętrznych za pomocą funkcji PathBasedRouting dostępnych w bramy aplikacji.

W pierwszym przykładzie Instalatora wszystkich interfejsów API są zarządzane tylko z sieci wirtualnej. Konsumenci wewnętrzny (wyróżniane na kolor pomarańczowy) mogą uzyskiwać dostęp do wszystkich sieci wewnętrznych i zewnętrznych interfejsów API. Ruch nigdy nie trafia do Internetu, wysokiej wydajności jest dostarczany za pomocą obwodów Express Route.

![trasy adresu URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Przed rozpoczęciem

1. Zainstaluj najnowszą wersję poleceń cmdlet programu Azure PowerShell za pomocą Instalatora platformy sieci Web. Najnowszą wersję można pobrać i zainstalować z sekcji **Windows PowerShell** strony [Pliki do pobrania](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Tworzenie sieci wirtualnej i Utwórz oddzielne podsieci dla interfejsu API zarządzania i bramy aplikacji. 
3. Jeśli chcesz utworzyć niestandardowy serwer DNS dla sieci wirtualnej, zrobić przed rozpoczęciem wdrażania. Dobrze Sprawdź, czy działa przez zapewnienie im maszyny wirtualnej utworzonej w nową podsieć w sieci wirtualnej można rozwiązać i dostęp do wszystkich punktów końcowych usługi Azure.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Co to jest wymagana do utworzenia integracja interfejsu API zarządzania i bramę aplikacji?

* **Pula serwerów zaplecza:** wewnętrzny wirtualny adres IP usługi Zarządzanie interfejsami API.
* **Ustawienia puli serwerów zaplecza:** każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są stosowane do wszystkich serwerów w puli.
* **Port frontonu:** jest to port publiczny, który jest otwarty na bramie aplikacji. Naciśnięcie on ruch jest kierowany do jednego z serwerów zaplecza.
* **Odbiornik:** odbiornik ma port frontonu, protokół (Http lub Https, z uwzględnieniem wielkości liter) oraz nazwę certyfikatu SSL (w przypadku konfigurowania odciążania protokołu SSL).
* **Reguła:** reguły wiąże odbiornik pulę serwerów zaplecza.
* **Niestandardowe sondy kondycji:** bramy aplikacji domyślnie używa sond na podstawie adresu IP do ustalenia w BackendAddressPool serwerów, które są aktywne. Zarządzanie interfejsami API, które usługa tylko odpowiada na żądania, które mają nagłówek hosta poprawne, dlatego sond domyślne zakończyć się niepowodzeniem. Sondy kondycji niestandardowy musi być zdefiniowana, aby ustalić, że usługa jest aktywne i powinny przekazywania żądań bramy aplikacji.
* **Certyfikat domeny niestandardowej:** dostęp do interfejsu API zarządzania z Internetu, należy utworzyć mapowanie CNAME jego nazwy hosta na nazwę DNS frontonu bramy aplikacji. To zapewnia, że nagłówek nazwy hosta i certyfikatu wysłanego na bramie aplikacji, które jest przekazywane do interfejsu API zarządzania jest jeden APIM rozpoznać jako prawidłowy.

## <a name="overview-steps"> </a> Kroki wymagane do integracji interfejsu API zarządzania i bramy aplikacji 

1. Utworzenie grupy zasobów dla usługi Resource Manager.
2. Tworzenie sieci wirtualnej, podsieci i publicznego adresu IP dla bramy aplikacji. Utwórz inną podsieć dla interfejsu API zarządzania.
3. Tworzenie usługi Zarządzanie interfejsami API w podsieci sieci Wirtualnej utworzone powyżej i upewnij się, że w przypadku korzystania z trybu wewnętrznego.
4. Konfiguracja niestandardowa nazwa domeny w usłudze API Management.
5. Utwórz obiekt konfiguracji bramy aplikacji.
6. Utwórz zasób aplikacji bramy.
7. Utwórz rekord CNAME z bramy aplikacji na nazwę hosta serwera proxy usługi API Management publicznej nazwy DNS.

## <a name="create-a-resource-group-for-resource-manager"></a>Tworzenie grupy zasobów dla usługi Resource Manager

Upewnij się, że używasz najnowszej wersji programu Azure PowerShell. Więcej informacji znajduje się w artykule [Using Windows PowerShell with Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager) (Używanie programu Windows PowerShell z usługą Resource Manager).

### <a name="step-1"></a>Krok 1

Zaloguj się do platformy Azure.

```powershell
Connect-AzureRmAccount
```

Uwierzytelnianie przy użyciu poświadczeń.<BR>

### <a name="step-2"></a>Krok 2

Sprawdź subskrypcje dla konta i zaznacz go.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Krok 3

Utwórz grupę zasobów (ten krok można pominąć, jeśli używasz istniejącej grupy zasobów).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Usługa Azure Resource Manager wymaga, aby wszystkie grupy zasobów określały lokalizację. Będzie ona używana jako domyślna lokalizacja dla zasobów w danej grupie zasobów. Upewnij się, że wszystkie polecenia, aby utworzyć bramę aplikacji używać tej samej grupie zasobów.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Utwórz sieć wirtualną i podsieć bramy aplikacji

Poniższy przykład pokazuje, jak utworzyć sieć wirtualną przy użyciu zasobu manager.

### <a name="step-1"></a>Krok 1

Przypisz zakresu adresów 10.0.0.0/24 do zmiennej podsieci do użycia dla bramy aplikacji podczas tworzenia sieci wirtualnej.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Krok 2

Przypisz 10.0.1.0/24 zakres adresów do zmiennej podsieci do użycia dla interfejsu API zarządzania podczas tworzenia sieci wirtualnej.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Krok 3

Tworzenie sieci wirtualnej o nazwie **appgwvnet** w grupie zasobów **apim-appGw-zarządcy zasobów** dla regionu zachodnie stany USA, przy użyciu 10.0.0.0/16 prefiks podsieci 10.0.0.0/24 i 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Krok 4

Przypisanie zmiennej podsieci następne kroki

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Tworzenie usługi Zarządzanie interfejsami API w sieci Wirtualnej w trybie wewnętrzny

Poniższy przykład pokazuje, jak można utworzyć usługi Zarządzanie interfejsami API w sieci Wirtualnej skonfigurowana dla tylko wewnętrzny dostępu.

### <a name="step-1"></a>Krok 1
Utwórz obiekt sieci wirtualnej interfejsu API zarządzania za pomocą podsieci $apimsubnetdata utworzone powyżej.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Krok 2
Tworzenie usługi Zarządzanie interfejsami API w sieci wirtualnej.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
Po pomyślnym zainicjowaniu powyższego polecenia dotyczą [konfiguracji DNS wymagany dostęp do wewnętrznych usługi Zarządzanie interfejsami API sieci Wirtualnej](api-management-using-with-internal-vnet.md#apim-dns-configuration) do niego dostęp.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Ustawienie niestandardowej nazwy domeny w usłudze API Management

### <a name="step-1"></a>Krok 1
Przekaż certyfikat z kluczem prywatnym w domenie. W tym przykładzie będzie `*.contoso.net`. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>Krok 2
Po przesłaniu certyfikatu, Utwórz obiekt konfiguracji nazwę hosta dla serwera proxy z nazwą hosta z `api.contoso.net`, ponieważ certyfikat przykład stanowi źródło dla `*.contoso.net` domeny. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz zasób publicznego adresu IP **publicIP01** w grupie zasobów **apim-appGw-zarządcy zasobów** dla regionu zachodnie stany USA.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

Adres IP jest przypisywany do bramy aplikacji w chwili uruchamiania usługi.

## <a name="create-application-gateway-configuration"></a>Tworzenie konfiguracji bramy aplikacji

Wszystkie elementy konfiguracji muszą zostać skonfigurowane przed utworzeniem bramy aplikacji. Poniższe kroki umożliwiają utworzenie elementów konfiguracji wymaganych w przypadku zasobu bramy aplikacji.

### <a name="step-1"></a>Krok 1

Utwórz konfigurację adresu IP bramy aplikacji o nazwie **gatewayIP01**. Uruchomiona usługa Application Gateway wybierze adres IP ze skonfigurowanej podsieci i skieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Krok 2

Skonfiguruj port IP frontonu publiczny punkt końcowy IP. Jest to port, podłączoną do użytkowników końcowych.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>Krok 3

Skonfiguruj adres IP frontonu z punktem końcowym publicznego adresu IP.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Krok 4

Skonfiguruj certyfikat bramy aplikacji, używane do odszyfrowania i zaszyfrowanie ruchu przechodzącego przez.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Krok 5

Utwórz odbiornik HTTP bramy aplikacji. Przypisać IP frontonu certyfikatu konfiguracji, portu i protokołu ssl.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Krok 6

Tworzenie niestandardowych sondy do usługi API Management `ContosoApi` punktu końcowego domeny serwera proxy. Ścieżka `/status-0123456789abcdef` jest domyślnego kondycji punktu końcowego hostowanych na wszystkie usługi Zarządzanie interfejsami API. Ustaw `api.contoso.net` jako nazwę hosta niestandardowego sondy do zabezpiecz ją przy użyciu certyfikatu SSL.

> [!NOTE]
> Nazwa hosta `contosoapi.azure-api.net` jest nazwą hosta serwera proxy domyślne, które zostały skonfigurowane podczas usługi o nazwie `contosoapi` jest tworzony w publicznej Azure. 
> 

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Krok 7

Przekaż certyfikat do użycia zasobów puli wewnętrznej bazy danych z włączoną obsługą protokołu SSL. Jest to ten sam certyfikat, który podano w kroku 4 powyżej.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Krok 8

Skonfiguruj ustawienia HTTP zaplecza bramy aplikacji. Dotyczy to również ustawienie limitu czasu, po którym są anulowane żądania wewnętrznej bazy danych. Ta wartość jest inny niż limit czasu sondowania.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Krok 9

Skonfiguruj pulę adresów IP zaplecza, o nazwie **apimbackend** z wewnętrznego wirtualnego adresu IP adresu usługi Zarządzanie interfejsami API utworzone powyżej.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Krok 10

Tworzenie ustawień dla fikcyjnego zaplecza (nieistniejącego). Żądania do ścieżki interfejsu API, które nie chcemy ujawniać z interfejsu API zarządzania za pośrednictwem bramy aplikacji będzie trafień tego wewnętrznej bazy danych i zwróć 404.

Skonfiguruj ustawienia HTTP zaplecza zastępczego.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Skonfiguruj fikcyjny zaplecza **dummyBackendPool**, który wskazuje adres FQDN **dummybackend.com**. Ten adres FQDN nie istnieje w sieci wirtualnej.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Utwórz ustawienie reguł używanego domyślnie wskazujący na wewnętrznej bazy danych nie istnieje bramę aplikacji **dummybackend.com** w sieci wirtualnej.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>Krok 11

Konfiguruj adres URL reguły ścieżki dla pul zaplecza. Dzięki temu, wybierając tylko niektóre z interfejsów API z interfejsu API zarządzania dla są udostępniane publicznie. Na przykład, jeśli istnieją `Echo API` (/ echo /), `Calculator API` (/calc/) itd. Sprawdź tylko `Echo API` dostępny z Internetu). 

Poniższy przykład tworzy Prosta reguła dla "/ echo /" ścieżkę routingu ruchu sieciowego do wewnętrznej "apimProxyBackendPool".

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Jeśli ścieżka nie odpowiada reguły ścieżki, chcemy, aby umożliwić z interfejsu API zarządzania, Konfiguracja mapowania ścieżki reguły konfiguruje również domyślną pulę adresów zaplecza o nazwie **dummyBackendPool**. Na przykład http://api.contoso.net/calc/* przechodzi do **dummyBackendPool** określone jako domyślna pula cofanie dopasowane ruchu.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

Powyższy krok zapewnia, że tylko żądania dla ścieżki "/ echo" są dozwolone przez bramę aplikacji. Żądania do innych interfejsów API skonfigurowane w usłudze API Management spowoduje zgłoszenie błędów 404 z bramy aplikacji podczas uzyskiwania dostępu do z Internetu. 

### <a name="step-12"></a>Krok 12

Utwórz ustawienie reguły dla bramy aplikacji korzystać z routingu na podstawie ścieżki adresu URL.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>Krok 13

Skonfiguruj liczbę wystąpień i rozmiaru bramy aplikacji. W tym miejscu używamy [SKU zapory aplikacji sieci Web](../application-gateway/application-gateway-webapplicationfirewall-overview.md) Aby zwiększyć bezpieczeństwo zasobu interfejsu API zarządzania.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>Krok 14

Konfigurowanie zapory aplikacji sieci Web w trybie "Zapobiegania".
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Utwórz bramę aplikacji

Utwórz bramę aplikacji ze wszystkimi obiektami konfiguracji z powyższych kroków.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME nazwy hosta serwera proxy usługi API Management do publicznej nazwy DNS zasobu bramy aplikacji

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontonu na potrzeby komunikacji. Korzystając z publicznym adresem IP, brama aplikacji wymaga przypisywany dynamicznie nazwy DNS, które nie mogą być łatwe w użyciu. 

Brama aplikacji w nazwa DNS należy utworzyć rekord CNAME, który wskazuje nazwę hosta serwera proxy APIM (np. `api.contoso.net` w powyższych przykładach) do tej nazwy DNS. Aby skonfigurować rekord IP CNAME serwera sieci Web, należy pobrać szczegółów bramy aplikacji i jej skojarzonej nazwy IP DNS za pomocą elementu publicznego adresu IP. Użycie rekordów A nie jest zalecane, ponieważ adres VIP może się zmieniać podczas ponownego uruchomienia bramy.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"> </a> Podsumowanie
Skonfigurowane w sieci Wirtualnej platformy Azure API Management oferuje interfejs pojedyncza brama dla wszystkich skonfigurowanych interfejsów API, czy są one lokalnego hostowanej lub w chmurze. Integrowanie aplikacji bramy z interfejsu API zarządzania zapewnia elastyczność selektywne włączenie określonego interfejsów API, aby być dostępna w Internecie, a także dostarczając zapory aplikacji sieci Web frontonu do Twojego wystąpienia usługi API Management.

##<a name="next-steps"> </a> Następne kroki
* Dowiedz się więcej na temat bramy aplikacji Azure
  * [Omówienie bramy aplikacji](../application-gateway/application-gateway-introduction.md)
  * [Zapora aplikacji sieci Web bramy aplikacji](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Brama aplikacji przy użyciu routingu na podstawie ścieżki](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Dowiedz się więcej o interfejsu API zarządzania i sieci wirtualnych
  * [Za pomocą interfejsu API zarządzania dostępny tylko w ramach sieci Wirtualnej](api-management-using-with-internal-vnet.md)
  * [Za pomocą interfejsu API zarządzania w sieci Wirtualnej](api-management-using-with-vnet.md)
