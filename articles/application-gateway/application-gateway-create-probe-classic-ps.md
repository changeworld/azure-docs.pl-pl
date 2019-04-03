---
title: Tworzenie niestandardowej sondy — Azure Application Gateway — PowerShell — Model Klasyczny | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie niestandardowej sondy dla bramy aplikacji przy użyciu programu PowerShell w klasycznym modelu wdrażania
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 338a7be1-835c-48e9-a072-95662dc30f5e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 01c1768f60da98206f0dfd041745428256f545fc
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861883"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Tworzenie niestandardowej sondy dla bramy aplikacji platformy Azure (model klasyczny) przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-probe-ps.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-probe-classic-ps.md)

W tym artykule należy dodać niestandardowej sondy do istniejącej bramy aplikacji przy użyciu programu PowerShell. Niestandardowe sondy są przydatne w przypadku aplikacji używających konkretnej strony kontroli kondycji lub dla aplikacji, które nie są oferowane pomyślnej odpowiedzi na domyślnej aplikacji sieci web.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak [wykonać te kroki przy użyciu modelu usługi Resource Manager](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Aby utworzyć bramę aplikacji:

1. Utwórz zasób bramy aplikacji.
2. Utwórz konfiguracyjny plik XML lub obiekt konfiguracji.
3. Przekaż konfigurację aplikacji do nowo utworzonego zasobu bramy aplikacji.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Tworzenie zasobu bramy aplikacji przy użyciu niestandardowej sondy

Aby utworzyć bramę, użyj polecenia cmdlet `New-AzureApplicationGateway`, zastępując wartości własnymi. Opłaty za bramę nie są jeszcze naliczane. Rozliczanie zaczyna się na późniejszym etapie, po pomyślnym uruchomieniu bramy.

Poniższy przykład tworzy bramę aplikacji przy użyciu sieci wirtualnej „testvnet1” i podsieci „subnet-1”.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Aby sprawdzić, czy brama została utworzona, możesz użyć polecenia cmdlet `Get-AzureApplicationGateway`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> Wartość domyślna parametru *InstanceCount* to 2, a wartość maksymalna — 10. Wartość domyślna parametru *GatewaySize* to Medium (Średnia). Można wybrać mały, średni i duży.
> 
> 

Parametry *VirtualIPs* (Wirtualne adresy IP) i *DnsName* (Nazwa serwera DNS) są wyświetlane jako puste, ponieważ brama nie została jeszcze uruchomiona. Te wartości są tworzone, gdy brama zacznie działać.

### <a name="configure-an-application-gateway-by-using-xml"></a>Konfigurowanie bramy aplikacji przy użyciu języka XML

W poniższym przykładzie używany jest plik XML, aby skonfigurować wszystkie ustawienia bramy aplikacji i zatwierdzić je w zasobie bramy aplikacji.  

Skopiuj poniższy tekst do Notatnika.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Edytuj zawarte w nawiasach wartości elementów konfiguracji. Zapisz plik z rozszerzeniem .xml.

Poniższy przykład przedstawia sposób konfigurowania bramy aplikacji, aby równoważyć obciążenie ruchu HTTP na publicznym porcie 80 i rozsyłać ruch sieciowy do portu zaplecza 80 między dwoma adresami IP za pomocą niestandardowej sondy przy użyciu pliku konfiguracji.

> [!IMPORTANT]
> W elemencie Http lub Https jest rozróżniana wielkość liter.

Nowy element konfiguracji \<sondowania\> jest dodawany do Konfigurowanie niestandardowych sond.

Parametry konfiguracji są następujące:

|Parametr|Opis|
|---|---|
|**Name (Nazwa)** |Nazwa odwołania dla niestandardowej sondy. |
| **Protokół** | Protokół używany (możliwe wartości to HTTP lub HTTPS).|
| **Host** i **ścieżki** | Pełna ścieżka adresu URL, które jest wywoływane przez usługę application gateway w celu określenia kondycji wystąpienia. Na przykład, jeśli masz http witryny sieci Web:\//contoso.com/, a następnie niestandardowej sondy można skonfigurować dla "http:\//contoso.com/path/custompath.htm" dla sonda sprawdza mieć pomyślnej odpowiedzi HTTP.|
| **Interval** | Konfiguruje sprawdzanie interwału sondowania w ciągu kilku sekund.|
| **Limit czasu** | Określa limit czasu sondy sprawdzanie odpowiedzi HTTP.|
| **UnhealthyThreshold** | Liczba zakończonych niepowodzeniem odpowiedzi HTTP, potrzebne do wystąpienia zaplecza jako *złej kondycji*.|

Odwołuje się nazwa sondy \<BackendHttpSettings\> konfigurację, aby przypisać puli zaplecza, która korzysta z ustawień niestandardowej sondy.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Dodawanie niestandardowej sondy do istniejącej bramy aplikacji

Zmiana bieżącej konfiguracji bramy aplikacji wymaga wykonania trzech kroków: Pobierz bieżący plik konfiguracyjny XML, zmodyfikować tak, aby mieć niestandardową sondę i konfigurowania bramy aplikacji przy użyciu nowych ustawień XML.

1. Pobierz plik XML przy użyciu `Get-AzureApplicationGatewayConfig`. To polecenie cmdlet eksportuje konfigurację XML do zmodyfikowania, aby dodać ustawienie sondy.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Otwórz plik XML w edytorze tekstów. Dodaj `<probe>` sekcji po `<frontendport>`.

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   W sekcji backendHttpSettings XML należy dodać nazwę sondowania, jak pokazano w poniższym przykładzie:

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   Zapisz plik XML.

1. Aktualizowanie konfiguracji bramy aplikacji za pomocą nowego pliku XML przy użyciu `Set-AzureApplicationGatewayConfig`. To polecenie cmdlet aktualizuje Twojej bramy application gateway przy użyciu nowej konfiguracji.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz skonfigurować odciążanie protokołu Secure Sockets Layer (SSL), zobacz [Konfigurowanie bramy aplikacji na potrzeby odciążania protokołu SSL](application-gateway-ssl.md).

Jeśli chcesz skonfigurować bramę aplikacji do użycia z wewnętrznym modułem równoważenia obciążenia, zobacz artykuł [Create an application gateway with an internal load balancer (ILB)](application-gateway-ilb.md) (Tworzenie bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia).

