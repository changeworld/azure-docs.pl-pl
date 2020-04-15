---
title: Konfigurowanie kompleksowego protokołu TLS za pomocą bramy aplikacji platformy Azure
description: W tym artykule opisano sposób konfigurowania kompleksowego protokołu TLS z usługą Azure Application Gateway przy użyciu programu PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: 481cbda1d35f7d630dabca00fd01677f542447c2
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312495"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>Konfigurowanie protokołu TLS końcowego do końcowego przy użyciu bramy aplikacji w programie PowerShell

## <a name="overview"></a>Omówienie

Usługa Azure Application Gateway obsługuje kompleksowe szyfrowanie ruchu. Brama aplikacji kończy połączenie TLS/SSL w bramie aplikacji. Następnie brama stosuje reguły routingu do ruchu, ponownie szyfruje pakiet i przesyła dalej pakiet do odpowiedniego serwera zaplecza na podstawie zdefiniowanych reguł routingu. Każda odpowiedź z serwera sieci Web przechodzi przez ten sam proces z powrotem do użytkownika końcowego.

Brama aplikacji obsługuje definiowanie niestandardowych opcji TLS. Obsługuje również wyłączenie następujących wersji protokołu: **TLSv1.0**, **TLSv1.1**i **TLSv1.2**, a także określenie, które mechanizmy szyfrowania do użycia i kolejność preferencji. Aby dowiedzieć się więcej o konfigurowalnych opcjach protokołu TLS, zobacz [omówienie zasad TLS](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> Protokół SSL 2.0 i SSL 3.0 są domyślnie wyłączone i nie można ich włączyć. Są one uważane za niezabezpieczone i nie mogą być używane z bramą aplikacji.

![obraz scenariusza][scenario]

## <a name="scenario"></a>Scenariusz

W tym scenariuszu dowiesz się, jak utworzyć bramę aplikacji przy użyciu end-to-end TLS z programem PowerShell.

W tym scenariuszu:

* Utwórz grupę zasobów o nazwie **appgw-rg**.
* Utwórz sieć wirtualną o nazwie **appgwvnet** o powierzchni adresowej **10.0.0.0/16**.
* Utwórz dwie podsieci o nazwie **appgwsubnet** i **appsubnet**.
* Utwórz małą bramę aplikacji obsługującą kompleksowe szyfrowanie TLS, która ogranicza wersje protokołów TLS i pakiety szyfrowania.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby skonfigurować kompleksowe tls z bramą aplikacji, certyfikat jest wymagany dla bramy i certyfikaty są wymagane dla serwerów zaplecza. Certyfikat bramy jest używany do wyprowadzania klucza symetrycznego zgodnie ze specyfikacją protokołu TLS. Klucz symetryczny jest następnie używany szyfrować i odszyfrowywać ruch wysyłany do bramy. Certyfikat bramy musi być w formacie wymiany informacji osobistych (PFX). Ten format pliku umożliwia eksportowanie klucza prywatnego, który jest wymagany przez bramę aplikacji do wykonywania szyfrowania i odszyfrowywania ruchu.

W przypadku szyfrowania TLS end-to-end brama aplikacji musi być jawnie dozwolona przez bramę aplikacji. Przekaż publiczny certyfikat serwerów zaplecza do bramy aplikacji. Dodanie certyfikatu gwarantuje, że brama aplikacji komunikuje się tylko ze znanymi wystąpieniami zaplecza. To dodatkowo zabezpiecza komunikację end-to-end.

Proces konfiguracji jest opisany w poniższych sekcjach.

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

W tej sekcji można przejść przez tworzenie grupy zasobów, która zawiera bramę aplikacji.

1. Zaloguj się do swojego konta platformy Azure.

   ```powershell
   Connect-AzAccount
   ```

2. Wybierz subskrypcję, która ma być używana w tym scenariuszu.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Utwórz grupę zasobów. (Pomiń ten krok, jeśli używasz istniejącej grupy zasobów).

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Tworzenie sieci wirtualnej i podsieci dla bramy aplikacji

Poniższy przykład tworzy sieć wirtualną i dwie podsieci. Jedna podsieć jest używana do przechowywania bramy aplikacji. Druga podsieć jest używana dla zaplecza, które hostuje aplikację sieci web.

1. Przypisz zakres adresów dla podsieci, która ma być używana dla bramy aplikacji.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Podsieci skonfigurowane dla bramy aplikacji powinny być odpowiednio dobrane. Bramę aplikacji można skonfigurować dla maksymalnie 10 wystąpień. Każde wystąpienie przyjmuje jeden adres IP z podsieci. Zbyt mała podsieć może niekorzystnie wpłynąć na skalowanie bramy aplikacji.
   >

2. Przypisz zakres adresów, który ma być używany dla puli adresów zaplecza.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Utwórz sieć wirtualną z podsieciami zdefiniowanymi w poprzednich krokach.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Pobierz zasoby zasobów sieci wirtualnej i podsieci, które mają być używane w kolejnych krokach.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz publiczny zasób IP, który ma być używany dla bramy aplikacji. Ten publiczny adres IP jest używany w jednym z kroków, które następują.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Brama aplikacji nie obsługuje używania publicznego adresu IP utworzonego przy użyciu zdefiniowanej etykiety domeny. Obsługiwany jest tylko publiczny adres IP z dynamicznie tworzoną etykietą domeny. Jeśli wymagana jest przyjazna nazwa DNS bramy aplikacji, zaleca się użycie rekordu CNAME jako aliasu.

## <a name="create-an-application-gateway-configuration-object"></a>Tworzenie obiektu konfiguracji bramy aplikacji

Wszystkie elementy konfiguracji są ustawiane przed utworzeniem bramy aplikacji. Poniższe kroki umożliwiają utworzenie elementów konfiguracji wymaganych w przypadku zasobu bramy aplikacji.

1. Tworzenie konfiguracji IP bramy aplikacji. To ustawienie umożliwia skonfigurowanie, z której podsieci używana jest brama aplikacji. Po uruchomieniu bramy aplikacji pobiera adres IP ze skonfigurowanej podsieci i kieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Utwórz konfigurację ip front-endu. To ustawienie mapuje prywatny lub publiczny adres IP na frontołów bramy aplikacji. Poniższy krok kojarzy publiczny adres IP w poprzednim kroku z konfiguracją ip front-end.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Skonfiguruj pulę adresów IP zaplecza z adresami IP serwerów sieci web zaplecza. Będą to adresy IP odbierające ruch sieciowy pochodzący z punktu końcowego adresu IP frontonu. Zastąp adresy IP w próbce własnymi punktami końcowymi adresu IP aplikacji.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > W pełni kwalifikowana nazwa domeny (FQDN) jest również prawidłową wartością, która ma być używana zamiast adresu IP serwerów zaplecza. Włącz go za pomocą **przełącznika -BackendFqdns.** 

4. Skonfiguruj port IP front-end dla publicznego punktu końcowego IP. Ten port jest portem, z który łączą się użytkownicy końcowi.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Skonfiguruj certyfikat bramy aplikacji. Ten certyfikat służy do odszyfrowywania i ponownego szyfrowania ruchu na bramie aplikacji.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > W tym przykładzie konfiguruje certyfikat używany dla połączenia TLS. Certyfikat musi być w formacie .pfx, a hasło musi zawierać od 4 do 12 znaków.

6. Utwórz odbiornik HTTP dla bramy aplikacji. Przypisz konfigurację frontu IP, port i certyfikat TLS/SSL do użycia.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Przekaż certyfikat, który ma być używany w zasobach puli zaplecza z obsługą protokołu TLS.

   > [!NOTE]
   > Sonda domyślna pobiera klucz publiczny z *domyślnego* powiązania TLS na adres IP zaplecza i porównuje wartość klucza publicznego, którą otrzymuje z wartością klucza publicznego, którą podasz w tym miejscu. 
   > 
   > Jeśli używasz nagłówków hosta i wskazania nazwy serwera (SNI) na zapleczu, pobrany klucz publiczny może nie być zamierzoną witryną, do której przepływa ruch. W razie wątpliwości odwiedź https://127.0.0.1/ serwery zaplecza, aby potwierdzić, który certyfikat jest używany dla *domyślnego* powiązania TLS. Użyj klucza publicznego z tego żądania w tej sekcji. Jeśli używasz nagłówków hosta i SNI na powiązaniach HTTPS i nie otrzymasz https://127.0.0.1/ odpowiedzi i certyfikatu z ręcznego żądania przeglądarki na serwerach zaplecza, należy skonfigurować domyślne powiązanie TLS na nich. Jeśli tego nie zrobisz, sondy nie powiedzie się, a zaplecze nie jest na białej liście.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Certyfikat podany w poprzednim kroku powinien być kluczem publicznym certyfikatu .pfx prezentanego na zapleczu. Wyeksportuj certyfikat (a nie certyfikat główny) zainstalowany na serwerze zaplecza w formacie oświadczeń, dowodów i rozumowania (CER) i użyj go w tym kroku. Ten krok umieszcza na białej liście zaplecza z bramą aplikacji.

   Jeśli używasz jednostki SKU bramy aplikacji w wersji 2, utwórz zaufany certyfikat główny zamiast certyfikatu uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Omówienie end-to end TLS z bramą aplikacji:](ssl-overview.md#end-to-end-tls-with-the-v2-sku)

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Skonfiguruj ustawienia HTTP dla zaplecza bramy aplikacji. Przypisz certyfikat przekazany w poprzednim kroku do ustawień HTTP.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   W przypadku jednostki SKU bramy aplikacji w wersji 2 należy użyć następującego polecenia:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Utwórz regułę routingu modułu równoważenia obciążenia, która konfiguruje zachowanie modułu równoważenia obciążenia. W tym przykładzie tworzona jest podstawowa reguła okrężna.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Skonfiguruj rozmiar wystąpienia bramy aplikacji. Dostępne rozmiary **to\_Standard Small,** **\_Standard Medium**i **Standard\_Large**.  W przypadku pojemności dostępne wartości to **od 1** do **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Liczbę wystąpień 1 można wybrać do celów testowych. Ważne jest, aby wiedzieć, że liczba wystąpień w ramach dwóch wystąpień nie jest objęty umowy SLA i dlatego nie jest zalecane. Małe bramy mają być używane do testowania deweloperów, a nie do celów produkcyjnych.

11. Skonfiguruj zasady TLS, które mają być używane w bramie aplikacji. Brama aplikacji obsługuje możliwość ustawiania minimalnej wersji dla wersji protokołu TLS.

    Następujące wartości to lista wersji protokołu, które można zdefiniować:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    Poniższy przykład ustawia minimalną wersję protokołu **na TLSv1_2** i umożliwia **TLS\_ECDHE\_ECDSA\_\_z\_AES 128\_GCM\_SHA256**, **TLS\_\_ECDHE\_ECDSA\_z\_AES\_256\_GCM SHA384**i **TLS\_RSA\_tylko z\_AES\_128\_GCM\_SHA256.**

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Korzystając ze wszystkich powyższych kroków, utwórz bramę aplikacji. Utworzenie bramy jest procesem, który zajmuje dużo czasu, aby uruchomić.

Dla jednostki SKU v1 użyj poniższego polecenia
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

Dla jednostki SKU V2 użyj poniższego polecenia
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Stosowanie nowego certyfikatu, jeśli certyfikat zaplecza wygasł

Ta procedura służy do stosowania nowego certyfikatu, jeśli certyfikat zaplecza wygasł.

1. Pobierz bramę aplikacji do aktualizacji.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Dodaj nowy zasób certyfikatu z pliku cer, który zawiera klucz publiczny certyfikatu i może być również tym samym certyfikatem dodanym do odbiornika dla zakończenia protokołu TLS w bramie aplikacji.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Pobierz nowy obiekt certyfikatu uwierzytelniania do zmiennej (TypeName: Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Przypisz nowy certyfikat do ustawienia **wewnętrznej bazy danychhttp** i odsyłaj go zmienną $AuthCert. (Określ nazwę ustawienia HTTP, które chcesz zmienić).
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Zaobwestić zmiany do bramy aplikacji i przekazać nową konfigurację zawartą w zmiennej $out.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Usuwanie nieużytego wygasłego certyfikatu z ustawień HTTP

Ta procedura służy do usuwania nieużytego wygasłego certyfikatu z ustawień HTTP.

1. Pobierz bramę aplikacji do aktualizacji.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Wyświetl nazwę certyfikatu uwierzytelniania, który chcesz usunąć.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Usuń certyfikat uwierzytelniania z bramy aplikacji.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Zaobowiądnie zmianę.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>Ograniczanie wersji protokołu TLS w istniejącej bramie aplikacji

W poprzednich krokach można było utworzyć aplikację z kompleksowym protokołem TLS i wyłączyć niektóre wersje protokołu TLS. Poniższy przykład wyłącza niektóre zasady TLS w istniejącej bramie aplikacji.

1. Pobierz bramę aplikacji do aktualizacji.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Zdefiniuj zasady TLS. W poniższym przykładzie **TLSv1.0** i **TLSv1.1** są wyłączone, a mechanizmy **\_szyfrowania TLS ECDHE\_ECDSA\_z\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_z\_AES\_256\_GCM\_SHA384**i **TLS\_RSA\_z\_AES\_128\_GCM\_SHA256** są jedynymi dozwolonymi.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Na koniec zaktualizuj bramę. Ten ostatni krok jest długotrwałym zadaniem. Po zakończeniu, end-to-end TLS jest skonfigurowany na bramie aplikacji.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Pobierz nazwę DNS bramy aplikacji

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontoniju komunikacji. Brama aplikacji wymaga dynamicznie przypisanej nazwy DNS podczas korzystania z publicznego adresu IP, który nie jest przyjazny. Aby upewnić się, że użytkownicy końcowi mogą trafić do bramy aplikacji, można użyć rekordu CNAME, aby wskazać publiczny punkt końcowy bramy aplikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny na platformie Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Aby skonfigurować alias, pobierz szczegóły bramy aplikacji i skojarzonej z nią nazwy IP/DNS przy użyciu elementu **PublicIPAddress** dołączonego do bramy aplikacji. Nazwa DNS bramy aplikacji służy do tworzenia rekordu CNAME, który wskazuje dwie aplikacje sieci web na tę nazwę DNS. Nie zaleca się używania rekordów A, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

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

Aby uzyskać więcej informacji na temat wzmacniania zabezpieczeń aplikacji sieci Web za pomocą Zapory aplikacji sieci Web za pośrednictwem bramy aplikacji, zobacz [omówienie zapory aplikacji sieci Web](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
