---
title: Skonfiguruj klasycznego środowiska PowerShell odciążania — brama usługi aplikacji Azure — SSL | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera instrukcje, aby utworzyć bramę aplikacji przy użyciu protokołu SSL Odciążanie przy użyciu modelu klasycznego wdrożenia usługi Azure
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
ms.openlocfilehash: e620730b86d648c1ac9db7a9e6faa7a2d206b46e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Skonfiguruj bramę aplikacji dla odciążania protokołu SSL przy użyciu klasycznego modelu wdrażania

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-ssl-arm.md)
> * [Program Azure PowerShell klasycznego](application-gateway-ssl.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](application-gateway-ssl-cli.md)

Usługę Azure Application Gateway można skonfigurować tak, aby przerywała sesję protokołu SSL (Secure Sockets Layer) na poziomie bramy, co pozwoli na uniknięcie wykonywania kosztownych zadań szyfrowania protokołu SSL w kolektywie serwerów sieci Web. Odciążanie protokołu SSL upraszcza również konfigurowanie serwerów frontonu i zarządzanie aplikacją sieci Web.

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Zainstaluj najnowszą wersję poleceń cmdlet programu Azure PowerShell za pomocą Instalatora platformy sieci Web. Najnowszą wersję można pobrać i zainstalować z sekcji **Windows PowerShell** strony [Pliki do pobrania](https://azure.microsoft.com/downloads/).
2. Sprawdź, czy masz działającą sieć wirtualną z prawidłową podsiecią. Upewnij się, że z podsieci nie korzystają żadne maszyny wirtualne ani wdrożenia w chmurze. Brama aplikacji musi znajdować się w podsieci sieci wirtualnej.
3. Serwery, które można skonfigurować do korzystania z bramy aplikacji musi istnieje lub ma ich punkty końcowe, które są tworzone w sieci wirtualnej lub wirtualnego adresu IP (VIP) przypisany lub publicznego adresu IP.

Aby skonfigurować bramę aplikacji odciążanie protokołu SSL, wykonaj następujące kroki w podanej kolejności:

1. [Tworzenie bramy aplikacji](#create-an-application-gateway)
2. [Przekaż certyfikaty SSL](#upload-ssl-certificates)
3. [Konfigurowanie bramy](#configure-the-gateway)
4. [Ustaw konfigurację bramy](#set-the-gateway-configuration)
5. [Uruchom bramę](#start-the-gateway)
6. [Sprawdź stan bramy](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Aby utworzyć bramę, wprowadź `New-AzureApplicationGateway` polecenia cmdlet, zastępując wartości własnymi. Opłaty za bramę nie są jeszcze naliczane. Rozliczanie zaczyna się na późniejszym etapie, po pomyślnym uruchomieniu bramy.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Aby sprawdzić, czy utworzono bramę, możesz wprowadzić `Get-AzureApplicationGateway` polecenia cmdlet.

W przykładzie **opis**, **InstanceCount**, i **GatewaySize** są opcjonalnymi parametrami. Wartość domyślna dla **InstanceCount** jest **2**, wartość maksymalna **10**. Wartość domyślna dla **GatewaySize** jest **średni**. Małe i duże są inne dostępne wartości. **VirtualIPs** i **DnsName** są wyświetlane jako puste, ponieważ brama nie została jeszcze uruchomiona. Te wartości są tworzone po brama jest w stanie uruchomienia.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Przekaż certyfikaty SSL

Wprowadź `Add-AzureApplicationGatewaySslCertificate` można przekazać certyfikatu serwera w formacie PFX na bramie aplikacji. Nazwa certyfikatu jest nazwą wybranych przez użytkownika i muszą być unikatowe w ramach bramy aplikacji. Ten certyfikat jest określane o tej nazwie we wszystkich operacjach zarządzania certyfikatu dla bramy aplikacji.

Poniższy przykład zawiera polecenia cmdlet. Zastąp wartości w próbce własnymi.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Następnie sprawdź poprawność przekazywanie certyfikatu. Wprowadź `Get-AzureApplicationGatewayCertificate` polecenia cmdlet.

Poniższy przykład zawiera polecenia cmdlet w pierwszym wierszu, a następnie dane wyjściowe:

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
> Hasło certyfikatu musi należeć do zakresu od 4 do 12 znaków składa się z liter i cyfr. Znaki specjalne nie są akceptowane.

## <a name="configure-the-gateway"></a>Konfigurowanie bramy

Konfiguracja bramy aplikacji składa się z wielu wartości. Wartości mogą być powiązane ze sobą w celu utworzenia konfiguracji.

Potrzebne wartości:

* **Pula serwerów zaplecza**: Lista adresów IP serwerów zaplecza. Na liście adresów IP powinna należeć do podsieci sieci wirtualnej lub powinny być publiczny adres IP lub wirtualne adresy IP.
* **Ustawienia puli serwera zaplecza**: co Pula ma ustawienia, takie jak port protokołu i koligacji na podstawie plików cookie. Te ustawienia są powiązane z pulą i są stosowane do wszystkich serwerów w tej puli.
* **Port frontonu**: ten port jest port publiczny, który jest otwarty na bramie aplikacji. Ruch trafia do tego portu, a następnie jest przekierowywany do jednego z serwerów zaplecza.
* **Odbiornik**: odbiornika ma port frontonu, protokół (Http lub Https; tych wartości jest rozróżniana wielkość liter) oraz nazwę certyfikatu SSL (jeśli konfigurowania odciążanie protokołu SSL).
* **Reguła**: reguła wiąże odbiornika i puli serwerów zaplecza i określa, które puli serwerów zaplecza umożliwiających kierowanie ruchem do gdy liczba trafień określonego odbiornika. Obecnie jest obsługiwana tylko reguła *podstawowa*. Reguła *podstawowa* to dystrybucja obciążenia z działaniem okrężnym.

**Uwagi dotyczące konfiguracji dodatkowych**

W przypadku konfiguracji certyfikatów SSL protokół w polu **HttpListener** należy zmienić na **Https** (z uwzględnieniem wielkości liter). Dodaj **SslCert** elementu **HttpListener** o wartości do tej samej nazwie, używane w [certyfikaty SSL przekazać](#upload-ssl-certificates) sekcji. Port frontonu powinny zostać uaktualnione do **443**.

**Aby włączyć koligacji na podstawie plików cookie**: można skonfigurować bramę aplikacji, aby upewnić się, że żądanie z sesji klienta jest zawsze kierowane do tej samej maszyny Wirtualnej w kolektywie serwerów sieci web. W tym celu należy wstawić umożliwia bramy do kierowania ruchem danych odpowiednio plik cookie sesji. Aby włączyć koligację opartą na plikach cookie, ustaw element **CookieBasedAffinity** na wartość **Enabled** w elemencie **BackendHttpSettings**.

Można utworzyć konfiguracji poprzez utworzenie obiektu konfiguracji lub przy użyciu pliku XML konfiguracji.
Aby utworzyć konfigurację za pomocą pliku XML konfiguracji, wprowadź poniższy przykład:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
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

Następnym etapem jest skonfigurowanie bramy aplikacji. Możesz wprowadzić `Set-AzureApplicationGatewayConfig` polecenia cmdlet obiekt konfiguracji lub plik XML konfiguracji.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Uruchamianie bramy

Po skonfigurowaniu bramy, wprowadź `Start-AzureApplicationGateway` polecenia cmdlet można uruchomić bramy. Naliczanie opłat za bramę aplikacji rozpocznie się po pomyślnym uruchomieniu bramy.

> [!NOTE]
> `Start-AzureApplicationGateway` Polecenie cmdlet może zająć 15-20 minut na zakończenie.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Sprawdzanie stanu bramy

Wprowadź `Get-AzureApplicationGateway` polecenia cmdlet, aby sprawdzić stan bramy. Jeśli `Start-AzureApplicationGateway` zakończyło się pomyślnie w poprzednim kroku, **stanu** powinien być **systemem**i **VirtualIPs** i **DnsName** powinien ma nieprawidłowe.

W tym przykładzie pokazano bramę aplikacji, który jest włączony, uruchamianie i gotowa do sporządzenia ruchu:

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
