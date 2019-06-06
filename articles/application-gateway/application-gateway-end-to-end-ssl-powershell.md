---
title: Konfigurowanie certyfikatu SSL end-to-end za pomocą usługi Azure Application Gateway
description: W tym artykule opisano sposób konfigurowania end-to-end SSL przy użyciu usługi Azure Application Gateway przy użyciu programu PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: d9851f6b3e32d0c7ab0d7774458ba5bc4d9ba823
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729674"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Konfigurowanie kompleksowej usługi SSL przy użyciu bramy aplikacji przy użyciu programu PowerShell

## <a name="overview"></a>Omówienie

Usługa Azure Application Gateway obsługuje end-to-end szyfrowania ruchu. Usługa Application Gateway kończy połączenia SSL na bramie aplikacji. Następnie brama stosuje reguły routingu do ruchu sieciowego, ponownie szyfruje pakiet i przekazuje pakiet do odpowiedniego serwera zaplecza na podstawie reguł routingu zdefiniowane. Każda odpowiedź z serwera sieci Web przechodzi przez ten sam proces z powrotem do użytkownika końcowego.

Usługa Application Gateway obsługuje definiowanie niestandardowe opcje protokołu SSL. Obsługuje ona również wyłączenie następujących protokołów: **TLSv1.0**, **TLSv1.1**, i **zabezpieczeń TLSv1.2**, jak również definiowanie mechanizmów szyfrowania, które do użycia i w kolejności preferencji. Aby dowiedzieć się, jak można konfigurować opcje protokołu SSL, zobacz [Przegląd zasad SSL](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> Protokoły SSL 2.0 i protokołu SSL 3.0 są domyślnie wyłączone i nie może być włączone. One są uważane za niebezpieczne i nie można używać z usługą Application Gateway.

![Obraz scenariusza][scenario]

## <a name="scenario"></a>Scenariusz

W tym scenariuszu dowiesz się, jak utworzyć bramę aplikacji przy użyciu protokołu SSL end-to-end przy użyciu programu PowerShell.

W tym scenariuszu wykonują następujące czynności:

* Utwórz grupę zasobów o nazwie **appgw-rg**.
* Tworzenie sieci wirtualnej o nazwie **appgwvnet** z przestrzeni adresowej **10.0.0.0/16**.
* Utworzyć dwie podsieci o nazwie **appgwsubnet** i **appsubnet**.
* Utwórz mała aplikacja bramy pomocnicze end-to-end szyfrowania SSL w tej wersji protokołu SSL limitów i mechanizmów szyfrowania.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do skonfigurowania end-to-end SSL z usługą application gateway, certyfikat jest wymagany dla bramy i certyfikaty są wymagane do serwerów zaplecza. Certyfikat bramy jest używany do uzyskania klucza symetrycznego zgodnie z specyfikacją protokołu SSL. Klucz symetryczny jest następnie używany szyfruje i odszyfrowuje ruch wysyłany do bramy. Certyfikat bramy musi mieć format wymiany informacji osobistych (PFX). Ten format pliku umożliwia eksportowanie klucza prywatnego, wymagane przez tę bramę aplikacji z realizacją szyfrowania i odszyfrowywania ruchu.

End-to-end szyfrowania SSL wewnętrznej musi być jawnie dozwolone przez usługę application gateway. Przekazywanie certyfikatu publicznego serwera zaplecza do usługi application gateway. Dodawanie certyfikatu gwarantuje, że bramy application gateway komunikuje się tylko ze znanych wystąpień zaplecza. W ten sposób dalszej komunikacji end-to-end.

W poniższych sekcjach opisano sposób konfiguracji.

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Ta sekcja przeprowadzi Cię przez tworzenie grupy zasobów, która zawiera bramę application gateway.

1. Zaloguj się do swojego konta platformy Azure.

   ```powershell
   Connect-AzAccount
   ```

2. Wybierz subskrypcję do użycia dla tego scenariusza.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Utwórz grupę zasobów. (Pomiń ten krok, jeśli używasz istniejącej grupy zasobów).

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Tworzenie sieci wirtualnej i podsieci dla bramy aplikacji

Poniższy przykład tworzy sieć wirtualną i dwie podsieci. Jedną podsieć jest używana do przechowywania bramy aplikacji. Innych podsieci jest używana do zaplecza, które hostują aplikację sieci web.

1. Przypisz zakres adresów podsieci, która ma być używany dla usługi application gateway.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Podsieci skonfigurowane w usłudze application gateway należy poprawnie wielkości. Bramy aplikacji można skonfigurować dla maksymalnie 10 wystąpień. Każde wystąpienie zajmuje się jeden adres IP z podsieci. Zbyt małe podsieci może niekorzystnie wpłynąć na skalowanie w poziomie bramy aplikacji.
   >

2. Przypisz zakres adresów ma być używany dla puli adresów zaplecza.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Utwórz sieć wirtualną z podsieciami, zdefiniowane w poprzednich krokach.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Pobierz zasób sieci wirtualnej i podsieci zasobów do użycia w kolejnych krokach.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz zasób publicznego adresu IP ma być używany dla usługi application gateway. Ten publiczny adres IP jest używany w jednej z opisanych poniżej.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Usługa Application Gateway nie obsługuje użycia publiczny adres IP utworzony za pomocą etykiety domeny zdefiniowane. Tylko publiczny adres IP, z etykietą dynamicznie utworzoną domeny jest obsługiwany. Jeśli wymagana jest przyjazna nazwa DNS dla usługi application gateway, zalecane jest rekord CNAME jest używany jako alias.

## <a name="create-an-application-gateway-configuration-object"></a>Tworzenie obiektu konfiguracji bramy aplikacji

Wszystkie elementy konfiguracji są ustawiane przed utworzeniem bramy aplikacji. Poniższe kroki umożliwiają utworzenie elementów konfiguracji wymaganych w przypadku zasobu bramy aplikacji.

1. Utwórz konfigurację adresu IP bramy aplikacji. To ustawienie określa korzystającego z podsieci bramy aplikacji. Uruchomiona usługa application gateway go wybierze adres IP ze skonfigurowanej podsieci i kieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Utwórz konfigurację adresów IP frontonu. To ustawienie mapuje prywatny lub publiczny adres IP frontonu bramy aplikacji. Następny krok kojarzy publiczny adres IP w poprzednim kroku z konfiguracją adresów IP frontonu.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Skonfiguruj pulę adresów IP zaplecza za pomocą adresów IP serwerów sieci web zaplecza. Będą to adresy IP odbierające ruch sieciowy pochodzący z punktu końcowego adresu IP frontonu. Zastąp adresy IP w próbce własne punktami końcowymi adresów IP aplikacji.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > W pełni kwalifikowaną nazwę domeny (FQDN) jest również prawidłową wartość, należy użyć zamiast adresu IP dla serwerów zaplecza. Można włączyć za pomocą **- BackendFqdns** przełącznika. 

4. Skonfiguruj port adresu IP frontonu dla punktu końcowego publicznego adresu IP. Ten port jest numer portu, którego użytkownicy końcowi nawiązywać połączenie.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Konfigurowanie certyfikatu dla usługi application gateway. Ten certyfikat jest używany do odszyfrowania i ponownie zaszyfrować ruch w bramie aplikacji.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Ten przykład umożliwia skonfigurowanie certyfikatu używanego na potrzeby połączenia SSL. Ten certyfikat musi być w formacie pfx, a hasło musi mieć 4 – 12 znaków.

6. Utwórz odbiornik HTTP bramy application Gateway. Przypisywanie konfiguracji adresów IP frontonu, portu i certyfikat protokołu SSL do użycia.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Przekaż certyfikat do użycia zasobów w puli zaplecza z włączonym protokołem SSL.

   > [!NOTE]
   > Domyślnej funkcji badania pobiera klucz publiczny z *domyślne* powiązania SSL na adresie IP serwer zaplecza i porównuje wartość klucza publicznego, otrzymuje się na wartość klucza publicznego zostanie podane w tym miejscu. 
   > 
   > Jeśli używasz nagłówki hosta i oznaczaniem nazwy serwera (SNI) na zapleczu pobrane klucz publiczny nie może być planowanej lokacji, do których widok przepływów ruchu sieciowego. Jeśli jesteś w stanie wątpliwości, odwiedź stronę https://127.0.0.1/ na serwerach zaplecza, aby upewnić się, który certyfikat jest używany dla *domyślne* powiązania SSL. W tej sekcji, należy użyć klucza publicznego z tym żądaniem. Jeśli używasz nagłówki hosta i SNI na powiązania HTTPS i nie otrzymasz odpowiedzi i certyfikat od żądanie ręcznej przeglądarki https://127.0.0.1/ na serwerach zaplecza, należy skonfigurować domyślne powiązanie SSL na nich. Jeśli nie zrobisz, sondy i zapleczu nie jest umieszczona na białej liście.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Podany w poprzednim kroku certyfikat powinien być klucz publiczny certyfikatu pfx na zapleczu. Wyeksportuj certyfikat (nie certyfikat główny) zainstalowane na serwerze zaplecza w formacie oświadczeń, dowód i wnioskowania (CER) i używać go w tym kroku. Ten krok umieszczenie zaplecza za pomocą bramy application gateway.

   Jeśli używasz bramy Application Gateway jednostkę SKU v2 następnie utworzyć z zaufanym certyfikatem głównym zamiast certyfikatu uwierzytelniania. Aby uzyskać więcej informacji, zobacz [omówienie kompleksowej usługi SSL z usługą Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Skonfiguruj ustawienia HTTP zaplecza bramy aplikacji. Przypisać certyfikat przekazany w poprzednim kroku w ustawieniach protokołu HTTP.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   W ramach jednostki SKU bramy Application Gateway w wersji 2 użyj następującego polecenia:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Utwórz regułę routingu modułu równoważenia obciążenia, która służy do konfigurowania zachowania modułu równoważenia obciążenia. W tym przykładzie tworzona jest podstawową regułę działania okrężnego.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Skonfiguruj rozmiar wystąpienia bramy aplikacji. Dostępne rozmiary to **standardowa\_małych**, **standardowa\_średni**, i **standardowa\_duże**.  W przypadku pojemności dostępne wartości to **1** za pośrednictwem **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Liczbą wystąpień równą 1 można wybrać do celów testowych. Warto wiedzieć, że dowolnej liczby wystąpień w ramach dwóch wystąpień nie jest objęta umową SLA i w związku z tym nie jest zalecane. Małych bram są używane do testowania aplikacji a nie do celów produkcyjnych.

11. Konfigurowanie zasad protokołu SSL, który ma być używany w usłudze application gateway. Usługa Application Gateway obsługuje możliwość ustawienia minimalnej wersji do wersji protokołu SSL.

    Lista protokołów, które mogą być definiowane są następujące wartości:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    Poniższy przykład ustawia wersję protokołu minimalne **TLS 1_2** i umożliwia **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, i **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** tylko.

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Korzystając z powyższych kroków, Tworzenie bramy aplikacji. Tworzenie bramy to proces, który zajmuje dużo czasu do uruchomienia.

```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Zastosowania nowego certyfikatu, jeśli certyfikat zaplecza wygasł

Użyj tej procedury do zastosowania nowego certyfikatu, jeśli serwer zaplecza certyfikat wygasł.

1. Pobierz bramy aplikacji w celu zaktualizowania.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Dodaj nowy zasób, certyfikat z pliku .cer, który zawiera klucz publiczny certyfikatu i mogą być tego samego certyfikatu, które są dodawane do odbiornika dla kończenia żądań SSL na bramie aplikacji.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Pobierz nowy obiekt certyfikatu uwierzytelniania do zmiennej (nazwa typu: Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Przypisz nowy certyfikat do **BackendHttp** ustawienia można znaleźć go ze zmienną $AuthCert. (Należy określić nazwę ustawienia protokołu HTTP, który chcesz zmienić.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Zatwierdź zmianę do bramy aplikacji i przekazać nową konfigurację, które są zawarte w zmiennej $out.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Usuń nieużywane wygasły certyfikat z ustawienia HTTP

Użyj tej procedury można usunąć nieużywane wygasły certyfikat z ustawienia protokołu HTTP.

1. Pobierz bramy aplikacji w celu zaktualizowania.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Podaj nazwy certyfikatu uwierzytelniania, który chcesz usunąć.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Usuń certyfikat uwierzytelniania z bramy aplikacji.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Zatwierdź zmiany.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Ograniczenie wersji protokołu SSL w istniejącej bramie aplikacji

Poprzednie kroki trwało Cię przez proces tworzenia aplikacji przy użyciu protokołu SSL end-to-end i wyłączenie niektórych wersji protokołu SSL. Poniższy przykład wyłącza określone zasady protokołu SSL w istniejącej bramie aplikacji.

1. Pobierz bramy aplikacji w celu zaktualizowania.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Definiowanie zasad protokołu SSL. W poniższym przykładzie **TLSv1.0** i **TLSv1.1** są wyłączone i mechanizmów szyfrowania **TLS\_ECDHE\_ECDSA\_WITH\_ AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, i **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** są jedynymi te dozwolone.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Na koniec zaktualizuj bramę. Ten ostatni krok to długotrwałe zadanie. Gdy wszystko będzie gotowe, end-to-end skonfigurowano protokół SSL na bramie aplikacji.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Pobieranie nazwy DNS bramy aplikacji

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontonu na potrzeby komunikacji. Usługa Application Gateway wymaga dynamicznie przypisywanej nazwy DNS, korzystając z publicznego adresu IP, który nie jest przyjazna. Aby upewnić się, że użytkownicy końcowi mogą trafić bramę aplikacji, rekord CNAME służy do wskazania publicznego punktu końcowego bramy aplikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla platformy Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Aby skonfigurować alias, Pobierz szczegóły bramy aplikacji i skojarzone nazwy DNS adresu IP za pomocą **publicznego adresu IP** elementu dołączonego do bramy aplikacji. Użyj nazwy DNS bramy aplikacji, aby utworzyć rekord CNAME wskazujący dwóch internetowych do tej nazwy DNS. Firma Microsoft nie zaleca się korzystanie z rekordów, ponieważ adres VIP można zmienić na ponowne uruchomienie usługi application gateway.

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

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat ograniczania funkcjonalności zabezpieczeń aplikacji sieci web za pomocą zapory aplikacji sieci Web za pośrednictwem bramy Application Gateway, zobacz [Omówienie zapory aplikacji sieci Web](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
