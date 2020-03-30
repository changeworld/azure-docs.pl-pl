---
title: Tworzenie niestandardowej sondy przy użyciu usługi PowerShell — brama aplikacji platformy Azure
description: Dowiedz się, jak utworzyć niestandardową sondę bramy aplikacji przy użyciu programu PowerShell w klasycznym modelu wdrażania
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: e01a1cad98ded9d7ce8683b6adf38b5d53959774
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966812"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Tworzenie niestandardowej sondy dla bramy aplikacji platformy Azure (klasycznej) przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Portal Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-probe-ps.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-probe-classic-ps.md)

W tym artykule należy dodać niestandardową sondę do istniejącej bramy aplikacji z programem PowerShell. Sondy niestandardowe są przydatne dla aplikacji, które mają określoną stronę sprawdzania kondycji lub dla aplikacji, które nie zapewniają pomyślnej odpowiedzi w domyślnej aplikacji sieci web.

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i Klasyczny](../azure-resource-manager/management/deployment-models.md). W tym artykule opisano przy użyciu modelu wdrażania klasycznego. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.  Dowiedz się, jak [wykonać te kroki w modelu przy użyciu usługi Resource Manager](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Aby utworzyć bramę aplikacji:

1. Utwórz zasób bramy aplikacji.
2. Utwórz konfiguracyjny plik XML lub obiekt konfiguracji.
3. Przekaż konfigurację aplikacji do nowo utworzonego zasobu bramy aplikacji.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Tworzenie zasobu bramy aplikacji za pomocą sondy niestandardowej

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
> Wartość domyślna parametru *InstanceCount* to 2, a wartość maksymalna — 10. Wartość domyślna parametru *GatewaySize* to Medium (Średnia). Można wybrać między Małe, Średnie i Duże.
> 
> 

Parametry *VirtualIPs* (Wirtualne adresy IP) i *DnsName* (Nazwa serwera DNS) są wyświetlane jako puste, ponieważ brama nie została jeszcze uruchomiona. Te wartości są tworzone, gdy brama jest w stanie uruchomionym.

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

W poniższym przykładzie pokazano, jak użyć pliku konfiguracji do skonfigurowania bramy aplikacji w celu równoważenia obciążenia ruchu HTTP na porcie publicznym 80 i wysyłania ruchu sieciowego do portu zaplecza 80 między dwoma adresami IP przy użyciu sondy niestandardowej.

> [!IMPORTANT]
> W elemencie Http lub Https jest rozróżniana wielkość liter.

Nowy element \<konfiguracji\> Probe jest dodawany do konfigurowania sond niestandardowych.

Parametry konfiguracji to:

|Parametr|Opis|
|---|---|
|**Nazwa** |Nazwa odwołania dla sondy niestandardowej. |
| **Protokół** | Używany protokół (możliwe wartości to HTTP lub HTTPS).|
| **Host** i **ścieżka** | Pełna ścieżka adresu URL, która jest wywoływana przez bramę aplikacji w celu określenia kondycji wystąpienia. Na przykład jeśli masz witrynę\/http: /contoso.com/, a następnie niestandardowe sondy\/można skonfigurować dla "http: /contoso.com/path/custompath.htm" dla kontroli sondy mieć pomyślną odpowiedź HTTP.|
| **Interwał** | Konfiguruje sprawdzanie interwału sondy w sekundach.|
| **Limit czasu** | Definiuje limit czasu sondy dla sprawdzania odpowiedzi HTTP.|
| **UnhealthyThreshold** | Liczba nieudanych odpowiedzi HTTP potrzebnych do oznaczenia wystąpienia zaplecza jako *w złej kondycji*.|

Nazwa sondy odwołuje się \<w konfiguracji Wewnętrznej\> bazyhttpsettings przypisać, która pula zaplecza używa niestandardowych ustawień sondy.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Dodawanie sondy niestandardowej do istniejącej bramy aplikacji

Zmiana bieżącej konfiguracji bramy aplikacji wymaga trzech kroków: Pobierz bieżący plik konfiguracyjny XML, zmodyfikuj, aby mieć niestandardową sondę i skonfiguruj bramę aplikacji przy nowych ustawieniach XML.

1. Pobierz plik XML `Get-AzureApplicationGatewayConfig`za pomocą pliku . To polecenie cmdlet eksportuje konfigurację XML do zmodyfikowanego w celu dodania ustawienia sondy.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Otwórz plik XML w edytorze tekstu. Dodaj `<probe>` sekcję `<frontendport>`po pliku .

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

   W sekcji wewnętrznej bazy danychHttpSettings w formacie XML dodaj nazwę sondy, jak pokazano w poniższym przykładzie:

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

1. Zaktualizuj konfigurację bramy aplikacji `Set-AzureApplicationGatewayConfig`za pomocą nowego pliku XML za pomocą programu . To polecenie cmdlet aktualizuje bramę aplikacji o nową konfigurację.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz skonfigurować odciążanie warstwy SSL (Secure Sockets Layer), zobacz [Konfigurowanie bramy aplikacji dla odciążania SSL](application-gateway-ssl.md).

Jeśli chcesz skonfigurować bramę aplikacji do użycia z wewnętrznym modułem równoważenia obciążenia, zobacz artykuł [Create an application gateway with an internal load balancer (ILB)](application-gateway-ilb.md) (Tworzenie bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia).

