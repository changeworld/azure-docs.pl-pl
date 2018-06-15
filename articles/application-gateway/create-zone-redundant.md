---
title: Utworzyć strefy bramy nadmiarowe aplikacji Azure
description: Dowiedz się, jak utworzyć bramy aplikacji nadmiarowe strefy, która nie wymaga bramy seperarte w każdej strefie.
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937756"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>Tworzenie strefy bramy nadmiarowe aplikacji Azure — prywatnej wersji zapoznawczej

Platforma nadmiarowe strefy bramy aplikacji jest nowe jednostki SKU, które oferuje wiele udoskonaleń w stosunku do istniejącej aplikacji bramy jednostki SKU w tym:
- **Strefy odporności** — wdrożenia bramy aplikacji teraz może obejmować wiele stref dostępności, eliminuje konieczność obsługi administracyjnej i numeru pin brama aplikacji w osobnych wystąpień w każdej strefie z Menedżera ruchu. Można wybrać pojedynczej strefy lub stref wdrożonym wystąpień bramy aplikacji, w związku z tym zapewnienie odporności awarii strefy. Puli wewnętrznej bazy danych dla aplikacji mogą być dystrybuowane podobnie różnych strefach dostępności.
- **Ulepszenia wydajności**
- **Statyczny adres VIP** -wyłącznie bramy aplikacji VIP statycznego typu VIP obsługuje teraz. Dzięki temu, że wirtualne adresy IP skojarzone z bramy aplikacji nie zmieniają się po ponownym uruchomieniu.
- **Integracja magazynu kluczy dla certyfikatów SSL klienta**
- **Skrócić czas wdrażania i aktualizacji**

> [!NOTE]
> Brama aplikacji w nadmiarowe strefy jest obecnie w podglądzie prywatnym. Ta wersja zapoznawcza jest dostarczany bez umowy dotyczącej poziomu usług i nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane lub mogą mieć ograniczone możliwości. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Obsługiwane regiony

Bramy aplikacji nadmiarowe strefy są obecnie obsługiwane w regionie wschodnie stany USA 2. Wkrótce zostaną dodane kolejnych regionach.

## <a name="topologies"></a>Topologie

W bieżącej wersji nie jest już konieczne utworzenie strefy przypięty bramy aplikacji można uzyskać zonal nadmiarowości. Tego samego wdrożenia bramy aplikacji teraz może obejmować wiele stref.

Co najmniej trzy wystąpienia są wymagane, aby upewnić się, że są rozkładane wszystkie trzy strefy. Brama aplikacji w dystrybuuje wystąpień różnych strefach miarę dodawania więcej wystąpień.

Poprzednie topologie nadmiarowe strefy wyszukiwanego, podobnie jak na poniższym diagramie:

![Stary topologii geograficznie nadmiarowy](media/create-zone-redundant/old-redundant.png)

Nową strefę topologię nadmiarowe wygląda ten diagram:

![Nową strefę topologię geograficznie nadmiarowy](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>Wdrożenie

### <a name="prerequisites"></a>Wymagania wstępne

Obecnie strefy nadmiarowe funkcja jest dostępna tylko w prywatnej wersji zapoznawczej. Musi być e-mail appgwxzone@microsoft.com być białej. Po otrzymaniu potwierdzenia, możesz przejść do następnych kroków. Podaj następujące informacje w wiadomości e-mail, aby uzyskać listę dozwolonych podobnej:

- Identyfikator subskrypcji
- Nazwa regionu
- Przybliżonej liczby wymaganych bram aplikacji

### <a name="resource-manager-template-deployment"></a>Wdrożenie szablonu usługi Resource Manager

1. Upewnij się, że białej wcześniej wymienionymi jest subskrypcję, której używasz.
2. Po otrzymaniu potwierdzenia, zaloguj się do konta platformy Azure i wybierz odpowiednią subskrypcję, jeśli występuje więcej niż jedną subskrypcję. Upewnij się, że Wybierz subskrypcję, która została białej.

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. Utworzenie nowej grupy zasobów

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. Pobieranie z [GitHub](https://github.com/amitsriva/CrossZonePreview) i zwróć uwagę na folder, w którym je zapisać.
5. Utwórz nowe wdrożenie w grupie zasobów, który został utworzony. Przed wdrożeniem odpowiednio zmodyfikuj plik szablonu i parametry. 

   Na poniższym diagramie przedstawiono gdy można pobrać Identyfikatora dzierżawy w portalu Azure:

   ![Identyfikator dzierżawcy z portalu](media/create-zone-redundant/tenant-id.png)

Szablon tworzy następujące zasoby:

- **Przypisane tożsamości użytkownika** — służy do włączania wystąpień bramy aplikacji dostępu do magazynu kluczy i pobierać certyfikaty przechowywane przez użytkownika.
- **KeyVault** — Key Vault przechowywania certyfikatu użytkownika. Może to być również istniejące magazyn kluczy.
- **Klucz tajny** — klucza prywatnego, który jest przechowywany w magazynie kluczy.
- **Zasady dostępu** — zasada dostępu stosowane na magazyn kluczy, którego udziela uprawnień za pomocą przypisane tożsamości użytkownika, dzięki czemu wdrożenia bramy aplikacji można pobrać certyfikatów użytkowników.
- **Publiczny adres IP** — zastrzeżony adres IP, który umożliwia dostęp do bramy aplikacji. Ten adres IP nigdy nie zmienia cyklem życia aplikacji bramy.
- **Sieciowe grupy zabezpieczeń** — moduł NSG automatycznie utworzone w podsieci bramy aplikacji, którego kliknięcie spowoduje otwarcie portu ruchu na skonfigurowany odbiornik. To jest jawnie tworzenia i zarządzania nimi w nowej jednostce SKU w porównaniu do poprzednich jednostka SKU, których ta grupa NSG zakończyła niejawne.
- **Sieć wirtualna** — sieci wirtualnej, w którym wdrożono bramę aplikacji i aplikacji.
- **Brama aplikacji w** — tworzy bramę aplikacji z wystąpieniami w strefie wymagane. Domyślnie wybrane są wszystkie strefy (1,2,3). Nazwa jednostki SKU jest zmieniana na *Standard_v2*. Jest to nazwa jednostki SKU może ulec zmianie. Obecnie Konfiguracja skalowania automatycznego zawiera min i max ustaw żądaną liczbę wystąpień. Po włączeniu Skalowanie automatyczne, to można zmienić.

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

1. Upewnij się, że z subskrypcją użytą jest białej wcześniej wymienionymi w wymaganiach wstępnych.
2. Pobierz i zainstaluj prywatnej MSI programu PowerShell z [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi).
3. Pobierz prywatnego plik zip programu PowerShell z lokalizacji wymienionych w wersji zapoznawczej wiadomość e-mail z potwierdzeniem rejestracji. Rozpakuj plik na dysku i zanotuj lokalizację.
4. Po włączeniu podglądu ładowanie modułów Podgląd najpierw przed zalogować się do swojego konta:

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. Zaloguj się do konta platformy Azure i wybierz odpowiednią subskrypcję, jeśli występuje więcej niż jedną subskrypcję. Upewnij się, że wybierz odpowiednią subskrypcję, która została białej.
5. Uruchom następujące polecenia, aby ustanowić stałe wspólne, obejmujących nazwy dla większości jednostek tworzona. 

   Zmodyfikuj wpisów, co jest wymagane dla z preferencjami użytkownika.

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. Utwórz grupę zasobów:

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. Utwórz przypisane tożsamości użytkownika używane w celu udzielenia dostępu do bramy aplikacji można pobrać certyfikaty z magazynu kluczy.

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. Utwórz magazyn kluczy, używany do przechowywania certyfikatów:

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. Przekaż certyfikat do magazynu kluczy jako klucz tajny:

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. Przypisać zasady dostępu do magazynu kluczy przy użyciu tożsamości użytkownika przypisane. Dzięki temu wystąpień bramy aplikacji można uzyskać dostęp do magazynu klucz tajny:

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. Utwórz grupy zabezpieczeń sieci (NSG) umożliwia dostęp do podsieci bramy aplikacji na portach, w których są tworzone nowe odbiorników.

    Na przykład dla protokołu HTTP/HTTPS na domyślnych portów grupa NSG umożliwia dostęp przychodzący do 80, 443 i 65200 – 65535 dla operacji zarządzania.

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. Tworzenie sieci wirtualnej i podsieci:

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. Utwórz publiczny adres IP zarezerwowany typ/statycznych:

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. Utwórz bramę aplikacji:

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. Pobierz publiczny adres IP bramy aplikacji utworzone:

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

-  Zostanie I opłaty będą naliczane za brama aplikacji w wersji zapoznawczej?

   Podczas (wersja zapoznawcza) nie jest bez dodatkowych opłat. Będzie rozliczane zasobów niż bramy aplikacji, takich jak usługi Key Vault, maszyny wirtualne itp.
- Jakich regionach jest dostępna w wersji zapoznawczej?

   Podgląd jest obecnie dostępna w regionie wschodnie stany USA 2. Wkrótce zostaną dodane kolejnych regionach.
- Portalu jest obsługiwana w wersji zapoznawczej?

   Nie, obsługa jest ograniczona do prywatnego moduł programu PowerShell i szablonu usługi Resource Manager w prywatnej wersji zapoznawczej.

- Obciążeń produkcyjnych jest obsługiwana w prywatnej wersji zapoznawczej?

   Nie, Brak umowy dotyczącej poziomu usług i pomocy technicznej w prywatnej wersji zapoznawczej. Nie zaleca się zawiesić obciążeń produkcyjnych podczas podglądu. Obsługa jest ograniczona do bezpośredniej interakcji z grupy produktów za pomocą aliasów e-mail do podglądu.

- Jak zgłosić problemy?

   Prywatnej wersji zapoznawczej może zawierać usterki i może mieć wdrożeń częste kodu. Użyj aliasu Obsługa appgwxzone@microsoft.com raportowania problemy i uzyskać pomoc.

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia


|Problem  |Szczegóły  |
|---------|---------|---------|
|Rozliczenia     |Obecnie nie rozliczeń|
|Dzienniki diagnostyczne (nie metryki)     |Wydajność i Dzienniki żądań i odpowiedzi nie są wyświetlane aktualnie|
|Portal/interfejsu wiersza polecenia/pakiet SDK     |Brak obsługi dla portalu, interfejsu wiersza polecenia lub zestawu SDK. Nie należy portalu wydania aktualizacji, aby wyświetlić podgląd bram.|
|Czasami niepowodzenia aktualizacji za pomocą szablonu     |Jest to spowodowane wyścigu z KeyVault zasad dostępu|Po utworzeniu magazynu kluczy i przypisać tożsamość użytkownika może być usunięte z szablonu i wymagane są tylko odwołania do klucza tajnego i tożsamości w szablonie.|
|Skalowanie automatyczne     |Nie obsługuje obecnie Skalowanie automatyczne|
|Zapora aplikacji sieci Web     |Obecnie nie jest obsługiwana zapory aplikacji sieci Web|
|Certyfikaty i dynamicznych adresów VIP dostarczone przez użytkownika     |Te nie są obsługiwane w nowego modelu. Magazyn kluczy używana do przechowywania certyfikatów i statyczne adresy VIP.|
|Tej samej podsieci dla starego i brama aplikacji w wersji zapoznawczej     |Nie można użyć podsieci z istniejącą bramę aplikacji (stary model) dla wersji prywatnej wersji zapoznawczej.|
|HTTP/2, aplikacje sieci Web Azure protokołu WebSocket, trybie FIPS jako wewnętrznej bazy danych     |Obecnie nieobsługiwane |


## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Aby uzyskać pomoc techniczna i opinie, skontaktuj się z do appgwxzone@microsoft.com. Grupa produktów bramy aplikacji jest chętnie Zapoznamy swoją opinię do ulepszenia oraz wskazówki gdy jest to wymagane.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat innych funkcji bramy aplikacji:

- [Co to jest brama aplikacji w usłudze Azure?](overview.md)