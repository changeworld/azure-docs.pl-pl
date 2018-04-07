---
title: Konfigurowanie protokołu SSL na trasie z bramy aplikacji Azure
description: W tym artykule opisano sposób konfigurowania protokołu SSL na trasie z bramy aplikacji Azure przy użyciu programu PowerShell
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/27/2018
ms.author: victorh
ms.openlocfilehash: f4552960cf16e8f7cb89868a03e4b4699eba2540
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Konfigurowanie protokołu SSL na trasie przy użyciu bramy aplikacji przy użyciu programu PowerShell

## <a name="overview"></a>Przegląd

Brama aplikacji Azure obsługuje end-to-end szyfrowania ruchu. Brama aplikacji w zakończenie połączenia SSL na bramie aplikacji. Następnie brama dotyczy reguły routingu ruchu, ponowne zaszyfrowanie pakiet i przekazuje pakiet do odpowiedniego serwera zaplecza, zgodnie z regułami routingu zdefiniowane. Każda odpowiedź z serwera sieci Web przechodzi przez ten sam proces z powrotem do użytkownika końcowego.

Brama aplikacji w obsługuje Definiowanie niestandardowych opcji protokołu SSL. Obsługuje także wyłączenie następujących wersji protokołu: **TLSv1.0**, **TLSv1.1**, i **TLSv1.2**, jak również definiowanie mechanizmów szyfrowania, które umożliwia i kolejność preferencji . Aby dowiedzieć się więcej na temat opcji SSL można skonfigurować, zobacz [Przegląd zasad SSL](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> Protokół SSL 2.0 i 3.0 protokołu SSL są domyślnie wyłączone i nie można włączyć. One są uważane za niebezpieczne i nie można używać z bramy aplikacji.

![Scenariusz obrazu][scenario]

## <a name="scenario"></a>Scenariusz

W tym scenariuszu można dowiedzieć się, jak utworzyć bramę aplikacji przy użyciu protokołu SSL na trasie przy użyciu programu PowerShell.

W tym scenariuszu obejmują:

* Utwórz grupę zasobów o nazwie **zarządcy zasobów appgw**.
* Tworzenie sieci wirtualnej o nazwie **appgwvnet** się na przestrzeń adresową z **10.0.0.0/16**.
* Utwórz dwie podsieci o nazwie **appgwsubnet** i **appsubnet**.
* Tworzenie aplikacji małych bramy obsługi end-to-end szyfrowania SSL tej wersji protokołu SSL limity i mechanizmów szyfrowania.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby skonfigurować protokół SSL na trasie z bramy aplikacji, certyfikat jest wymagany dla bramy i certyfikaty są wymagane dla serwerów zaplecza. Certyfikat bramy jest używany do szyfrowania i odszyfrowywania ruchu wysyłane do niego za pośrednictwem protokołu SSL. Certyfikat bramy musi mieć format wymiana informacji osobistych (PFX). Ten format pliku umożliwia eksportowanie klucza prywatnego, który jest wymagany przez bramę aplikacji do szyfrowania i deszyfrowania ruchu.

Do szyfrowania SSL na trasie wewnętrznej musi być białej z bramy aplikacji. Należy przekazać certyfikatu publicznego serwerów zaplecza na bramie aplikacji. Trwa dodawanie certyfikatu gwarantuje, że bramy aplikacji komunikuje się wyłącznie z znane wystąpień zaplecza. To dodatkowe zabezpiecza komunikację end-to-end.

W poniższych sekcjach opisano sposób konfiguracji.

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Ta sekcja przeprowadzi Cię przez proces tworzenia grupy zasobów, która zawiera bramy aplikacji.


   1. Zaloguj się do konta platformy Azure.

   ```powershell
   Login-AzureRmAccount
   ```


   2. Wybierz subskrypcję do użycia na potrzeby tego scenariusza.

   ```powershell
   Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
   ```


   3. Utwórz grupę zasobów. (Pomiń ten krok, jeśli używasz istniejącej grupy zasobów).

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Tworzenie sieci wirtualnej i podsieci dla bramy aplikacji

Poniższy przykład tworzy sieć wirtualną z dwoma podsieciami. W jednej podsieci jest używany do przechowywania bramy aplikacji. Innych podsieci jest używany dla zaplecza, których hostem aplikacji sieci web.


   1. Przypisz zakres adresów podsieci do zastosowania w przypadku bramy aplikacji.

   ```powershell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Podsieci skonfigurowane dla bramy aplikacji należy ustalać poprawnie. Brama aplikacji można skonfigurować dla maksymalnie 10 wystąpień. Każde wystąpienie ma jeden adres IP z podsieci. Zbyt małe podsieci może niekorzystnie wpłynąć na skalowanie w poziomie bramy aplikacji.
   > 
   > 

   2. Przypisz zakres adresów do użycia dla puli adresów zaplecza.

   ```powershell
   $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

   3. Tworzenie sieci wirtualnej z podsieciami zdefiniowane w powyższych kroków.

   ```powershell
   $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

   4. Pobieranie zasobu sieci wirtualnej i zasoby podsieci do użycia w kolejnych krokach.

   ```powershell
   $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz zasób publicznego adresu IP do użycia dla bramy aplikacji. Ten publiczny adres IP jest używana w jeden z kroków, które należy wykonać.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Brama aplikacji nie obsługuje korzystania utworzone za pomocą etykiety zdefiniowanych domeny publicznego adresu IP. Obsługiwane jest tylko publiczny adres IP z etykietą utworzony dynamicznie domeny. Jeśli wymagana jest przyjazna nazwa DNS dla bramy aplikacji, zaleca się używania rekordu CNAME jako alias.

## <a name="create-an-application-gateway-configuration-object"></a>Tworzenie obiektu konfiguracji bramy aplikacji

Wszystkie elementy konfiguracji są ustawiane przed utworzeniem bramy aplikacji. Poniższe kroki umożliwiają utworzenie elementów konfiguracji wymaganych w przypadku zasobu bramy aplikacji.

   1. Utwórz konfigurację IP bramy aplikacji. To ustawienie określa używający podsieci bramy aplikacji. Po uruchomieniu bramy aplikacji przejmuje on z podsieci skonfigurowanego adresu IP i kieruje ruchem do adresów IP w puli adresów IP zaplecza sieciowym. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


   2. Utworzenie konfiguracji IP frontonu. To ustawienie mapuje prywatny lub publiczny adres IP frontonu bramy aplikacji. Następny krok kojarzy publicznego adresu IP w poprzednim kroku z konfiguracją IP frontonu.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

   3. Skonfiguruj pulę adresów IP zaplecza z adresami IP serwerów sieci web zaplecza. Będą to adresy IP odbierające ruch sieciowy pochodzący z punktu końcowego adresu IP frontonu. Zastąp adresów IP w próbce własne punkty końcowe adresu IP aplikacji.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > W pełni kwalifikowaną nazwą domeny (FQDN) jest również prawidłową wartość do użycia zamiast adresu IP dla serwerów zaplecza. Można włączyć za pomocą **- BackendFqdns** przełącznika. 


   4. Skonfiguruj port IP frontonu publiczny punkt końcowy IP. Jest to port, podłączoną do użytkowników końcowych.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

   5. Konfigurowanie certyfikatu dla bramy aplikacji. Ten certyfikat służy do odszyfrowania i reencrypt ruchu dla bramy aplikacji.

   ```powershell
   $password = ConvertTo-SecureString  <password for certificate file> -AsPlainText -Force 
   $cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $password 
   ```

   > [!NOTE]
   > Ten przykład konfiguruje certyfikatu używanego na potrzeby połączenia SSL. Ten certyfikat musi mieć format PFX, a hasło musi mieć 4 do 12 znaków.

   6. Utwórz odbiornik HTTP bramy aplikacji. Przypisywanie konfiguracji IP frontonu, port i certyfikat protokołu SSL.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

   7. Przekaż certyfikat do użycia zasobów włączony protokół SSL puli zaplecza.

   > [!NOTE]
   > Domyślnego badania pobiera klucza publicznego z *domyślne* powiązanie SSL na adres IP zaplecza na i porównuje wartość klucza publicznego odbiera wartość klucza publicznego zostanie podana tutaj. 
   
   > Jeśli używasz nagłówków hosta i wskazanie nazwy serwera (SNI) na wewnętrznej pobrane klucz publiczny nie może być planowanej lokacji, do których ruch. Jeśli masz wątpliwości, odwiedź witrynę https://127.0.0.1/ na serwerach wewnętrznych, aby potwierdzić certyfikat, który jest używany dla *domyślne* powiązania SSL. W tej sekcji, Użyj klucza publicznego z tym żądaniem. Jeśli korzystasz z nagłówkami hosta i SNI na powiązania HTTPS i nie pojawi się odpowiedzi i certyfikatu z żądanie przeglądarki ręczne https://127.0.0.1/ na serwerach wewnętrznych, należy skonfigurować domyślne powiązanie SSL na nich. Jeśli nie zrobisz, sondy i wewnętrznej nie jest białej.

   ```powershell
   $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > Certyfikat w tym kroku należy klucz publiczny certyfikatu .pfx na wewnętrznej. Wyeksportuj certyfikat (nie certyfikatu głównego) zainstalowane na serwerze zaplecza w formacie oświadczenia, dowód i wnioskowania (CER) i używać go w tym kroku. Ten krok whitelists zaplecza bramy aplikacji.

   8. Skonfiguruj ustawienia protokołu HTTP dla wewnętrznej bramy aplikacji. Przypisać certyfikat przekazany w poprzednim kroku w ustawieniach protokołu HTTP.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```
   9. Utwórz reguły routingu usługi równoważenia obciążenia, który konfiguruje zachowanie usługi równoważenia obciążenia. W tym przykładzie jest tworzona reguła podstawowe okrężnego.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   10. Skonfiguruj rozmiar wystąpienia bramy aplikacji. Dostępne rozmiary są **standardowe\_małych**, **standardowe\_średni**, i **standardowe\_duży**.  Pojemność, dostępne wartości to **1** za pośrednictwem **10**.

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   > [!NOTE]
   > Liczba wystąpień 1 można wybrać do celów testowych. Warto wiedzieć, że dowolnej liczby wystąpień w obszarze dwa wystąpienia nie pasuje do żadnego umowy SLA i dlatego nie jest zalecana. Mała bramy są używane dla deweloperów testu, a nie do celów produkcyjnych.

   11. Skonfiguruj zasady SSL, który ma być używany dla bramy aplikacji. Brama aplikacji w obsługuje możliwość określenia minimalnej wersji dla wersji protokołu SSL.

   Listę wersji protokołów, które mogą być definiowane są następujące wartości:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
   Poniższy przykład przedstawia wersji protocol minimalne **TLSv1_2** i umożliwia **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, i **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** tylko.

   ```powershell
   $SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
   ```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Przy użyciu powyższych kroków, Utwórz bramę aplikacji. Tworzenie bramy jest procesem, który zajmuje dużo czasu do uruchomienia.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Ograniczenia wersji protokołu SSL na istniejącą bramę aplikacji

Powyższych kroków trwało użytkownika przez proces tworzenia aplikacji przy użyciu protokołu SSL na trasie i wyłączenie niektórych wersji protokołu SSL. Poniższy przykład powoduje wyłączenie określone zasady SSL na istniejącą bramę aplikacji.

   1. Pobiera bramę aplikacji, aby zaktualizować.

   ```powershell
   $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

   2. Definiowanie zasad SSL. W poniższym przykładzie **TLSv1.0** i **TLSv1.1** są wyłączone i mechanizmów szyfrowania **TLS\_ECDHE\_ECDSA\_WITH\_ AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, i **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** jedyne te dozwolone.

   ```powershell
   Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

   3. Na koniec zaktualizuj bramy. Należy pamiętać, że ten ostatni krok długotrwałe zadanie. Po zakończeniu SSL end-to-end jest skonfigurowany dla bramy aplikacji.

   ```powershell
   $gw | Set-AzureRmApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Pobierz nazwę DNS bramy aplikacji

Po utworzeniu bramy, następnym krokiem jest skonfigurowanie frontonu dla komunikacji. Brama aplikacji wymaga przypisywany dynamicznie nazwy DNS, korzystając z publicznego adresu IP, który nie jest przyjazną. Aby zapewnić, że użytkownicy końcowi mogą trafień bramy aplikacji, umożliwia rekord CNAME wskaż publiczny punkt końcowy bramy aplikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla platformy Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Aby skonfigurować alias, należy pobrać szczegółów bramy aplikacji i skojarzonej z nią nazwy IP DNS przy użyciu **publicznego adresu IP** element dołączony na bramie aplikacji. Brama aplikacji w nazwa DNS umożliwia utworzenie rekordu CNAME, który wskazuje aplikacje dwie sieci web do tej nazwy DNS. Firma Microsoft nie zaleca się użycie rekordów A, ponieważ adres VIP, można zmienić na ponowne uruchomienie z bramy aplikacji.

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

Aby uzyskać więcej informacji na temat wzmacniania zabezpieczeń aplikacji sieci web z zapory aplikacji sieci Web za pośrednictwem bramy aplikacji, zobacz [Omówienie zapory aplikacji sieci Web](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
