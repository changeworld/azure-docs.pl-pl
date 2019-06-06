---
title: Migrowanie z usługi Azure Application Gateway i zapory aplikacji z v1 na v2
description: W tym artykule dowiesz się, jak przeprowadzić migrację usługi Azure Application Gateway i zapory aplikacji sieci Web z v1 na v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 44d5ce3e194c873a564039934f518cb3a0e142e3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497174"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrowanie z usługi Azure Application Gateway i zapory aplikacji z v1 na v2

[Usługa Azure Application Gateway i zapory aplikacji sieci Web (WAF) w wersji 2](application-gateway-autoscaling-zone-redundant.md) jest teraz dostępna, oferuje dodatkowe funkcje, takie jak skalowanie automatyczne i strefy dostępności nadmiarowości. Jednak istniejących bram z v1 nie są automatycznie uaktualnione do wersji 2. Jeśli chcesz przeprowadzić migrację z v1 na v2, wykonaj kroki opisane w tym artykule.

W przypadku migracji, istnieją dwa etapy:

1. Migracja konfiguracji
2. Migrowanie ruchu klientów

W tym artykule opisano migrację konfiguracji. Migracja danych klienta zależy od określonego środowiska. Jednak niektóre ogólny, ogólne zalecenia dotyczące [znajdują się](#migrate-client-traffic).

## <a name="migration-overview"></a>Migrowanie — omówienie

Skrypt programu Azure PowerShell jest dostępny, wykonuje następujące czynności:

* Tworzy nową bramę Standard_v2 lub WAF_v2 w podsieci sieci wirtualnej, który określisz.
* Bezproblemowo kopiuje konfiguracji skojarzone z bramy Standard lub zapory aplikacji sieci Web w wersji 1 do nowo utworzonej bramie Standard_V2 lub WAF_V2.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Nowa brama v2 ma nowe publiczne i prywatne adresy IP. Nie można przenieść adresy IP skojarzone z istniejącą bramą v1 bezproblemowo do wersji 2. Jednakże możesz przydzielić istniejącego (nieprzydzielone) publicznego lub prywatnego adresu IP do nowej bramy w wersji 2.
* Musisz podać przestrzeń adresów IP w innej podsieci w ramach sieci wirtualnej, w którym znajduje się brama v1. Skrypt nie można utworzyć bramy v2 w istniejących podsieci, które mają już bramy v1. Jednak jeśli już istniejącą podsieć bramy w wersji 2, mogą nadal działać pod warunkiem, że jest wystarczająco dużo przestrzeni adresów IP.
* Aby przeprowadzić migrację konfiguracji protokołu SSL, należy określić certyfikatów SSL używany w bramie v1.
* Jeśli masz włączony dla bramy V1 tryb ze standardem FIPS, nie będzie można migrować do nowej bramy w wersji 2. Tryb FIPS nie jest obsługiwana w wersji 2.
* w wersji 2 nie obsługuje protokołu IPv6, dlatego bram v1 włączony protokół IPv6 nie są migrowane. Jeśli skrypt zostanie uruchomiony, może nie zakończyć.
* Jeśli brama v1 zawiera tylko prywatny adres IP, skrypt tworzy publiczny adres IP i prywatnego adresu IP do nowej bramy w wersji 2. bram w wersji 2 nie obsługują obecnie tylko prywatnych adresów IP.

## <a name="download-the-script"></a>Pobierz skrypt

Pobierz skrypt migracji z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Użyj skryptu

Istnieją dwie opcje dla Ciebie w zależności od preferencji i konfigurowanie lokalnego środowiska PowerShell:

* Jeśli nie masz zainstalowane moduły Az platformy Azure lub nie mieć nic przeciwko, odinstalowywanie modułów Azure Az, najlepszym rozwiązaniem jest użycie `Install-Script` opcję, aby uruchomić skrypt.
* Jeśli musisz zachować modułów Azure Az usługi najlepiej pobrać skrypt i uruchom go bezpośrednio.

Aby ustalić, czy masz zainstalowane moduły Az platformy Azure, uruchom `Get-InstalledModule -Name az`. Jeśli nie widzisz żadnych zainstalowanych modułów Az, a następnie można użyć `Install-Script` metody.

### <a name="install-using-the-install-script-method"></a>Instalowanie przy użyciu metody Install-Script

Aby użyć tej opcji, nie może mieć modułów Azure Az zainstalowana na danym komputerze. Jeśli są one zainstalowane, następujące polecenie wyświetla komunikat o błędzie. Możesz odinstalować modułów Azure Az lub użyj innych opcji, aby ręcznie pobrać skrypt i uruchom go.
  
Uruchom skrypt za pomocą następującego polecenia:

`Install-Script -Name AzureAppGWMigration`

To polecenie instaluje także wymagane moduły Az.  

### <a name="install-using-the-script-directly"></a>Zainstaluj bezpośrednio za pomocą skryptu

Jeśli masz niektórych modułów Azure Az zainstalowany i nie można odinstalować je (lub nie chcesz je odinstalować), możesz ręcznie pobrać skrypt, używając **pobieranie ręczne** karcie link pobierania skryptu. Skrypt jest pobierana jako plik raw nupkg. Aby zainstalować skrypt z tego pliku nupkg, zobacz [Pobieranie pakietu ręczne](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download).

Aby uruchomić skrypt:

1. Użyj `Connect-AzAccount` do połączenia z platformą Azure.

1. Użyj `Import-Module Az` Aby zaimportować moduły Az.

1. Uruchom `Get-Help AzureAppGWMigration.ps1` zbadanie wymagane parametry:

   `AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale`

   Parametry skryptu:
   * **resourceId: [String]: Wymagane** — jest to Identyfikatora zasobu platformy Azure dla istniejącego v1 standardowe lub bramy v1 zapory aplikacji sieci Web. Aby znaleźć tę wartość ciągu, przejdź do witryny Azure portal, wybierz swój bramy aplikacji lub zasobów zapory aplikacji sieci Web i kliknij przycisk **właściwości** link dla bramy. Identyfikator zasobu znajduje się na tej stronie.

     Można również uruchomić następujące polecenia programu Azure PowerShell, aby uzyskać identyfikator zasobu:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]:  Wymagane** — jest to przestrzeń adresów IP, która została przydzielona (lub chcesz przydzielić) dla nowej podsieci, która zawiera nowe bramy w wersji 2. To musi być określona w notacji CIDR. Na przykład: 10.0.0.0/24. Nie trzeba tworzyć wcześniej tej podsieci. Skrypt utworzy go dla Ciebie, jeśli nie istnieje.
   * **appgwName: [String]: Opcjonalnie**. Jest to ciąg, który określa się użycie jako nazwę nowej bramy Standard_v2 lub WAF_v2. Jeśli ten parametr nie jest podany, nazwę istniejącej bramy v1 będzie używany wraz z sufiksem *_v2* dołączane.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Opcjonalnie**.  Rozdzielana przecinkami listy obiektów PSApplicationGatewaySslCertificate, które tworzysz do reprezentowania certyfikatów SSL z bramy w wersji 1 muszą być przesłane do nowej bramy w wersji 2. Dla każdego z certyfikatów SSL skonfigurowane dla standardowych v1 lub bramy v1 zapory aplikacji sieci Web, można utworzyć nowy obiekt PSApplicationGatewaySslCertificate za pośrednictwem `New-AzApplicationGatewaySslCertificate` przedstawionego w tym miejscu polecenia. Należy ścieżkę do pliku certyfikatu SSL i hasło.

       Ten parametr jest tylko opcjonalne, jeśli nie masz odbiorników HTTPS skonfigurowany dla bramy w wersji 1 lub zapory aplikacji sieci Web. Jeśli masz co najmniej jeden Konfiguracja odbiornika protokołu HTTPS, należy określić ten parametr.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      Możesz przekazać `$mySslCert1, $mySslCert2` (rozdzielana przecinkami) w poprzednim przykładzie jako wartości tego parametru w skrypcie.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Opcjonalnie**. Rozdzielana przecinkami lista PSApplicationGatewayTrustedRootCertificate obiektów, które tworzysz do reprezentowania [zaufanych certyfikatów głównych](ssl-overview.md) uwierzytelniania wystąpień wewnętrznej bazy danych z bramy w wersji 2.  

      Aby utworzyć listę obiektów PSApplicationGatewayTrustedRootCertificate, zobacz [New AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [String]: Opcjonalnie**. Określony prywatny adres IP, którą chcesz skojarzyć do nowej bramy w wersji 2.  Musi to być w tej samej sieci wirtualnej, można przydzielić nowej bramy w wersji 2. Jeśli to nie jest określona, skrypt przydziela prywatny adres IP dla bramy w wersji 2.
    * **publicIpResourceId: [String]: Opcjonalnie**. Identyfikator zasobu dla publicznego zasobu adresu IP w ramach subskrypcji, którą chcesz przydzielić do nowej bramy w wersji 2. Jeśli nie zostanie określony, skrypt przydziela nowy publiczny adres IP w tej samej grupie zasobów. Nazwa jest nazwą bramy v2 z *- IP* dołączane.
   * **validateMigration: [Przełącz]: Opcjonalnie**. Użyj tego parametru, jeśli chcesz, aby przez skrypt niektórych konfiguracji podstawowej porównywania, sprawdzania poprawności po utworzenie bramy w wersji 2 i kopii konfiguracji. Domyślnie nie jest sprawdzana.
   * **enableAutoScale: [Przełącz]: Opcjonalnie**. Użyj tego parametru, jeśli chcesz, aby skrypt, aby włączyć Skalowanie automatyczne na nową bramę v2, po jego utworzeniu. Domyślnie automatyczne skalowanie jest wyłączone. Można zawsze ręcznie włączyć go później bramy nowo utworzoną w wersji 2.

1. Uruchom skrypt, za pomocą odpowiednich parametrów.

    **Przykład**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceName "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrowanie ruchu klientów

Po pierwsze Sprawdź skrypt pomyślnie utworzono nową bramę v2 z dokładną konfiguracją migrowane z bramy w wersji 1. Można to sprawdzić, w witrynie Azure portal.

Ponadto Wyślij niewielką ilość ruchu za pośrednictwem bramy v2 jako testu ręcznego.
  
Poniżej przedstawiono kilka scenariuszy, w którym Twojej bieżącej bramy application gateway (warstwa standardowa) może pojawić się ruch klientów i naszymi zaleceniami dotyczącymi każdego z nich:

* **Niestandardowa strefa DNS (np. contoso.com) wskazujący na adres IP serwera sieci Web (przy użyciu rekordu A) skojarzone z Standard w wersji 1 lub bramy v1 zapory aplikacji sieci Web**.

    Możesz zaktualizować rekord DNS, aby wskazywał etykiety DNS lub adresu IP frontonu skojarzonej z Twojej bramy application gateway Standard_v2. W zależności od czasu wygaśnięcia skonfigurowane na rekord DNS może upłynąć trochę czasu cały ruch z klienta do migracji do nowej bramy w wersji 2.
* **Niestandardowe strefy DNS (np. contoso.com) wskazujący Etykieta DNS (na przykład: *myappgw.eastus.cloudapp.azure.com* przy użyciu rekordu CNAME) skojarzony z bramą v1**.

   Dostępne są dwie opcje:

  * Jeśli używasz publiczne adresy IP na Twojej bramy application gateway, możesz to zrobić kontrolowanego, szczegółową migracji przyrostowo kierowania ruchu (metody routingu ważonego ruchu) do nowej bramy w wersji 2 przy użyciu profilu usługi Traffic Manager.

    Można to zrobić poprzez dodanie etykiet DNS v1 i v2 bram aplikacji, aby [profilu usługi Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)i CNAMEing niestandardowe rekord DNS (np. www.contoso.com) do domeny usługi Traffic Manager (na przykład contoso.trafficmanager.NET).
  * Lub można zaktualizować rekord DNS domeny niestandardowej wskaż etykietą DNS nową bramę aplikacji w wersji 2. W zależności od czasu wygaśnięcia skonfigurowane na rekord DNS może upłynąć trochę czasu cały ruch z klienta do migracji do nowej bramy w wersji 2.
* **Usługi klienci łączą się z frontonu adres IP bramy application gateway**.

   Zaktualizuj klientom na używanie adresy IP skojarzone z bramą aplikacji nowo utworzoną w wersji 2. Zaleca się, że nie używasz adresów IP bezpośrednio. Należy rozważyć użycie etykiety nazwy DNS (na przykład yourgateway.eastus.cloudapp.azure.com) skojarzony z bramą aplikacji można CNAME do własnych niestandardowych strefy DNS (np. contoso.com).

## <a name="common-questions"></a>Często zadawane pytania

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Czy istnieją jakiekolwiek ograniczenia, za pomocą skryptu programu Azure PowerShell do migracji konfiguracji z v1 na v2?

Tak. Zobacz [ostrzeżenia/ograniczenia](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Ma zastosowanie także produktu brama aplikacji zapory aplikacji internetowych w tym artykule i skrypt programu Azure PowerShell? 

Tak.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Skrypt programu Azure PowerShell również przyjmie nad ruchem między bramą v1 Moje bramy nowo utworzoną w wersji 2?

Nie. Skrypt programu Azure PowerShell migruje wyłącznie konfiguracji. Rzeczywisty ruch migracji jest odpowiedzialny za i w kontrolce.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Ma nową bramę v2, utworzone przez skrypt programu Azure PowerShell odpowiedni rozmiar do obsługi całego ruchu, który jest aktualnie obsługiwany przez mojej bramy v1?

Skrypt programu Azure PowerShell tworzy nową bramę v2 z rozmiarem odpowiedniego do obsługi ruchu w istniejącej bramie V1. Automatyczne skalowanie jest domyślnie wyłączona, ale można włączyć Skalowanie automatyczne, po uruchomieniu skryptu.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Czy mogę napotkał problemy przy użyciu tego skryptu. Jak uzyskać pomoc?
  
Możesz wysłać wiadomość e-mail na adres appgwmigrationsup@microsoft.com, otwórz zgłoszenie do pomocy technicznej z pomocy technicznej platformy Azure lub wykonać obie czynności.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o aplikacji bramy w wersji 2](application-gateway-autoscaling-zone-redundant.md)
