---
title: Konfigurowanie protokołu SSL odciążania — Azure Application Gateway — PowerShell — Model Klasyczny | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera instrukcje dotyczące tworzenia bramy aplikacji przy użyciu protokołu SSL offload przy użyciu klasycznego modelu wdrażania platformy
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 63f28d96-9c47-410e-97dd-f5ca1ad1b8a4
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: 89a88d79b6b93a233dbd4f335d0eb449e49d5289
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122204"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Konfigurowanie bramy aplikacji na potrzeby odciążania protokołu SSL przy użyciu klasycznego modelu wdrażania

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-ssl-arm.md)
> * [Program Azure PowerShell klasycznego](application-gateway-ssl.md)
> * [Interfejs wiersza polecenia platformy Azure](application-gateway-ssl-cli.md)

Usługę Azure Application Gateway można skonfigurować tak, aby przerywała sesję protokołu SSL (Secure Sockets Layer) na poziomie bramy, co pozwoli na uniknięcie wykonywania kosztownych zadań szyfrowania protokołu SSL w kolektywie serwerów sieci Web. Odciążanie protokołu SSL upraszcza również konfigurowanie serwerów frontonu i zarządzanie aplikacją internetową.

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Zainstaluj najnowszą wersję poleceń cmdlet programu Azure PowerShell za pomocą Instalatora platformy sieci Web. Najnowszą wersję można pobrać i zainstalować z sekcji **Windows PowerShell** strony [Pliki do pobrania](https://azure.microsoft.com/downloads/).
2. Sprawdź, czy masz działającą sieć wirtualną z prawidłową podsiecią. Upewnij się, że z podsieci nie korzystają żadne maszyny wirtualne ani wdrożenia w chmurze. Brama aplikacji musi znajdować się w podsieci sieci wirtualnej.
3. Serwery konfigurowane do używania bramy aplikacji muszą istnieje lub ma ich punkty końcowe, które są tworzone w sieci wirtualnej lub za pomocą publicznego adresu IP lub wirtualny adres IP (VIP) przypisany.

Aby skonfigurować odciążanie protokołu SSL w bramie aplikacji, wykonaj następujące kroki w podanej kolejności:

1. [Tworzenie bramy aplikacji](#create-an-application-gateway)
2. [Przekazywanie certyfikatów SSL](#upload-ssl-certificates)
3. [Konfigurowanie bramy](#configure-the-gateway)
4. [Ustaw konfigurację bramy](#set-the-gateway-configuration)
5. [Uruchomić bramę](#start-the-gateway)
6. [Sprawdzanie stanu bramy](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Aby utworzyć bramę, wprowadź `New-AzureApplicationGateway` polecenia cmdlet, zastępując wartości swoimi własnymi. Opłaty za bramę nie są jeszcze naliczane. Rozliczanie zaczyna się na późniejszym etapie, po pomyślnym uruchomieniu bramy.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Aby sprawdzić, czy brama została utworzona, można wprowadzić `Get-AzureApplicationGateway` polecenia cmdlet.

W tym przykładzie **opis**, **InstanceCount**, i **GatewaySize** są opcjonalnymi parametrami. Wartością domyślną dla **InstanceCount** jest **2**, o maksymalnej wartości **10**. Wartością domyślną dla **GatewaySize** jest **średni**. Małe i duże są inne dostępne wartości. **VirtualIPs** i **DnsName** są wyświetlane jako puste, ponieważ brama nie została jeszcze uruchomiona. Te wartości są tworzone po brama znajduje się w stanie uruchomienia.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Przekazywanie certyfikatów SSL

Wprowadź `Add-AzureApplicationGatewaySslCertificate` można przekazać certyfikatu serwera w formacie PFX do usługi application gateway. Nazwa certyfikatu jest nazwą wybranych przez użytkownika i muszą być unikatowe w obrębie bramy aplikacji. Ten certyfikat jest określany przy użyciu tej nazwy w wszystkie operacje zarządzania certyfikatami w usłudze application gateway.

Poniższy przykład pokazuje polecenia cmdlet. Zastąp wartości w próbce własnymi.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Następnie sprawdź poprawność przekazywanie certyfikatu. Wprowadź `Get-AzureApplicationGatewayCertificate` polecenia cmdlet.

Poniższy przykład pokazuje, w pierwszym wierszu, a następnie dane wyjściowe polecenia cmdlet:

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
> Hasło certyfikatu musi mieć długość od 4 – 12 znaków, które składają się z liter i cyfr. Znaki specjalne, nie są akceptowane.

## <a name="configure-the-gateway"></a>Konfigurowanie bramy

Konfiguracji usługi application gateway składa się z wielu wartości. Wartości mogą być powiązane ze sobą w celu utworzenia konfiguracji.

Potrzebne wartości:

* **Pula serwerów zaplecza**: lista adresów IP serwerów zaplecza. Adresy IP na liście powinny należeć do podsieci sieci wirtualnej lub być publiczny adres IP lub adresów VIP.
* **Ustawienia puli serwerów zaplecza**: każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są powiązane z pulą i są stosowane do wszystkich serwerów w tej puli.
* **Port frontonu**: port publiczny, który jest otwierany w bramie aplikacji. Ruch trafia do tego portu, a następnie jest przekierowywany do jednego z serwerów zaplecza.
* **Odbiornik**: Odbiornik ma port frontonu, protokół (Http lub Https; te wartości jest rozróżniana wielkość liter) oraz nazwę certyfikatu SSL (w przypadku konfigurowania odciążania protokołu SSL).
* **Reguła**: Reguła wiąże odbiornik z puli serwerów zaplecza i umożliwia zdefiniowanie której puli serwerów zaplecza do kierowania ruchu do kiedy trafienia do określonego odbiornika. Obecnie jest obsługiwana tylko reguła *podstawowa*. Reguła *podstawowa* to dystrybucja obciążenia z działaniem okrężnym.

**Dodatkowe uwagi dotyczące konfiguracji**

W przypadku konfiguracji certyfikatów SSL protokół w polu **HttpListener** należy zmienić na **Https** (z uwzględnieniem wielkości liter). Dodaj **SslCert** elementu **HttpListener** z wartością ustawioną na tej samej nazwie, które są używane w [certyfikaty SSL przekazywanie](#upload-ssl-certificates) sekcji. Port frontonu należy zaktualizować do **443**.

**Aby włączyć koligację opartą na plikach cookie**: Można skonfigurować bramę aplikacji, aby upewnić się, że żądania z sesji klienta było zawsze kierowane do tej samej maszyny Wirtualnej w kolektywie serwerów sieci web. Aby to osiągnąć, Wstaw plik cookie sesji, który umożliwi bramie prawidłowe kierowanie ruchu. Aby włączyć koligację opartą na plikach cookie, ustaw element **CookieBasedAffinity** na wartość **Enabled** w elemencie **BackendHttpSettings**.

Można skonstruować konfigurację, tworząc obiekt konfiguracji, albo za pomocą pliku XML konfiguracji.
Do utworzenia konfiguracji za pomocą pliku XML konfiguracji, wprowadź poniższego przykładu:


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

## <a name="set-the-gateway-configuration"></a>Ustaw konfigurację bramy

Następnym etapem jest skonfigurowanie bramy aplikacji. Możesz wprowadzić `Set-AzureApplicationGatewayConfig` polecenia cmdlet z plikiem konfiguracyjnym XML lub obiektu konfiguracji.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Uruchamianie bramy

Po skonfigurowaniu bramy, wprowadź `Start-AzureApplicationGateway` polecenia cmdlet, aby uruchomić bramę. Naliczanie opłat za bramę aplikacji rozpocznie się po pomyślnym uruchomieniu bramy.

> [!NOTE]
> `Start-AzureApplicationGateway` Polecenie cmdlet może zająć 15 – 20 minut na zakończenie.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Sprawdzanie stanu bramy

Wprowadź `Get-AzureApplicationGateway` polecenia cmdlet, aby sprawdzić stan bramy. Jeśli `Start-AzureApplicationGateway` zostało pomyślnie wykonane w poprzednim kroku **stanu** powinien być **systemem**i **VirtualIPs** i **DnsName** powinien zawierać prawidłowe wpisy.

W tym przykładzie pokazano bramę aplikacji, która działa uruchomiona i gotowa do przyjmowania ruchu:

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

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o równoważeniu obciążenia opcje ogólnie rzecz biorąc, zobacz:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
