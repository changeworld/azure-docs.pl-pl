---
title: Odciążanie SSL przy użyciu programu PowerShell — brama aplikacji platformy Azure
description: Ten artykuł zawiera instrukcje dotyczące tworzenia bramy aplikacji z odciążaniem SSL przy użyciu klasycznego modelu wdrażania platformy Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: c456a0856adb0d36349b5f96ba0ab8bab3eec5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047919"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Konfigurowanie bramy aplikacji dla odciążania SSL przy użyciu klasycznego modelu wdrażania

> [!div class="op_single_selector"]
> * [Portal Azure](application-gateway-ssl-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-ssl-arm.md)
> * [Klasyczna usługa Azure PowerShell](application-gateway-ssl.md)
> * [Interfejs wiersza polecenia platformy Azure](application-gateway-ssl-cli.md)

Usługę Azure Application Gateway można skonfigurować tak, aby przerywała sesję protokołu SSL (Secure Sockets Layer) na poziomie bramy, co pozwoli na uniknięcie wykonywania kosztownych zadań szyfrowania protokołu SSL w kolektywie serwerów sieci Web. Odciążanie protokołu SSL upraszcza również konfigurowanie serwerów frontonu i zarządzanie aplikacją internetową.

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Zainstaluj najnowszą wersję poleceń cmdlet programu Azure PowerShell za pomocą Instalatora platformy sieci Web. Najnowszą wersję można pobrać i zainstalować z sekcji **Windows PowerShell** strony [Pliki do pobrania](https://azure.microsoft.com/downloads/).
2. Sprawdź, czy masz działającą sieć wirtualną z prawidłową podsiecią. Upewnij się, że z podsieci nie korzystają żadne maszyny wirtualne ani wdrożenia w chmurze. Brama aplikacji musi znajdować się w podsieci sieci wirtualnej.
3. Serwery skonfigurowane do używania bramy aplikacji muszą istnieć lub mają przypisane ich punkty końcowe, które są tworzone w sieci wirtualnej lub z publicznym adresem IP lub wirtualnym adresem IP (VIP).

Aby skonfigurować odciążanie SSL na bramie aplikacji, wykonaj następujące kroki w podanej kolejności:

1. [Tworzenie bramy aplikacji](#create-an-application-gateway)
2. [Przekazywanie certyfikatów SSL](#upload-ssl-certificates)
3. [Konfigurowanie bramy](#configure-the-gateway)
4. [Ustawianie konfiguracji bramy](#set-the-gateway-configuration)
5. [Uruchamianie bramy](#start-the-gateway)
6. [Sprawdzanie stanu bramy](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Aby utworzyć bramę, `New-AzureApplicationGateway` wprowadź polecenie cmdlet, zastępując wartości własnymi. Opłaty za bramę nie są jeszcze naliczane. Rozliczanie zaczyna się na późniejszym etapie, po pomyślnym uruchomieniu bramy.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Aby sprawdzić, czy brama została utworzona, można wprowadzić polecenie `Get-AzureApplicationGateway` cmdlet.

W przykładzie **Description**, **InstanceCount**i **GatewaySize** są parametrami opcjonalnymi. Wartość domyślna dla **InstanceCount** to **2**, z maksymalną wartością **10**. Wartością domyślną dla **GatewaySize** jest **Medium**. Małe i duże są inne dostępne wartości. **VirtualIPs** i **DnsName** są wyświetlane jako puste, ponieważ brama nie została jeszcze uruchomiona. Te wartości są tworzone po brama jest w stanie uruchomionym.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Przekazywanie certyfikatów SSL

Wprowadź, `Add-AzureApplicationGatewaySslCertificate` aby przekazać certyfikat serwera w formacie PFX do bramy aplikacji. Nazwa certyfikatu jest nazwą wybraną przez użytkownika i musi być unikatowa w bramie aplikacji. Ten certyfikat jest określany przez tę nazwę we wszystkich operacjach zarządzania certyfikatami w bramie aplikacji.

W poniższym przykładzie przedstawiono polecenie cmdlet. Zastąp wartości w próbce własnymi.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Następnie sprawdź poprawność przekazywania certyfikatu. Wprowadź `Get-AzureApplicationGatewayCertificate` polecenie cmdlet.

W poniższym przykładzie przedstawiono polecenie cmdlet w pierwszym wierszu, po którym następuje wyjście:

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> Hasło certyfikatu musi zawierać od 4 do 12 znaków składających się z liter lub cyfr. Znaki specjalne nie są akceptowane.

## <a name="configure-the-gateway"></a>Konfigurowanie bramy

Konfiguracja bramy aplikacji składa się z wielu wartości. Wartości mogą być powiązane ze sobą w celu skonstruowania konfiguracji.

Potrzebne wartości:

* **Pula serwerów zaplecza**: Lista adresów IP serwerów zaplecza. Wymienione adresy IP powinny należeć do podsieci sieci wirtualnej lub powinny być publicznym adresem IP lub ADRESEM VIP.
* **Ustawienia puli serwerów zaplecza:** Każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są powiązane z pulą i są stosowane do wszystkich serwerów w tej puli.
* **Port front-end:** Ten port jest portem publicznym otwierany na bramie aplikacji. Ruch trafia do tego portu, a następnie jest przekierowywany do jednego z serwerów zaplecza.
* **Odbiornik:** Odbiornik ma port front-end, protokół (Http lub Https; te wartości są rozróżniane wielkość liter) i nazwę certyfikatu SSL (jeśli konfiguruje odciążanie SSL).
* **Reguła:** Reguła wiąże odbiornika i puli serwerów zaplecza i definiuje, które back-end puli serwerów, aby skierować ruch do po trafieniu określonego odbiornika. Obecnie jest obsługiwana tylko reguła *podstawowa*. Reguła *podstawowa* to dystrybucja obciążenia z działaniem okrężnym.

**Dodatkowe uwagi dotyczące konfiguracji**

W przypadku konfiguracji certyfikatów SSL protokół w polu **HttpListener** należy zmienić na **Https** (z uwzględnieniem wielkości liter). Dodaj element **SslCert** do **httplistener** z wartością ustawioną na taką samą nazwę, która jest używana w sekcji [Przekaż certyfikaty SSL.](#upload-ssl-certificates) Port front-end powinien zostać zaktualizowany do **443**.

**Aby włączyć koligacji opartej na plikach cookie:** Można skonfigurować bramę aplikacji, aby upewnić się, że żądanie z sesji klienta jest zawsze kierowane do tej samej maszyny Wirtualnej w farmie sieci web. Aby to osiągnąć, należy wstawić plik cookie sesji, który umożliwia bramie odpowiednio kierować ruchem. Aby włączyć koligację opartą na plikach cookie, ustaw element **CookieBasedAffinity** na wartość **Enabled** w elemencie **BackendHttpSettings**.

Konfigurację można utworzyć, tworząc obiekt konfiguracyjny lub używając konfiguracyjnego pliku XML.
Aby utworzyć konfigurację przy użyciu konfiguracyjnego pliku XML, wprowadź następujący przykład:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>Ustawianie konfiguracji bramy

Następnym etapem jest skonfigurowanie bramy aplikacji. Polecenie `Set-AzureApplicationGatewayConfig` cmdlet można wprowadzić za pomocą obiektu konfiguracyjnego lub pliku XML konfiguracji.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Uruchamianie bramy

Po skonfigurowaniu bramy wprowadź polecenie `Start-AzureApplicationGateway` cmdlet, aby uruchomić bramę. Naliczanie opłat za bramę aplikacji rozpocznie się po pomyślnym uruchomieniu bramy.

> [!NOTE]
> Polecenie `Start-AzureApplicationGateway` cmdlet może potrwać 15-20 minut, aby zakończyć.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Sprawdzanie stanu bramy

Wprowadź `Get-AzureApplicationGateway` polecenie cmdlet, aby sprawdzić stan bramy. Jeśli `Start-AzureApplicationGateway` zakończy się pomyślnie w poprzednim kroku, **stan** powinien być **uruchomiony**, a **VirtualIPs** i **DnsName** powinny mieć prawidłowe wpisy.

W tym przykładzie pokazano bramę aplikacji, która jest uruchomiona, uruchomiona i gotowa do podjęcia ruchu:

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat opcji równoważenia obciążenia w ogóle, zobacz:

* [Moduł równoważenia obciążenia platformy Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
