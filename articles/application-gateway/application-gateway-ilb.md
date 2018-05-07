---
title: Przy użyciu bramy aplikacji Azure z wewnętrznego modułu równoważenia obciążenia | Dokumentacja firmy Microsoft
description: Ta strona zawiera instrukcje dotyczące konfigurowania bramy aplikacji Azure z punktem końcowym wewnętrzny o zrównoważonym obciążeniu
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 7403d28e-909f-46a2-b282-43a8e942f53c
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: 5d5e70d46121252c616d77d33ee82397e3a3ca84
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Tworzenie Bramy aplikacji z wewnętrznym modułem równoważenia obciążenia (ILB)

> [!div class="op_single_selector"]
> * [Klasyczny portal Azure — program PowerShell](application-gateway-ilb.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-ilb-arm.md)

Brama aplikacji można skonfigurować internetowy wirtualnego adresu IP lub wewnętrzny punkt końcowy nie uwidoczniony w Internecie, znanej także jako punktu końcowego wewnętrznego modułu równoważenia obciążenia (ILB). Konfigurowanie bramy z ILB jest przydatna do wewnętrznych aplikacji biznesowych — nie są widoczne dla Internetu. Jest również przydatne w przypadku warstwy aplikacji wielowarstwowych, który znajduje się w granicy zabezpieczeń nie są widoczne do Internetu, ale nadal wymagają rozkład obciążenia działanie okrężne, lepkości sesji lub kończenia żądań SSL z/usług. W tym artykule przeprowadzimy Cię przez proces konfigurowania bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Zainstaluj najnowszą wersję poleceń cmdlet programu Azure PowerShell, za pomocą Instalatora platformy sieci Web. Można pobrać i zainstalować najnowszą wersję ze **programu Windows PowerShell** sekcji [stronę pobierania](https://azure.microsoft.com/downloads/).
2. Sprawdź, czy sieć wirtualną pracy z prawidłową podsieć.
3. Sprawdź, czy masz serwerów wewnętrznej bazy danych w sieci wirtualnej lub z publicznego adresu IP/VIP przypisane.

Aby utworzyć bramę aplikacji, wykonaj następujące kroki w podanej kolejności. 

1. [Tworzenie bramy aplikacji](#create-a-new-application-gateway)
2. [Konfigurowanie bramy](#configure-the-gateway)
3. [Ustaw konfigurację bramy](#set-the-gateway-configuration)
4. [Uruchom bramę](#start-the-gateway)
5. [Sprawdź bramy](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Utwórz bramę aplikacji:

**Aby utworzyć bramę**, użyj `New-AzureApplicationGateway` polecenia cmdlet, zastępując wartości własnymi. Zauważ, że opłaty za bramę nie są jeszcze naliczane. Rozliczanie zaczyna się na późniejszym etapie, po pomyślnym uruchomieniu bramy.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

```
VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399
```

**Aby sprawdzić poprawność** czy utworzono bramę, możesz użyć `Get-AzureApplicationGateway` polecenia cmdlet. 

W przykładzie *opis*, *InstanceCount*, i *GatewaySize* są opcjonalnymi parametrami. Wartość domyślna parametru *InstanceCount* to 2, a wartość maksymalna — 10. Wartość domyślna parametru *GatewaySize* to Medium (Średnia). Małe i duże są inne dostępne wartości. *VIP* i *DnsName* są wyświetlane jako puste, ponieważ brama nie została jeszcze uruchomiona. Zostaną utworzone, gdy brama zacznie działać. 

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 4:39:39 PM - Begin Operation:
Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
Operation: Get-AzureApplicationGateway
Name: AppGwTest    
Description: 
VnetName: testvnet1 
Subnets: {Subnet-1} 
InstanceCount: 2 
GatewaySize: Medium 
State: Stopped 
VirtualIPs: 
DnsName:
```

## <a name="configure-the-gateway"></a>Konfigurowanie bramy
Konfiguracja bramy aplikacji składa się z wielu wartości. Wartości mogą być powiązane ze sobą w celu utworzenia konfiguracji.

Potrzebne wartości:

* **Pula serwerów wewnętrznej bazy danych:** listę adresów IP serwerów wewnętrznej bazy danych. Na liście adresów IP albo powinna należeć do podsieci sieci wirtualnej lub powinny być publicznego adresu IP/VIP. 
* **Ustawienia puli serwerów zaplecza:** każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są powiązane z pulą i są stosowane do wszystkich serwerów w tej puli.
* **Port serwera sieci Web:** ten port jest port publiczny otwarte na bramie aplikacji. Ruch trafia do tego portu, a następnie jest przekierowywany do jednego z serwerów zaplecza.
* **Odbiornik:** odbiornika ma port serwera sieci Web, protokół (Http lub Https, te jest rozróżniana wielkość liter) oraz nazwę certyfikatu SSL (jeśli odciążania Konfigurowanie protokołu SSL). 
* **Reguła:** reguła wiąże odbiornika i puli serwerów wewnętrznej bazy danych i definiuje puli serwerów wewnętrznej bazy danych, których ruch powinny być kierowane do, gdy trafienia w szczególności odbiornika. Obecnie jest obsługiwana tylko reguła *podstawowa*. Reguła *podstawowa* to dystrybucja obciążenia z działaniem okrężnym.

Można utworzyć konfiguracji poprzez utworzenie obiektu konfiguracji lub plik XML konfiguracji. Do utworzenia konfiguracji przy użyciu pliku XML konfiguracji, użyj przykładu poniżej.

Pamiętaj o następujących kwestiach:

* *Konfiguracji IP frontonu* element opisuje zastosowanie w przypadku konfigurowania bramy aplikacji przy użyciu ILB szczegóły ILB. 
* Adres IP frontonu *typu* powinien być ustawiony na "Private"
* *StaticIPAddress* powinien być ustawiony na żądaną wewnętrznym adresem IP, na którym Brama odbiera ruch. Należy pamiętać, że *StaticIPAddress* element jest opcjonalny. Jeśli nie jest dostępny wewnętrzny adres IP z podsieci wdrożonej zestaw, jest wybierany. 
* Wartość *nazwa* elementu określonego w parametrze *elementu FrontendIPConfiguration* powinny być używane w HTTPListener *FrontendIP* element, aby odwołać się do Konfiguracja IP frontonu.
  
  **Przykładowy plik XML konfiguracji**
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name> 
            <Type>Private</Type> 
            <StaticIPAddress>10.0.0.10</StaticIPAddress> 
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>80</Port>
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
            <FrontendIP>fip1</FrontendIP>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Http</Protocol>
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
Następnie będzie Ustaw bramy aplikacji. Można użyć `Set-AzureApplicationGatewayConfig` polecenia cmdlet bez obiekt konfiguracji lub z pliku XML konfiguracji. 

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

```
VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d
```

## <a name="start-the-gateway"></a>Uruchamianie bramy

Po skonfigurowaniu bramy użyj polecenia cmdlet `Start-AzureApplicationGateway`, aby uruchomić bramę. Naliczanie opłat za bramę aplikacji rozpocznie się po pomyślnym uruchomieniu bramy. 

> [!NOTE]
> `Start-AzureApplicationGateway` Polecenie cmdlet może potrwać do 15-20 minut. 
> 
> 

```powershell
Start-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b
```

## <a name="verify-the-gateway-status"></a>Sprawdzanie stanu bramy

Użyj `Get-AzureApplicationGateway` polecenia cmdlet, aby sprawdzić stan bramy. Jeśli `Start-AzureApplicationGateway` zakończyło się pomyślnie w poprzednim kroku, powinien mieć stan *systemem*, i Vip oraz DnsName powinny mieć prawidłowe wpisy. Przykład obejmuje polecenia cmdlet w pierwszym wierszu, to po danych wyjściowych. W tym przykładzie brama jest uruchomiona i jest gotowa do sporządzenia ruchu. 

> [!NOTE]
> Brama aplikacji jest skonfigurowana do akceptowania ruchu w skonfigurowanym punkcie końcowym ILB 10.0.0.10 w tym przykładzie.

```powershell
Get-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
Name          : AppGwTest
Description   : 
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {10.0.0.10}
DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net
```

## <a name="next-steps"></a>Kolejne kroki
Więcej ogólnych informacji na temat opcji równoważenia obciążenia możesz znaleźć w następujących artykułach:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

