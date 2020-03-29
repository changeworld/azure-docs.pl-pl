---
title: Tworzenie bezpiecznej aplikacji sieci Web usługi Azure AD | Dokumenty firmy Microsoft
description: Ta prosta przykładowa aplikacja implementuje najlepsze rozwiązania w zakresie zabezpieczeń, które poprawiają stan zabezpieczeń aplikacji i organizacji podczas opracowywania na platformie Azure.
keywords: nie dotyczy
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 11bf7c0ae05c2e52d59efb32be47ce6bd96fac4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937976"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Tworzenie bezpiecznej aplikacji dla aplikacji usługi Azure AD
## <a name="overview"></a>Omówienie

Ten przykład jest prosty usługi Azure Active Directory z aplikacją sieci web, która łączy się z zasobami zabezpieczeń do tworzenia aplikacji na platformie Azure. Aplikacja implementuje najlepsze rozwiązania w zakresie zabezpieczeń, które mogą pomóc w ulepszaniu aplikacji i postawy zabezpieczeń organizacji podczas tworzenia aplikacji na platformie Azure.

Skrypty wdrażania skonfigurować infrastrukturę. Po uruchomieniu skryptów wdrażania, należy wykonać pewne ręcznej konfiguracji w witrynie Azure Portal, aby połączyć składniki i usługi razem. Ten przykład jest skierowany do doświadczonych deweloperów na platformie Azure, którzy pracują w branży detalicznej i chcą utworzyć zabezpieczoną usługę Azure Active Directory z bezpieczną infrastrukturą platformy Azure. 


Opracowując i wdrażając tę aplikację, dowiesz się, jak 
- Utwórz wystąpienie usługi Azure Key Vault, przechowuj i pobieraj z niego wpisy tajne.
- Wdrażanie aplikacji Azure Web App, która jest dedykowana izolowane z dostępem do zapory frontona. 
- Tworzenie i konfigurowanie wystąpienia bramy aplikacji platformy Azure za pomocą zapory korzystającej z zestawu reguł OWASP Top 10. 
- Włącz szyfrowanie danych podczas przesyłania i odpoczynku przy użyciu usług platformy Azure. 
- Skonfiguruj zasady platformy Azure i centrum zabezpieczeń, aby ocenić zgodność. 

Po opracowaniu i wdrożeniu tej aplikacji zostanie skonfigurowana następująca przykładowa aplikacja internetowa wraz z opisanymi środkami konfiguracji i zabezpieczeń.

## <a name="architecture"></a>Architektura
Aplikacja jest typową aplikacją n-warstwową z trzema warstwami. Fronton, back end i warstwa bazy danych ze zintegrowanymi komponentami monitorowania i tajnego zarządzania są pokazane w tym miejscu:

![Architektura aplikacji](./media/secure-aad-app/architecture.png)

To rozwiązanie korzysta z następujących usług platformy Azure. Szczegóły architektury wdrażania znajdują się w sekcji Architektura wdrażania. 

Architektura składa się z tych komponentów

- [Brama aplikacji platformy Azure](../../application-gateway/index.yml). Zapewnia bramę i zaporę dla naszej architektury aplikacji.
- [Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md). Zapewnia rozszerzalną usługę zarządzania wydajnością aplikacji (APM) na wielu platformach.
- [Usługa Azure Key Vault](../../key-vault/index.yml). Przechowuje i szyfruje wpisy tajne naszej aplikacji i zarządza tworzeniem zasad dostępu wokół nich.
- [Usługa Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Zapewnia chmurową usługę zarządzania tożsamościami i dostępem, logowanie i dostęp do zasobów.
- [System nazw domen platformy Azure](../../dns/dns-overview.md). Udostępnij usługę hostowania domeny.
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md). Zapewnia skalowanie aplikacji i tworzenie wysokiej dostępności dla usług.
- [Aplikacja Azure Web App](../../app-service/overview.md).  Zapewnia usługę opartą na protokoii HTTP do hostowania aplikacji sieci web.
- [Usługa Azure Security Center](../../security-center/index.yml). zapewnia zaawansowaną ochronę przed zagrożeniami w przypadku obciążeń hybrydowych w chmurze.
- [Zasady platformy Azure](../../governance/policy/overview.md). Zapewnia ocenę zasobów pod kątem niezgodności z przypisanymi zasadami.

## <a name="threat-model"></a>Model zagrożenia
Modelowanie zagrożeń to proces identyfikowania potencjalnych zagrożeń bezpieczeństwa dla firmy i aplikacji, a następnie zapewnienia odpowiedniego planu łagodzenia zmiany klimatu.

W tym przykładzie użyto [narzędzia Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) do zaimplementowania modelowania zagrożeń dla bezpiecznej przykładowej aplikacji. Diagramowanie składników i przepływów danych można zidentyfikować problemy i zagrożenia na wczesnym etapie procesu tworzenia. Czas i pieniądze zostaną zapisane później za pomocą tego.

Oto model zagrożenia dla przykładowej aplikacji

![Model zagrożenia](./media/secure-aad-app/threat-model.png)

Niektóre przykładowe zagrożenia i potencjalne luki w zabezpieczeniach generowane przez narzędzie do modelowania zagrożeń są pokazane na poniższym zrzucie ekranu. Model zagrożenia zawiera przegląd powierzchni ataku narażone i monituje deweloperów, aby zastanowić się, jak złagodzić problemy.

![Wyjście modelu zagrożenia](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Wymagania wstępne
Aby rozpocząć działania aplikacji, należy zainstalować następujące narzędzia:

- Edytor kodu do modyfikowania i wyświetlania kodu aplikacji. [Visual Studio Code](https://code.visualstudio.com/) jest opcją typu open source.
- [Narzędzie CLI platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) na komputerze deweloperskim.
- [Git](https://git-scm.com/) w systemie. Git jest używany do klonowania kodu źródłowego lokalnie.
- [jq](https://stedolan.github.io/jq/), narzędzie unix do wykonywania zapytań JSON w sposób przyjazny dla użytkownika.

Potrzebujesz subskrypcji platformy Azure, aby wdrożyć zasoby przykładowej aplikacji. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto,](https://azure.microsoft.com/free/) aby przetestować przykładową aplikację.

Po zainstalowaniu tych narzędzi można przystąpić do wdrożenia aplikacji na platformie Azure.

### <a name="implementation-guidance"></a>Wytyczne dotyczące wdrażania
Skrypt wdrażania jest jeden skrypt, który można podzielić na cztery fazy. Każda faza wdraża i konfiguruje zasób platformy Azure, który znajduje się na [diagramie architektury.](#architecture)

Cztery fazy są

- Wdrażanie usługi Azure Key Vault.
- Wdrażanie aplikacji sieci Web platformy Azure.
- Wdrażanie bramy aplikacji za pomocą zapory aplikacji sieci Web.
- Konfigurowanie usługi Azure AD za pomocą wdrożonej aplikacji.

Każda faza opiera się na poprzedniej przy użyciu konfiguracji z wcześniej wdrożonych zasobów.

Aby wykonać kroki implementacji, upewnij się, że zainstalowano narzędzia wymienione w obszarze [Wymagania wstępne](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Wdrażanie usługi Azure Key Vault
W tej sekcji należy utworzyć i wdrożyć wystąpienie usługi Azure Key Vault, które jest używane do przechowywania wpisów tajnych i certyfikatów.

Po zakończeniu wdrażania masz wystąpienie usługi Azure Key Vault wdrożone na platformie Azure.

Aby wdrożyć usługę Azure Key Vault przy użyciu programu Powershell
 
1. Zadeklaruj zmienne dla usługi Azure Key Vault.
2. Zarejestruj dostawcę usługi Azure Key Vault.
3. Utwórz grupę zasobów dla wystąpienia.
4. Utwórz wystąpienie usługi Azure Key Vault w grupie zasobów utworzonej w kroku 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>Poniższy użytkownik usługi Azure AD będzie miał uprawnienia administratora do usługi Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Zarejestruj dostawców Az
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Tworzenie wystąpienia usługi Azure Key Vault
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Dodawanie zasad administratora do magazynu kluczy
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Aby utworzyć zasadę dostępu, aby umożliwić użytkownikowi uzyskiwanie i wyświetlanie listy kluczy kryptograficznych, certyfikatów i wpisów tajnych, jeśli znasz nazwę głównego użytkownika:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Najlepszym rozwiązaniem jest używanie tożsamości zarządzanych dla zasobów platformy Azure w aplikacjach korzystających z usługi Key Vault w celu uzyskania dostępu do zasobów. Twoja postawa zabezpieczeń wzrasta, gdy klucze dostępu do usługi Key Vault nie są przechowywane w kodzie ani w konfiguracji.

Certyfikat główny znajduje się w kontenerze. Kroki podjęte w celu uzyskania certyfikatu są

1. Pobierz plik certyfikatu z [urzędu certyfikacji](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Dekodowanie pliku certyfikatu:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Ten skrypt tworzy przypisaną tożsamość dla wystąpienia usługi App Service, które może być używane z msi do interakcji z usługą Azure Key Vault bez twardych wpisów tajnych kodowania w kodzie lub konfiguracji.

Przejdź do wystąpienia usługi Azure Key Vault w portalu, aby autoryzować **Add new access policy**przypisaną tożsamość na karcie zasad dostępu. W obszarze **Wybierz podmiot główny**wyszukaj nazwę aplikacji podobną do nazwy utworzonego wystąpienia usługi App Service.
Podmiot usługi dołączony do aplikacji powinny być widoczne. Wybierz ją i zapisz stronę zasad dostępu, jak pokazano na poniższym zrzucie ekranu.

Ponieważ aplikacja musi tylko pobrać klucze, wybierz **Get** uprawnienia w opcjach wpisów tajnych, umożliwiając dostęp przy jednoczesnym zmniejszeniu przyznanych uprawnień.

![Zasady dostępu do magazynu kluczy](./media/secure-aad-app/kv-access-policy.png)

*Tworzenie zasad dostępu do usługi Key Vault*

Zapisz zasady dostępu, a następnie zapisz nową zmianę na karcie **Zasady dostępu,** aby zaktualizować zasady.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Wdrażanie bramy aplikacji z włączoną zaporą aplikacji sieci Web
W aplikacjach sieci web nie zaleca się ujawniania usług bezpośrednio na zewnątrz w Internecie.
Równoważenie obciążenia i reguły zapory zapewniają większe bezpieczeństwo i kontrolę nad ruchem przychodzącym oraz pomagają w zarządzaniu nim.

Aby wdrożyć wystąpienie bramy aplikacji

1. Utwórz grupę zasobów, aby pomieścić bramę aplikacji.
2. Aprowizuj sieć wirtualną, aby dołączyć do bramy.
3. Utwórz podsieć bramy w sieci wirtualnej.
4. Aprowizuj publiczny adres IP.
5. Aprowizuj bramę aplikacji.
6. Włącz zaporę aplikacji sieci web na bramie.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Wdrażanie aplikacji azure web
Usługa Azure App Service umożliwia tworzenie i hostowania aplikacji sieci web przy użyciu języków, takich jak Python, Ruby, C#i Java. Platforma Azure obsługuje również kontenery niestandardowe, które umożliwiają uruchamianie praktycznie wszystkich języków programowania na platformie Azure App Service.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Tworzenie planu usługi app service w warstwie bezpłatna
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Przed kontynuowaniem przejdź do interfejsu użytkownika konfiguracji systemu nazw domen platformy https://aka.ms/appservicecustomdns Azure dla domeny niestandardowej i postępuj zgodnie z instrukcjami, aby skonfigurować rekord CNAME dla nazwy hosta "www" i wskaż jej domyślną nazwę domeny aplikacji sieci Web

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Uaktualnij plan usługi app service do warstwy udostępnionej (minimum wymagane przez domeny niestandardowe)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Dodawanie niestandardowej nazwy domeny do aplikacji sieci Web
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Wytyczne i zalecenia

### <a name="network"></a>Network (Sieć)
Po zakończeniu wdrażania masz bramę aplikacji z włączoną zaporą aplikacji sieci web.

Wystąpienie bramy udostępnia port 443 dla protokołu HTTPS. Ta konfiguracja zapewnia, że nasza aplikacja jest dostępna tylko na porcie 443 za pośrednictwem protokołu HTTPS.

Blokowanie nieużywanych portów i ograniczanie ekspozycji na powierzchnię ataku jest najlepszym rozwiązaniem w zakresie bezpieczeństwa.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Dodawanie sieciowych grup zabezpieczeń do wystąpienia usługi App Service

Wystąpienia usługi App Service można zintegrować z sieciami wirtualnymi. Ta integracja umożliwia ich skonfigurowanie za pomocą zasad sieciowej grupy zabezpieczeń, które zarządzają ruchem przychodzącym i wychodzącym aplikacji.

1. Aby włączyć tę funkcję, w bloku wystąpienia usługi aplikacji Azure w obszarze **Ustawienia**wybierz pozycję **Sieć**. W prawym okienku skonfiguruj w obszarze **Integracja sieci wirtualnej**.

   ![Nowa integracja sieci wirtualnej](./media/secure-web-app/app-vnet-menu.png)

    *Nowa integracja sieci wirtualnej dla usługi App Service*
1. Na następnej stronie wybierz pozycję **Dodaj wirtualną (podgląd)**.

1. W następnym menu wybierz sieć wirtualną utworzoną `aad-vnet`we wdrożeniu rozpoczynającym się od programu . Można utworzyć nową podsieć lub wybrać istniejącą.
   W takim przypadku należy utworzyć nową podsieć. Ustaw **zakres adresów** na **10.0.3.0/24** i nazwij **podsieć podsieci**.

   ![Konfiguracja sieci wirtualnej usługi App Service](./media/secure-web-app/app-vnet-config.png)

    *Konfiguracja sieci wirtualnej dla usługi App Service*

Po włączeniu integracji z siecią wirtualną możesz dodać sieciowe grupy zabezpieczeń do naszej aplikacji.

1. Użyj pola wyszukiwania, wyszukaj **sieciowe grupy zabezpieczeń**. W wynikach wybierz pozycję **Sieciowe grupy zabezpieczeń.**

    ![Wyszukiwanie grup zabezpieczeń sieci](./media/secure-web-app/nsg-search-menu.png)

    *Wyszukiwanie grup zabezpieczeń sieci*

2. W następnym menu wybierz pozycję **Dodaj**. Wprowadź **nazwę** grupy zasobów i **zasobu,** w której powinna się znajdować. Ta grupa sieciowa zostanie zastosowana do podsieci bramy aplikacji.

    ![Tworzenie nsg](./media/secure-web-app/nsg-create-new.png)

    *Tworzenie nsg*

3. Po utworzeniu strony bliskiejświatła wybierz ją. W jego bloku w obszarze **Ustawienia**wybierz pozycję **Reguły zabezpieczeń przychodzących**. Skonfiguruj te ustawienia, aby umożliwić połączenia przychodzące do bramy aplikacji przez port 443.

   ![Konfigurowanie ndg](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurowanie ndg*

4. W regułach ruchu wychodzącego dla sieciowej sieciowej bramy dodaj regułę zezwalającą na połączenia wychodzące z wystąpieniem usługi App Service, tworząc regułę przeznaczoną dla tagu usługi`AppService`

   ![Dodawanie reguł ruchu wychodzącego dla sieciowej sieciowej sieciowej](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Dodawanie reguł ruchu wychodzącego dla sieciowej sieciowej sieciowej*

    Dodaj inną regułę ruchu wychodzącego, aby umożliwić bramie wysyłanie reguł ruchu wychodzącego do sieci wirtualnej.

   ![Dodawanie innej reguły ruchu wychodzącego](./media/secure-web-app/nsg-outbound-vnet.png)

    *Dodawanie innej reguły ruchu wychodzącego*

5. W bloku podsieci sieciowej sieciowej wybierz pozycję **Skojarz**, wybierz sieć wirtualną utworzoną we wdrożeniu i wybierz podsieć bramy o nazwie **gw-podsieć**. Grupa sieciowa jest stosowana do podsieci.

6. Utwórz inną sieć wg, jak we wcześniejszym kroku, tym razem dla wystąpienia usługi App Service. Nadaj mu nazwę. Dodaj regułę ruchu przychodzącego dla portu 443, tak jak w przypadku sieciowej sieciowej bramy aplikacji.

   Jeśli masz wystąpienie usługi App Service wdrożone w wystąpieniu środowiska usługi app service, co nie ma miejsca dla tej aplikacji, można dodać reguły przychodzące, aby zezwolić na sondy usługi Azure Service Health, otwierając porty 454-455 w przychodzących grupach zabezpieczeń sieciowej usługi app Service. Oto konfiguracja:

   ![Dodawanie reguł dla sond kondycji usługi Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Dodawanie reguł dla sond kondycji usługi Azure (tylko środowisko usługi app service)*

Aby ograniczyć obszar ataku, zmodyfikuj ustawienia sieci usługi App Service, aby zezwolić tylko bramie aplikacji na dostęp do aplikacji.
Aby zastosować ustawienia, przejdź do karty sieci usługi App Service, wybierając kartę **Ograniczenia IP** i tworząc regułę zezwalania, która umożliwia bezpośredni dostęp do usługi tylko na adresie IP bramy aplikacji. Adres IP bramy można pobrać ze strony przeglądu. Na karcie **Adres IP CIDR** wprowadź adres IP `<GATEWAY_IP_ADDRESS>/32`w tym formacie: .

![Zezwalaj tylko na bramę](./media/secure-web-app/app-allow-gw-only.png)

*Zezwalaj tylko adresowi IP bramy na dostęp do usługi App Service*

### <a name="azure-domain-name-system"></a>System nazw domen platformy Azure 
System nazw domen platformy Azure lub system nazw domen platformy Azure jest odpowiedzialny za tłumaczenie (lub rozwiązywanie) nazwy witryny sieci Web lub usługi na jego adres IP. Azure Domain Namehttps://docs.microsoft.com/azure/dns/dns-overview) System( to usługa hostingowa dla domen systemu nazw domen, która zapewnia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostując domeny na platformie Azure, użytkownicy mogą zarządzać rekordami systemu nazw domen przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, jak inne usługi platformy Azure. System nazw domen platformy Azure obsługuje również prywatne domeny systemu nazw domen.

### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption
Usługa Azure Disk Encryption wykorzystuje funkcję funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dysków danych. Rozwiązanie integruje się z usługą Azure Key Vault, aby ułatwić kontrolowanie kluczy szyfrowania dysku i zarządzanie nimi.

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości zarządzania dostępem do danych posiadacza karty w środowisku platformy Azure
- Usługa Azure Active Directory to usługa zarządzania katalogami i tożsamościami opartymi na wielu dzierżawach firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzone w usłudze Azure Active Directory, w tym użytkownicy uzyskujący dostęp do aplikacji Azure WebApp.
- Kontrola dostępu oparta na rolach platformy Azure umożliwia administratorom definiowanie uprawnień dostępu szczegółowej, aby udzielić tylko ilości dostępu, której użytkownicy potrzebują do wykonywania swoich zadań. Zamiast udzielać każdemu użytkownikowi nieograniczonych uprawnień do zasobów platformy Azure, administratorzy mogą zezwalać tylko na niektóre akcje dostępu do danych posiadacza karty. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- Zarządzanie tożsamościami uprzywilejowanymi usługi Azure Active Directory umożliwia klientom zminimalizowanie liczby użytkowników, którzy mają dostęp do określonych informacji, takich jak dane posiadacza karty. Administratorzy mogą używać zarządzania tożsamościami uprzywilejowanymi usługi Azure Active Directory do odnajdywanie, ograniczanie i monitorowanie tożsamości uprzywilejowanych i ich dostępu do zasobów. Ta funkcja może również służyć do wymuszania dostępu administracyjnego na żądanie, just-in-time w razie potrzeby.
- Usługa Azure Active Directory Identity Protection wykrywa potencjalne luki w zabezpieczeniach wpływających na tożsamość organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami organizacji i bada podejrzane incydentów w celu podjęcia odpowiednich działań w celu ich rozwiązania.
### <a name="secrets-management"></a>Zarządzanie wpisami tajnymi
Rozwiązanie używa usługi Azure Key Vault do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Poniższe funkcje usługi Azure Key Vault pomagają klientom chronić i uzyskiwać do nich dostęp
   - Zaawansowane zasady dostępu są konfigurowane na podstawie potrzeb.
   - Zasady dostępu usługi Key Vault są definiowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
   - Wszystkie klucze i wpisy tajne w magazynie kluczy mają daty wygaśnięcia.
   - Wszystkie klucze w ucho. Typ klucza to sprzętowy moduł zabezpieczeń (HSM) Chroniony 2048-bitowy klucz RSA.
   - Za pomocą usługi Key Vault można szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, . PFX i hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM). 
   - Użyj kontroli dostępu opartej na rolach (RBAC), aby przypisać uprawnienia użytkownikom, grupom i aplikacjom w określonym zakresie.     
   - Usługa Key Vault służy do zarządzania certyfikatami TLS za pomocą funkcji autorenewal. 
   - Dzienniki diagnostyczne dla usługi Key Vault są włączone z okresem przechowywania co najmniej 365 dni.
   - Dozwolone operacje kryptograficzne kluczy są ograniczone do wymaganych operacji.
### <a name="azure-security-center"></a>Azure Security Center
Dzięki usłudze Azure Security Center klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w różnych obciążeniach, ograniczać narażenie na zagrożenia oraz wykrywać ataki i reagować na nie. Dodatkowo 
   - Usługa Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia zaleceń dotyczących konfiguracji i usług w celu poprawy stanu bezpieczeństwa i ochrony danych.
   - Usługa Azure Security Center używa różnych funkcji wykrywania, aby ostrzegać klientów o potencjalnych atakach ukierunkowanych na ich środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Azure Security Center ma zestaw wstępnie zdefiniowanych alertów zabezpieczeń, które są wyzwalane, gdy zagrożenie lub podejrzana aktywność ma miejsce. Niestandardowe reguły alertów w usłudze Azure Security Center umożliwiają klientom definiowanie nowych alertów zabezpieczeń na podstawie danych, które są już zbierane z ich środowiska.
   - Usługa Azure Security Center udostępnia priorytetowe alerty zabezpieczeń i zdarzenia, co ułatwia klientom wykrywanie i rozwiązywanie potencjalnych problemów z zabezpieczeniami. Raport analizy zagrożeń jest generowany dla każdego wykrytego zagrożenia, aby pomóc zespołom reagowania na incydenty w badaniu i korygowaniu zagrożeń.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   Architektura zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji platformy Azure z skonfigurowaną zaporą aplikacji sieci web i włączonym zestawem reguł OWASP. Dodatkowe możliwości obejmują
   - End-to-end-SSL.
   - Wyłącz TLS v1.0 i v1.1.
   - Włącz TLSv1.2.
   - Zapora aplikacji sieci Web (tryb zapobiegania).
   - Tryb zapobiegania z owasp 3.0 ruleset.
   - Włącz rejestrowanie diagnostyki.
   - Niestandardowe sondy kondycji.
   - Usługa Azure Security Center i doradca platformy Azure zapewniają dodatkową ochronę i powiadomienia. Usługa Azure Security Center zapewnia również system reputacji.
### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji
Usługi platformy Azure szeroko rejestrują działania systemu i użytkownika, a także kondycję systemu:
   - Dzienniki aktywności: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w określeniu inicjatora operacji, czasu wystąpienia i stanu.
   - Dzienniki diagnostyczne: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zawierają wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji usługi Key Vault oraz dostęp do bramy aplikacji i dzienniki zapory. Wszystkie dzienniki diagnostyczne zapisują się na scentralizowanym i zaszyfrowanym koncie magazynu platformy Azure w celu archiwizacji. Przechowywanie jest konfigurowane przez użytkownika, do 730 dni, aby spełnić wymagania dotyczące przechowywania specyficzne dla organizacji.
### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor
   Te dzienniki są konsolidowane w [dziennikach usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowania pulpitu nawigacyjnego. Po zebraniu dane są zorganizowane w osobne tabele dla każdego typu danych w obszarach roboczych usługi Log Analytics, co umożliwia analizowanie wszystkich danych razem niezależnie od ich oryginalnego źródła. Ponadto usługa Azure Security Center integruje się z dziennikami usługi Azure Monitor, umożliwiając klientom korzystanie z zapytań Kusto w celu uzyskania dostępu do danych zdarzeń zabezpieczeń i łączenia ich z danymi z innych usług.

   Następujące [rozwiązania do monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) platformy Azure są uwzględniane jako część tej architektury

   - [Ocena usługi Active Directory:](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)Rozwiązanie do sprawdzania kondycji usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
   - [Kondycja agenta:](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)Rozwiązanie kondycji agenta raportuje liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, którzy nie reagują i liczbę agentów, którzy przesyłają dane operacyjne.
   - [Analiza dzienników aktywności:](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)Rozwiązanie analizy dzienników aktywności pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.
### <a name="azure-monitor"></a>Azure Monitor
   [Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)pomaga użytkownikom śledzić wydajność, utrzymywać zabezpieczenia i identyfikować trendy, umożliwiając organizacjom inspekcję, tworzenie alertów i archiwizowanie danych, w tym śledzenie wywołań interfejsu API w swoich zasobach platformy Azure.
### <a name="application-insights"></a>Application Insights 
   [Usługa Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) to rozszerzalna usługa zarządzania wydajnością aplikacji dla deweloperów sieci web na wielu platformach. Usługa Application Insights wykrywa anomalie wydajności, a klienci mogą go używać do monitorowania aplikacji sieci web na żywo. Zawiera zaawansowane narzędzia analityczne, które pomagają klientom diagnozować problemy i zrozumieć, co użytkownicy faktycznie robią ze swoją aplikacją. Został zaprojektowany, aby pomóc klientom stale poprawiać wydajność i użyteczność.

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault
   Tworzenie magazynu dla organizacji, w którym mają być przechowywane klucze, oraz utrzymywanie odpowiedzialności za zadania operacyjne, takie jak poniżej

   - Dane przechowywane w magazynie kluczy obejmują   
   - Klucz wglądu w aplikacje
   - Klucz dostępu do magazynu danych
   - Parametry połączenia
   - Nazwa tabeli danych
   - Poświadczenia użytkownika
   - Zaawansowane zasady dostępu są konfigurowane na podstawie potrzeb
   - Zasady dostępu usługi Key Vault są definiowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych
   - Wszystkie klucze i wpisy tajne w magazynie kluczy mają daty wygaśnięcia
   - Wszystkie klucze w magazynie kluczy są chronione sprzętowym modułem zabezpieczeń (HSM) [Typ klucza = sprzętowy moduł zabezpieczeń (HSM) Protected       
     2048-bitowy klawisz RSA]
   - Wszyscy użytkownicy/tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu kontroli dostępu opartej na rolach (RBAC)
   - Aplikacje nie udostępniają usługi Key Vault, chyba że ufają sobie nawzajem i potrzebują dostępu do tych samych wpisów tajnych w czasie wykonywania
   - Dzienniki diagnostyczne dla usługi Key Vault są włączone z okresem przechowywania co najmniej 365 dni.
   - Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych wymaganych

### <a name="vpn-and-expressroute"></a>SIEĆ VPN i usługa ExpressRoute
   Bezpieczny tunel sieci VPN lub [usługa ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowana przez bezpieczne nawiązanie połączenia z zasobami wdrożonymi jako część tej architektury referencyjnej aplikacji sieci Web PaaS. Poprzez odpowiednie skonfigurowanie sieci VPN lub usługi ExpressRoute klienci mogą dodać warstwę ochrony danych podczas przesyłania.

   Implementując bezpieczny tunel sieci VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a siecią wirtualną platformy Azure. To połączenie odbywa się przez Internet i umożliwia klientom bezpieczne "tunelowanie" informacji wewnątrz zaszyfrowanego łącza między siecią klienta a platformą Azure. Sieć VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która jest wdrażana przez przedsiębiorstwa każdej wielkości od dziesięcioleci. Tryb tunelu IPsec jest używany w tej opcji jako mechanizm szyfrowania.

   Ponieważ ruch w tunelu sieci VPN przechodzi przez Internet za pomocą sieci VPN typu lokacja lokacja, firma Microsoft oferuje inną, jeszcze bezpieczniejszą opcję połączenia. Usługa Azure ExpressRoute to dedykowane łącze sieci WAN między platformą Azure a lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ połączenia usługi ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, szybsze prędkości, mniejsze opóźnienia i wyższe zabezpieczenia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie dostawcy usług telekomunikacyjnych klienta, dane nie są przesyłane przez Internet, a zatem nie są na nie narażone.

   [Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najważniejsze wskazówki dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

#### <a name="implement-azure-active-directory-oidc"></a>Implementowanie oidc usługi Azure Active Directory

1. Aby sklonować repozytorium kodu źródłowego, użyj tego polecenia Git

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Aktualizowanie adresów URL przekierowań
1.  Przejdź z powrotem do witryny Azure portal. W lewym okienku nawigacji wybierz usługę Azure Active Directory, a następnie wybierz pozycję Rejestracje aplikacji.
2.  Na ekranie wynikowym wybierz aplikację WebApp-OpenIDConnect-DotNet-code-v2.
3.  Na karcie Uwierzytelnianie o W sekcji Przekierowanie identyfikatorów URI wybierz pozycję Web w polu kombi i dodaj następujące identyfikatory URI przekierowania.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.nethttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o W sekcji Ustawienia zaawansowane ustaw adres URL wylogowania nahttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  Na karcie Znakowanie o Aktualizowanie adresu URL strony głównej na https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.netadres usługi aplikacji, na przykład .
        o Zapisz konfigurację.
5.  Jeśli aplikacja wywołuje interfejs API sieci web, upewnij się, aby zastosować niezbędne zmiany w projekcie appsettings.json, więc wywołuje opublikowany adres URL interfejsu API zamiast localhost.
Publikowanie próbki
    1.  Na karcie Przegląd usługi App Service pobierz profil publikowania, klikając łącze Uzyskaj profil publikowania i zapisz go. Można również użyć innych mechanizmów wdrażania, takich jak kontrola źródła.
    2.  Przełącz się do programu Visual Studio i przejdź do projektu WebApp-OpenIDConnect-DotNet-code-v2. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję Publikuj. Kliknij pozycję Zaimportuj profil na dolnym pasku i zaimportuj wcześniej pobrany profil publikowania.
    3.  Kliknij przycisk Konfiguruj i na karcie Połączenie zaktualizuj docelowy adres URL, aby był to adres URL strony głównej, na przykład https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net. Kliknij przycisk Dalej.
    4.  Na karcie Ustawienia upewnij się, że opcja Włącz uwierzytelnianie organizacyjne NIE jest zaznaczona. Kliknij pozycję Zapisz. Kliknij przycisk Publikuj na ekranie głównym.
    5.  Visual Studio opublikuje projekt i automatycznie otworzy przeglądarkę do adresu URL projektu. Jeśli zostanie wyświetlona domyślna strona internetowa projektu, publikacja zakończyła się pomyślnie.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementowanie uwierzytelniania wieloskładnikowego dla usługi Azure Active Directory
   Administratorzy muszą upewnić się, że konta subskrypcji w portalu są chronione. Subskrypcja jest narażona na ataki, ponieważ zarządza utworzonymi zasobami. Aby chronić subskrypcję, włącz uwierzytelnianie wieloskładnikowe na karcie **Usługi Azure Active Directory** w ramach subskrypcji.

   Usługa Azure AD działa na podstawie zasad, które są stosowane do użytkownika lub grup użytkowników, które pasują do określonych kryteriów.
Platforma Azure tworzy domyślną zasadę określającą, że administratorzy potrzebują uwierzytelniania dwuskładnikowego, aby zalogować się do portalu.
Po włączeniu tej zasady może zostać wyświetlony monit o wylogowanie się i zalogowanie się z powrotem do witryny Azure portal.

Aby włączyć usługę MFA dla logowania administratora

   1. Przejdź do karty **Usługi Azure Active Directory** w witrynie Azure portal
   2. W kategorii zabezpieczeń wybierz dostęp warunkowy. Zobaczysz ten ekran

       ![Dostęp warunkowy — zasady](./media/secure-aad-app/ad-mfa-conditional-add.png)

Jeśli nie możesz utworzyć nowej zasady

   1. Przejdź do karty **usługi MFA.**
   2. Wybierz bezpłatne łącze próbne usługi Azure AD **Premium,** aby zasubskrybować bezpłatną wersję próbną.

   ![Bezpłatna wersja próbna usługi Azure AD Premium](./media/secure-aad-app/ad-trial-premium.png)

Powrót do ekranu dostępu warunkowego.

   1. Wybierz nową kartę zasad.
   2. Wprowadź nazwę zasad.
   3. Wybierz użytkowników lub grupy, dla których chcesz włączyć usługę MFA.
   4. W obszarze **Formanty dostępu**wybierz kartę **Przyznanie,** a następnie wybierz pozycję **Wymagaj uwierzytelniania wieloskładnikowego** (i innych ustawień).

   ![Wymaganie usługi MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   Zasady można włączyć, zaznaczając pole wyboru u góry ekranu lub na karcie **Dostęp warunkowy.** Gdy zasada jest włączona, użytkownicy potrzebują usługi MFA, aby zalogować się do portalu.

   Istnieje zasada linii bazowej, która wymaga usługi MFA dla wszystkich administratorów platformy Azure. Można go włączyć natychmiast w portalu. Włączenie tych zasad może unieważnić bieżącą sesję i wymusić ponowne zalogowanie się.

   Jeśli zasada bazowa nie jest włączona
   1.   Wybierz pozycję **Wymagaj usługi MFA dla administratorów**.
   2.   Natychmiast wybierz pozycję **Użyj zasad**.

   ![Natychmiast wybierz pozycję Użyj zasad](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Monitorowanie aplikacji i zasobów za pomocą usługi Azure Sentinel

   W miarę rozwoju aplikacji staje się trudne do agregowania wszystkich sygnałów zabezpieczeń i metryki otrzymane z zasobów i uczynić je przydatne w sposób zorientowany na akcję.

   Usługa Azure Sentinel jest przeznaczona do zbierania danych, wykrywania typów zagrożeń i zapewniania wglądu w zdarzenia związane z zabezpieczeniami.
Podczas oczekiwania na ręczną interwencję usługa Azure Sentinel może polegać na wstępnie napisanych podręcznikach, aby rozpocząć alerty i procesy zarządzania incydentami.

   Przykładowa aplikacja składa się z kilku zasobów, które usługa Azure Sentinel może monitorować.
Aby skonfigurować usługę Azure Sentinel, należy najpierw utworzyć obszar roboczy usługi Log Analytics, który przechowuje wszystkie dane zebrane z różnych zasobów.

Aby utworzyć ten obszar roboczy

   1. W polu wyszukiwania w witrynie Azure portal wyszukaj **usługę Log Analytics**. Wybierz **obszary robocze usługi Log Analytics**.

   ![Wyszukiwanie obszarów roboczych usługi Log Analytics](./media/secure-aad-app/sentinel-log-analytics.png)

   *Wyszukiwanie obszarów roboczych usługi Log Analytics*

   2. Na następnej stronie wybierz pozycję **Dodaj,** a następnie podaj nazwę, grupę zasobów i lokalizację obszaru roboczego.
   ![Tworzenie obszaru roboczego usługi Log Analytics](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Tworzenie obszaru roboczego usługi Log Analytics*

   3. Użyj pola wyszukiwania, aby wyszukać **usługę Azure Sentinel**.

   ![Wyszukiwanie ciągu „Azure Sentinel”](./media/secure-aad-app/sentinel-add.png)

   *Wyszukiwanie ciągu „Azure Sentinel”*

   4. Wybierz **pozycję Dodaj,** a następnie wybierz utworzony wcześniej obszar roboczy usługi Log Analytics.

   ![Dodawanie obszaru roboczego usługi Log Analytics](./media/secure-aad-app/sentinel-workspace-add.png)

   *Dodawanie obszaru roboczego usługi Log Analytics*

   5. Na stronie **Azure Sentinel — łączniki danych** w obszarze **Konfiguracja**wybierz pozycję **Łączniki danych**. Zobaczysz tablicę usług platformy Azure, które można połączyć z wystąpieniem magazynu usługi Log Analytics do analizy w usłudze Azure Sentinel.

   ![Łączniki danych usługi Log Analytics](./media/secure-aad-app/sentinel-connectors.png)

      *Dodawanie łącznika danych do usługi Azure Sentinel*

   Na przykład, aby połączyć bramę aplikacji, wykonaj następujące kroki:

   1. Otwórz blok wystąpienia bramy aplikacji platformy Azure.
   2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
   3. Wybierz **pozycję Dodaj ustawienie diagnostyczne**.

   ![Dodawanie diagnostyki bramy aplikacji](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Dodawanie diagnostyki bramy aplikacji*

   4. Na stronie **Ustawienia diagnostyczne** wybierz utworzony obszar roboczy usługi Log Analytics, a następnie wybierz wszystkie metryki, które chcesz zebrać i wysłać do usługi Azure Sentinel. Wybierz **pozycję Zapisz**.

   ![Ustawienia łącznika usługi Azure Sentinel](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Kwestie związane z kosztami
   Jeśli nie masz jeszcze konta platformy Azure, możesz utworzyć bezpłatne. Przejdź do [strony bezpłatnego konta,](https://azure.microsoft.com/free/) aby rozpocząć, zobacz, co możesz zrobić z bezpłatnym kontem platformy Azure i dowiedz się, które produkty są bezpłatne przez 12 miesięcy.

   Aby wdrożyć zasoby w przykładowej aplikacji z funkcjami zabezpieczeń, należy zapłacić za niektóre funkcje premium. Ponieważ aplikacja jest skalowana, a bezpłatne warstwy i wersje próbne oferowane przez platformę Azure muszą zostać uaktualnione w celu spełnienia wymagań aplikacji, koszty mogą wzrosnąć. Kalkulator [cen](https://azure.microsoft.com/pricing/calculator/) platformy Azure służy do szacowania kosztów.

## <a name="next-steps"></a>Następne kroki
   Poniższe artykuły mogą pomóc w projektowaniu, opracowywaniu i wdrażaniu bezpiecznych aplikacji.

- [Projekt](secure-design.md)
- [Opracowywanie](secure-develop.md)
- [Wdróż](secure-deploy.md)
