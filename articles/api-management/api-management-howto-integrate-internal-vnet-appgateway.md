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
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: c7d4351a9691c9787c42107306220e075f8648a0
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435127"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integracja usługi API Management w wewnętrznej sieci Wirtualnej z usługą Application Gateway

##<a name="overview"> </a> Przegląd

Usługa API Management można skonfigurować w sieci wirtualnej w trybie wewnętrznego, dzięki czemu dostępny tylko w obrębie sieci wirtualnej. Usługa Azure Application Gateway jest to usługa PAAS, co zapewnia modułu równoważenia obciążenia warstwy 7. Ona działa jako usługa zwrotnego serwera proxy i zapewnia między oferty zapory aplikacji sieci Web (WAF).

Łączenie usługi API Management aprowizowane w wewnętrznej sieci Wirtualnej za pomocą frontonu bramy Application Gateway umożliwia następujące scenariusze:

* Użyj tego samego zasobu usługi API Management do użycia przez klientów wewnętrznych i zewnętrznych klientów.
* Użyj pojedynczego zasobu usługi API Management i podzestawu interfejsów API zdefiniowane w usłudze API Management jest dostępna dla klientów zewnętrznych.
* Zapewnia sposób setką kompleksowych włączenie i wyłączenie z publicznej sieci Internet. dostęp do usługi API Management.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie usługi APIM. Aby uzyskać więcej informacji, zobacz [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).

##<a name="scenario"> </a> Scenariusz
W tym artykule opisano sposób użycia jednej usługi API Management dla wewnętrznych i zewnętrznych klientów i przypisz ją do działania jako pojedynczy frontonu dla obu lokalnie i w chmurze interfejsów API. Zobaczysz również jak udostępniać tylko podzbioru interfejsów API (w tym przykładzie, które zostaną one wyróżnione w kolorze zielonym) do użytku zewnętrznego za pomocą funkcji PathBasedRouting dostępnych w usłudze Application Gateway.

W pierwszym przykładzie konfiguracji wszystkie interfejsy API są zarządzane tylko z w ramach sieci wirtualnej. Wewnętrzny konsumentów (wyróżnione w kolorze pomarańczowym) mają dostęp do wszystkich wewnętrznych i zewnętrznych interfejsów API. Ruch nigdy nie trafia do internetowego o wysokiej wydajności są dostarczane za pośrednictwem obwodów Expressroute.

![trasy adresu URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Przed rozpoczęciem

1. Zainstaluj najnowszą wersję poleceń cmdlet programu Azure PowerShell za pomocą Instalatora platformy sieci Web. Najnowszą wersję można pobrać i zainstalować z sekcji **Windows PowerShell** strony [Pliki do pobrania](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Utwórz sieć wirtualną i Utwórz oddzielne podsieci dla usługi API Management i Application Gateway.
3. Jeśli zamierzasz utworzyć niestandardowego serwera DNS dla sieci wirtualnej, to zrobić przed rozpoczęciem wdrażania. Sprawdź, który działa przez zapewnienie im maszyny wirtualnej utworzonej w nowej podsieci w sieci wirtualnej można rozwiązać i dostęp do wszystkich punktów końcowych platformy Azure.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Co to jest wymagane do utworzenia integrację usługi API Management i Application Gateway?

* **Pula serwerów zaplecza:** wewnętrznego wirtualny adres IP usługi API Management.
* **Ustawienia puli serwerów zaplecza:** każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są stosowane do wszystkich serwerów w puli.
* **Port frontonu:** to port publiczny, który jest otwierany w bramie aplikacji. Ruch osiągnięcia go jest przekierowywany do jednego z serwerów zaplecza.
* **Odbiornik:** odbiornik ma port frontonu, protokół (Http lub Https, z uwzględnieniem wielkości liter) oraz nazwę certyfikatu SSL (w przypadku konfigurowania odciążania protokołu SSL).
* **Reguła:** reguła wiąże odbiornik z pulą serwerów zaplecza.
* **Niestandardowe sondy kondycji:** Application Gateway, domyślnie używa sondy na podstawie adresów IP, aby zorientować się w BackendAddressPool serwerów, które są aktywne. API Management, którą usługa tylko odpowiada na żądania, które mają nagłówek hosta poprawne, dlatego sondy domyślne zakończyć się niepowodzeniem. Sonda kondycji niestandardowy musi można zdefiniować, aby ułatwić usługa application gateway, sprawdzić, czy usługa jest aktywny i należy go przekazywania żądań.
* **Certyfikat domeny niestandardowej:** dostęp do usługi API Management z Internetu, należy utworzyć mapowanie rekordu CNAME jego nazwy hosta na nazwę DNS frontonu bramy aplikacji. To zapewnia, że nagłówek nazwy hosta i certyfikatu wysłanego do usługi Application Gateway, który jest przekazywany do usługi API Management jest jedna APIM rozpoznać jako prawidłowy.

## <a name="overview-steps"> </a> Kroki wymagane do integrowania usługi API Management i usługa Application Gateway

1. Utworzenie grupy zasobów dla usługi Resource Manager.
2. Tworzenie sieci wirtualnej, podsieci i publicznego adresu IP dla bramy Application Gateway. Utwórz inną podsieć dla usługi API Management.
3. Tworzenie usługi API Management w podsieci sieci Wirtualnej utworzonej powyżej i upewnij się, że korzystania z trybu wewnętrznego.
4. Konfigurowanie niestandardowej nazwy domeny w usłudze API Management.
5. Tworzenie obiektu konfiguracji bramy aplikacji.
6. Utwórz zasób usługi Application Gateway.
7. Utwórz rekord CNAME z publicznej nazwy DNS bramy aplikacji do nazwy hosta serwera proxy usługi API Management.

## <a name="create-a-resource-group-for-resource-manager"></a>Tworzenie grupy zasobów dla usługi Resource Manager

Upewnij się, że używasz najnowszej wersji programu Azure PowerShell. Więcej informacji znajduje się w artykule [Using Windows PowerShell with Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager) (Używanie programu Windows PowerShell z usługą Resource Manager).

### <a name="step-1"></a>Krok 1

Zaloguj się do platformy Azure.

```powershell
Connect-AzureRmAccount
```

Uwierzytelnianie przy użyciu swoich poświadczeń.<BR>

### <a name="step-2"></a>Krok 2

Sprawdź subskrypcje dla konta, a następnie wybierz ją.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Krok 3

Utwórz grupę zasobów (ten krok można pominąć, jeśli używasz istniejącej grupy zasobów).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Usługa Azure Resource Manager wymaga, aby wszystkie grupy zasobów określały lokalizację. Będzie ona używana jako domyślna lokalizacja dla zasobów w danej grupie zasobów. Upewnij się, że wszystkich poleceniach służących do tworzenia bramy aplikacji użyj tej samej grupie zasobów.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Utwórz sieć wirtualną i podsieć dla bramy application gateway

Poniższy przykład pokazuje, jak utworzyć sieć wirtualną przy użyciu zasobu manager.

### <a name="step-1"></a>Krok 1

Przypisz zakres adresów 10.0.0.0/24 do zmiennej podsieci do użycia podczas tworzenia sieci wirtualnej dla usługi Application Gateway.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Krok 2

Przypisz 10.0.1.0/24 zakresu adresów do zmiennej podsieci do użycia podczas tworzenia sieci wirtualnej dla usługi API Management.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Krok 3

Tworzenie sieci wirtualnej o nazwie **appgwvnet** w grupie zasobów **apim-appGw-RG** dla regionu zachodnie stany USA, użyciu prefiksu 10.0.0.0/16 i podsieci 10.0.0.0/24 i 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Krok 4

Przypisz zmienną podsieci dla następnych kroków

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Tworzenie usługi API Management w sieci Wirtualnej w trybie wewnętrznego

Poniższy przykład pokazuje sposób tworzenia usługi API Management w sieci Wirtualnej skonfigurowana dla tylko wewnętrzne dostęp.

### <a name="step-1"></a>Krok 1
Utwórz obiekt sieci wirtualnej usługi API Management przy użyciu podsieci $apimsubnetdata utworzonego powyżej.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Krok 2
Tworzenie usługi API Management w sieci wirtualnej.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
Po pomyślnym powyższego polecenia dotyczą [konfiguracji DNS wymagane do uzyskania dostępu usługi wewnętrznej sieci Wirtualnej usługa API Management](api-management-using-with-internal-vnet.md#apim-dns-configuration) do niego dostęp.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Konfigurowanie niestandardowej nazwy domeny w usłudze API Management

### <a name="step-1"></a>Krok 1
Przekaż certyfikat z kluczem prywatnym dla domeny. W tym przykładzie będzie `*.contoso.net`.

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>Krok 2
Po przekazaniu tego certyfikatu, Utwórz obiekt konfiguracji nazwy hosta dla serwera proxy z nazwą hosta `api.contoso.net`, ponieważ certyfikat przykład zapewnia urzędu `*.contoso.net` domeny.

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz zasób publicznego adresu IP **publicIP01** w grupie zasobów **apim-appGw-RG** dla regionu zachodnie stany USA.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

Adres IP jest przypisywany do bramy aplikacji w chwili uruchamiania usługi.

## <a name="create-application-gateway-configuration"></a>Utwórz konfigurację bramy aplikacji

Wszystkie elementy konfiguracji muszą zostać skonfigurowane przed utworzeniem bramy aplikacji. Poniższe kroki umożliwiają utworzenie elementów konfiguracji wymaganych w przypadku zasobu bramy aplikacji.

### <a name="step-1"></a>Krok 1

Utwórz konfigurację adresu IP bramy aplikacji o nazwie **gatewayIP01**. Uruchomiona usługa Application Gateway wybierze adres IP ze skonfigurowanej podsieci i skieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Krok 2

Skonfiguruj port adresu IP frontonu dla punktu końcowego publicznego adresu IP. Ten port jest numer portu, którego użytkownicy końcowi nawiązywać połączenie.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>Krok 3

Skonfiguruj adres IP frontonu z punktem końcowym publicznego adresu IP.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Krok 4

Konfigurowanie certyfikatu dla usługi Application Gateway, używany do odszyfrowania i ponownie zaszyfrować ruch przechodzi przez.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Krok 5

Utwórz odbiornik HTTP bramy Application Gateway. Przypisać adresu IP frontonu certyfikatu konfiguracji, portu i protokołu ssl.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Krok 6

Tworzenie niestandardowej sondy do usługi API Management `ContosoApi` punktu końcowego domeny serwera proxy. Ścieżka `/status-0123456789abcdef` jest punkt końcowy kondycji na domyślne hostowanych na wszystkie usługi API Management. Ustaw `api.contoso.net` jako nazwę hosta niestandardowej sondy, aby zabezpieczyć go za pomocą certyfikatu SSL.

> [!NOTE]
> Nazwa hosta `contosoapi.azure-api.net` jest domyślną nazwę hosta serwera proxy, skonfigurować usługi o nazwie `contosoapi` jest tworzony w publicznej Azure.
>

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Krok 7

Przekaż certyfikat do użycia na zasobów w puli zaplecza z włączonym protokołem SSL. Jest to ten sam certyfikat, który podana w kroku 4 powyżej.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Krok 8

Skonfiguruj ustawienia zaplecza HTTP bramy Application Gateway. Dotyczy to również ustawienie limitu czasu dla żądania wewnętrznej bazy danych, po upływie którego zostały anulowane. Ta wartość jest inna niż limit czasu sondy.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Krok 9

Skonfiguruj pulę adresów IP zaplecza o nazwie **apimbackend** za pomocą wewnętrznego wirtualnego adresu IP adres usługi API Management utworzonego powyżej.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Krok 10

Utwórz ustawienia fikcyjnego zaplecza (nieistniejącej). Żądania do ścieżek interfejsu API, które firma Microsoft nie należy udostępniać z usługi API Management za pośrednictwem bramy aplikacji zostanie osiągnięty to zaplecze i zwrócić kod 404.

Skonfiguruj ustawienia HTTP zaplecza fikcyjnego z rolą.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Konfigurowanie fikcyjnego zaplecza **dummyBackendPool**, który wskazuje na adres FQDN **dummybackend.com**. Ten adres FQDN nie istnieje w sieci wirtualnej.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Utwórz ustawienie reguły, która Application Gateway będzie używana domyślnie, które wskazuje do wewnętrznej bazy danych nie istnieje **dummybackend.com** w sieci wirtualnej.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>Krok 11

Konfigurowanie adresu URL reguły ścieżki dla puli zaplecza. Dzięki temu można wybierać tylko niektóre z interfejsów API z usługi API Management są dostępne publicznie. Na przykład, jeśli istnieją `Echo API` (/ echo /) `Calculator API` Marka (/calc/) itp. tylko `Echo API` dostępny z Internetu).

Poniższy przykład tworzy prostą regułę dla "/ echo /" ścieżki routingu ruchu do zaplecza "apimProxyBackendPool".

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Jeśli ścieżka nie odpowiada reguły ścieżki, chcemy, aby włączyć z poziomu usługi API Management, konfigurację reguły ścieżki mapy konfiguruje również domyślną pulę adresów zaplecza o nazwie **dummyBackendPool**. Na przykład http://api.contoso.net/calc/sum przechodzi do **dummyBackendPool** jak jest zdefiniowany jako domyślna pula nie dopasowany ruchu.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

Powyższy krok zapewnia, że tylko żądania dla ścieżki "/ echo" są dozwolone za pośrednictwem bramy aplikacji. Żądania do innych interfejsów API skonfigurowany w usłudze API Management spowoduje zgłoszenie błędów 404 z bramy aplikacji z Internetu.

### <a name="step-12"></a>Krok 12

Utwórz ustawienie reguły w usłudze Application Gateway do użycia, routingu opartego na ścieżkach URL.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>Krok 13

Skonfiguruj liczbę wystąpień i rozmiar bramy aplikacji. Tutaj używamy [jednostki SKU zapory aplikacji sieci Web](../application-gateway/application-gateway-webapplicationfirewall-overview.md) Aby zwiększyć bezpieczeństwo zasobu usługi API Management.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>Krok 14

Konfigurowanie zapory aplikacji sieci Web, aby być w trybie "Zapobieganie".
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Tworzenie bramy aplikacji

Tworzenie bramy aplikacji przy użyciu obiektów konfiguracji z poprzednich kroków.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME nazwę hosta serwera proxy usługi API Management do publicznej nazwy DNS zasobu usługi Application Gateway

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontonu na potrzeby komunikacji. Korzystając z publicznym adresem IP, brama aplikacji wymaga dynamicznie przypisywanej nazwy DNS, które nie mogą być łatwe w użyciu.

Aby utworzyć rekord CNAME, który wskazuje nazwę hosta serwera proxy usługi APIM, oznacza należy użyć nazwy DNS bramy aplikacji (np. `api.contoso.net` w powyższych przykładach) do tej nazwy DNS. Aby skonfigurować rekord CNAME dla adresu IP frontonu, Pobierz szczegóły bramy aplikacji i skojarzone nazwy DNS adresu IP używając elementu PublicIPAddress. Korzystanie z rekordów A nie jest zalecane, ponieważ wirtualne adresy IP mogą ulec zmianie po ponownym uruchomieniu bramy.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"> </a> Podsumowanie
Usługa Azure API Management skonfigurowany w sieci Wirtualnej zapewnia interfejs jednej bramy dla wszystkich skonfigurowanych interfejsów API, czy są one hostowane lokalnie lub w chmurze. Integrowanie bramy aplikacji z usługą API Management zapewnia elastyczność selektywnie Włączanie określonego interfejsy API dostępne w Internecie, a także zapewnianie zapory aplikacji sieci Web jako frontonu do Twojego wystąpienia usługi API Management.

##<a name="next-steps"> </a> Następne kroki
* Dowiedz się więcej o usłudze Azure Application Gateway
  * [Application Gateway — omówienie](../application-gateway/application-gateway-introduction.md)
  * [Zapory aplikacji sieci Web usługi Application Gateway](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Bramy aplikacji przy użyciu routingu opartego na ścieżkach](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Dowiedz się więcej na temat usługi API Management i sieciami wirtualnymi
  * [Korzystanie z API Management jest dostępna tylko w obrębie sieci Wirtualnej](api-management-using-with-internal-vnet.md)
  * [Za pomocą usługi API Management w sieci Wirtualnej](api-management-using-with-vnet.md)
