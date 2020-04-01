---
title: Migrowanie z wersji 1 do wersji 2 — brama aplikacji platformy Azure
description: W tym artykule pokazano, jak przeprowadzić migrację bramy aplikacji platformy Azure i Zapory aplikacji sieci Web z wersji 1 do wersji 2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 96f3825288846e86771ef3907eb4da4e58630df3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475178"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrowanie bramy aplikacji platformy Azure i zapory aplikacji sieci Web z wersji 1 do wersji 2

[Usługa Azure Application Gateway i Zapora aplikacji sieci Web (WAF) w wersji 2](application-gateway-autoscaling-zone-redundant.md) są teraz dostępne, oferując dodatkowe funkcje, takie jak skalowanie automatyczne i nadmiarowość strefy dostępności. Jednak istniejące bramy w wersji 1 nie są automatycznie uaktualniane do wersji 2. Jeśli chcesz przeprowadzić migrację z wersji 1 do wersji 2, wykonaj kroki opisane w tym artykule.

Istnieją dwa etapy migracji:

1. Migrowanie konfiguracji
2. Migrowanie ruchu klienta

W tym artykule opisano migrację konfiguracji. Migracja ruchu klienta różni się w zależności od określonego środowiska. Jednak niektóre wysokiego szczebla, ogólne zalecenia [są dostarczane](#migrate-client-traffic).

## <a name="migration-overview"></a>Przegląd migracji

Dostępny jest skrypt programu Azure PowerShell, który wykonuje następujące czynności:

* Tworzy nową Standard_v2 lub WAF_v2 bramę w określonej podsieci sieci wirtualnej.
* Bezproblemowo kopiuje konfigurację skojarzoną z bramą standardu w wersji 1 lub WAF do nowo utworzonej bramy Standard_V2 lub WAF_V2.

### <a name="caveatslimitations"></a>Zastrzeżenia\Ograniczenia

* Nowa brama w wersji 2 ma nowe publiczne i prywatne adresy IP. Nie jest możliwe bezproblemowe przeniesienie adresów IP skojarzonych z istniejącą bramą w wersji 1 do wersji 2. Można jednak przydzielić istniejący (nieprzydzielony) publiczny lub prywatny adres IP do nowej bramy w wersji 2.
* Należy podać przestrzeń adresową IP dla innej podsieci w sieci wirtualnej, w której znajduje się brama w wersji 1. Skrypt nie może utworzyć bramy w wersji 2 w istniejących podsieciach, które mają już bramę w wersji 1. Jeśli jednak istniejąca podsieć ma już bramę w wersji 2, może ona nadal działać pod warunkiem, że jest wystarczająca ilość miejsca adresu IP.
* Aby przeprowadzić migrację konfiguracji SSL, należy określić wszystkie certyfikaty SSL używane w bramie w wersji 1.
* Jeśli dla bramy V1 jest włączony tryb FIPS, nie zostanie ona zmigrowana do nowej bramy w wersji 2. Tryb FIPS nie jest obsługiwany w wersji 2.
* Wersja 2 nie obsługuje IPv6, więc bramy v1 z włączoną funkcją IPv6 nie są migrowane. Jeśli skrypt zostanie uruchomiony, może on nie zostać ukończony.
* Jeśli brama w wersji 1 ma tylko prywatny adres IP, skrypt tworzy publiczny adres IP i prywatny adres IP dla nowej bramy w wersji 2. Bramy w wersji 2 obecnie nie obsługują tylko prywatnych adresów IP.
* Nagłówki z nazwami zawierającymi inne niż litery, cyfry, łączniki i podkreślenia nie są przekazywane do aplikacji. Dotyczy to tylko nazw nagłówków, a nie wartości nagłówka. Jest to przełomowa zmiana z v1.

## <a name="download-the-script"></a>Pobierz skrypt

Pobierz skrypt migracji z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Korzystanie ze skryptu

Istnieją dwie opcje w zależności od lokalnej konfiguracji środowiska programu PowerShell i preferencji:

* Jeśli nie masz zainstalowanych modułów Az platformy Azure lub nie masz nic przeciwko odinstalowaniu modułów `Install-Script` usługi Azure Az, najlepszą opcją jest użycie opcji uruchomienia skryptu.
* Jeśli chcesz zachować moduły usługi Azure Az, najlepiej jest pobrać skrypt i uruchomić go bezpośrednio.

Aby ustalić, czy masz zainstalowane moduły `Get-InstalledModule -Name az`Az platformy Azure, uruchom program . Jeśli nie widzisz żadnych zainstalowanych modułów Az, `Install-Script` możesz użyć tej metody.

### <a name="install-using-the-install-script-method"></a>Instalowanie przy użyciu metody Install-Script

Aby użyć tej opcji, nie może mieć modułów Az platformy Azure zainstalowanych na komputerze. Jeśli są zainstalowane, następujące polecenie wyświetla błąd. Można odinstalować moduły usługi Azure Az lub użyć innej opcji, aby ręcznie pobrać skrypt i uruchomić go.
  
Uruchom skrypt za pomocą następującego polecenia:

`Install-Script -Name AzureAppGWMigration`

To polecenie instaluje również wymagane moduły Az.  

### <a name="install-using-the-script-directly"></a>Instalowanie bezpośrednio przy użyciu skryptu

Jeśli masz zainstalowane moduły usługi Azure Az i nie możesz ich odinstalować (lub nie chcesz ich odinstalowywać), możesz ręcznie pobrać skrypt za pomocą karty **Ręczne pobieranie** w linku do pobierania skryptu. Skrypt jest pobierany jako nieprzetworzony plik nupkg. Aby zainstalować skrypt z tego pliku nupkg, zobacz [Ręczne pobieranie pakietu](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Aby uruchomić skrypt:

1. Służy `Connect-AzAccount` do łączenia się z platformą Azure.

1. Służy `Import-Module Az` do importowania modułów Az.

1. Uruchom, `Get-Help AzureAppGWMigration.ps1` aby sprawdzić wymagane parametry:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parametry skryptu:
   * **resourceId: [String]: Wymagane** — jest to identyfikator zasobu platformy Azure dla istniejącej bramy standardu w wersji 1 lub WAF w wersji 1. Aby znaleźć tę wartość ciągu, przejdź do witryny Azure Portal, wybierz bramę aplikacji lub **zasób** WAF i kliknij łącze Właściwości bramy. Identyfikator zasobu znajduje się na tej stronie.

     Można również uruchomić następujące polecenia programu Azure PowerShell, aby uzyskać identyfikator zasobu:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [Ciąg]: Wymagane** — jest to przestrzeń adresowa IP, która została przydzielona (lub chcesz przydzielić) dla nowej podsieci, która zawiera nową bramę w wersji 2. Należy to określić w notacji CIDR. Na przykład: 10.0.0.0/24. Nie trzeba tworzyć tej podsieci z wyprzedzeniem. Skrypt tworzy go dla Ciebie, jeśli nie istnieje.
   * **appgwName: [Ciąg]: Opcjonalnie**. Jest to ciąg określony jako nazwa dla nowej bramy Standard_v2 lub WAF_v2. Jeśli ten parametr nie zostanie podany, nazwa istniejącej bramy w wersji 1 będzie używana z sufiksem *_v2* dołączone.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Opcjonalnie**.  Oddzielona przecinkami lista obiektów PSApplicationGatewaySslCertificate, które tworzysz w celu reprezentowania certyfikatów SSL z bramy w wersji 1, musi zostać przekazana do nowej bramy w wersji 2. Dla każdego certyfikatu SSL skonfigurowanego dla bramy Standard v1 lub WAF v1 można utworzyć nowy obiekt PSApplicationGatewaySslCertificate za pomocą `New-AzApplicationGatewaySslCertificate` polecenia pokazanego tutaj. Potrzebujesz ścieżki do pliku Cert SSL i hasła.

     Ten parametr jest opcjonalny tylko wtedy, gdy nie masz odbiorników HTTPS skonfigurowanych dla bramy w wersji 1 lub WAF. Jeśli masz co najmniej jedną konfigurację odbiornika HTTPS, należy określić ten parametr.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     W poprzednim `$mySslCert1, $mySslCert2` przykładzie można przekazać (rozdzielone przecinkami) jako wartości dla tego parametru w skrypcie.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Opcjonalnie**. Oddzielona przecinkami lista obiektów PSApplicationGatewayTrustedRootCertificate, które tworzysz w celu [reprezentowania zaufanych certyfikatów głównych](ssl-overview.md) do uwierzytelniania wystąpień wewnętrznej bazy danych z bramy w wersji 2.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Aby utworzyć listę obiektów PSApplicationGatewayTrustedCertificate, zobacz [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [Ciąg]: Opcjonalnie**. Określony prywatny adres IP, który chcesz skojarzyć z nową bramą w wersji 2.  Musi to być z tej samej sieci wirtualnej, które można przydzielić dla nowej bramy w wersji 2. Jeśli nie jest to określone, skrypt przydziela prywatny adres IP dla bramy w wersji 2.
   * **publicIpResourceId: [Ciąg]: Opcjonalnie**. Identyfikator zasobów istniejącego publicznego adresu IP (standardowej jednostki SKU) w ramach subskrypcji, który chcesz przydzielić do nowej bramy w wersji 2. Jeśli nie zostanie to określone, skrypt przydziela nowy publiczny adres IP w tej samej grupie zasobów. Nazwa jest nazwą bramy v2 z *dołączenym -IP.*
   * **validateMigration: [switch]: Opcjonalnie**. Użyj tego parametru, jeśli chcesz, aby skrypt wykonać kilka podstawowych weryfikacji porównania konfiguracji po utworzeniu bramy w wersji 2 i kopii konfiguracji. Domyślnie nie jest wykonywane sprawdzanie poprawności.
   * **enableAutoScale: [switch]: Opcjonalnie**. Użyj tego parametru, jeśli chcesz, aby skrypt włączyć skalowanie automatyczne na nowej bramy w wersji 2 po jego utworzeniu. Domyślnie skalowanie automatyczne jest wyłączone. Zawsze można ręcznie włączyć go później na nowo utworzonej bramie w wersji 2.

1. Uruchom skrypt przy użyciu odpowiednich parametrów. Może upłynąć od pięciu do siedmiu minut, aby zakończyć.

    **Przykład**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrowanie ruchu klienta

Najpierw sprawdź, czy skrypt pomyślnie utworzył nową bramę w wersji 2 z dokładną konfiguracją migrujną z bramy w wersji 1. Można to sprawdzić za pomocą witryny Azure portal.

Ponadto wyślij niewielką ilość ruchu przez bramę v2 jako test ręczny.
  
Oto kilka scenariuszy, w których bieżąca brama aplikacji (Standard) może odbierać ruch klientów, a nasze zalecenia dla każdego z nich:

* **Niestandardowa strefa DNS (na przykład contoso.com), która wskazuje adres IP frontona (przy użyciu rekordu A) skojarzonego z bramą Standard v1 lub WAF v1**.

    Można zaktualizować rekord DNS, aby wskazywał etykietę IP lub DNS frontona skojarzoną z bramą aplikacji Standard_v2. W zależności od czasu wygaśnięcia skonfigurowanego w rekordzie DNS migracja całego ruchu klienta do nowej bramy w 2.
* **Niestandardowa strefa DNS (na przykład contoso.com), która wskazuje etykietę DNS (na przykład: *myappgw.eastus.cloudapp.azure.com* przy użyciu rekordu CNAME) skojarzoną z bramą w wersji 1**.

   Masz dwie możliwości:

  * Jeśli używasz publicznych adresów IP w bramie aplikacji, można wykonać kontrolowaną, szczegółową migrację przy użyciu profilu usługi Traffic Manager, aby przyrostowo kierować ruch (metoda routingu ruchu ważonego) do nowej bramy w wersji 2.

    Można to zrobić, dodając etykiety DNS bram aplikacji w wersji 1 i 2 do [profilu Usługi Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method) `www.contoso.com`i CNAMEing niestandardowy rekord DNS (na przykład) do domeny Menedżera ruchu (na przykład contoso.trafficmanager.net).
  * Można też zaktualizować rekord DNS domeny niestandardowej, aby wskazywał etykietę DNS nowej bramy aplikacji w wersji 2. W zależności od czasu wygaśnięcia skonfigurowanego w rekordzie DNS migracja całego ruchu klienta do nowej bramy w 2.
* **Klienci łączą się z adresem IP wewnętrznej bazy danych bramy aplikacji.**

   Zaktualizuj klientów, aby używali adresów IP skojarzonych z nowo utworzoną bramą aplikacji w wersji 2. Zaleca się, aby nie używać adresów IP bezpośrednio. Należy rozważyć użycie etykiety nazwy DNS (na przykład yourgateway.eastus.cloudapp.azure.com) skojarzonej z bramą aplikacji, która może być CNAME do własnej niestandardowej strefy DNS (na przykład contoso.com).

## <a name="common-questions"></a>Często zadawane pytania

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Czy istnieją jakieś ograniczenia ze skryptem programu Azure PowerShell w celu migracji konfiguracji z wersji 1 do wersji 2?

Tak. Zobacz [Zastrzeżenia/Ograniczenia](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Czy ten artykuł i skrypt programu Azure PowerShell ma zastosowanie również do produktu WAF bramy aplikacji? 

Tak.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Czy skrypt programu Azure PowerShell przełącza się również za ruch z mojej bramy w wersji 1 do nowo utworzonej bramy w wersji 2?

Nie. Skrypt programu Azure PowerShell tylko migruje konfigurację. Rzeczywista migracja ruchu jest twoim obowiązkiem i kontrolą.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Czy nowa brama w wersji 2 została utworzona przez skrypt programu Azure PowerShell odpowiednio dobrana do obsługi całego ruchu, który jest obecnie obsługiwany przez moją bramę w wersji 1?

Skrypt programu Azure PowerShell tworzy nową bramę w wersji 2 o odpowiednim rozmiarze do obsługi ruchu na istniejącej bramie w wersji 1. Skalowanie automatyczne jest domyślnie wyłączone, ale po uruchomieniu skryptu można włączyć skalowanie automatyczne.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Skonfigurowałem bramę w wersji 1 do wysyłania dzienników do magazynu platformy Azure. Czy skrypt replikuje tę konfigurację również dla wersji 2?

Nie. Skrypt nie replikuje tej konfiguracji dla wersji 2. Konfigurację dziennika należy dodać oddzielnie do zmigrowanego bramy w wersji 2.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Czy ten skrypt obsługuje certyfikaty przesłane do usługi Azure KeyVault?

Nie. Obecnie skrypt nie obsługuje certyfikatów w keyvault. Jest to jednak rozważane w przyszłej wersji.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Wpadłem na pewne problemy z wykorzystaniem tego skryptu. Jak mogę uzyskać pomoc?
  
Możesz wysłać wiadomość appgwmigrationsup@microsoft.come-mail do , otwórz sprawę pomocy technicznej za pomocą pomocy technicznej platformy Azure lub wykonaj obie te usługi.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o bramie aplikacji w wersji 2](application-gateway-autoscaling-zone-redundant.md)
