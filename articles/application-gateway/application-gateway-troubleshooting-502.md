---
title: Rozwiązywanie problemów zły brama bramy aplikacji Azure (502) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problemy z błędami 502 bramy aplikacji
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: 4eca6a588d2c95189f0ba995b8db195907e9dc39
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Rozwiązywanie problemów z błędami Zła brama bramy aplikacji

Dowiedz się, jak rozwiązywać problemy z błędów zły bramy (502) podczas używania bramy aplikacji.

## <a name="overview"></a>Przegląd

Po skonfigurowaniu bramy aplikacji, jest jeden z błędów, które użytkownicy mogą wystąpić "Błąd serwera: 502 — Serwer sieci Web odebrał nieprawidłową odpowiedź działając jako brama lub serwer proxy". Ten błąd może się zdarzyć z powodu następujących powodów:

* Grupy NSG, przez lub niestandardowe DNS blokuje dostęp do elementów członkowskich puli wewnętrznej bazy danych.
* Maszyny wirtualne zaplecza lub wystąpienia zestawu skali maszyny wirtualnej są [nie odpowiada na domyślnej sondy kondycji](#problems-with-default-health-probe.md).
* Nieprawidłowy lub niewłaściwego [konfiguracji sondy kondycji niestandardowych](#problems-with-custom-health-probe.md).
* Azure bramy aplikacji [puli zaplecza nie jest skonfigurowany lub pusty](#empty-backendaddresspool).
* Żadna z maszyn wirtualnych i wystąpień w [zestaw skali maszyny wirtualnej są w dobrej kondycji](#unhealthy-instances-in-backendaddresspool).
* [Problemy łączność lub limit czasu żądania](#request-time-out) z żądania użytkownika.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problem sieciowej grupy zabezpieczeń, trasy zdefiniowane przez użytkownika lub niestandardowe DNS

### <a name="cause"></a>Przyczyna

Zablokowanie dostępu do wewnętrznej bazy danych z powodu obecności NSG, przez lub niestandardowe DNS wystąpieniach bramy aplikacji nie będzie mogła nawiązać połączenia puli wewnętrznej bazy danych i może powodować błędy sondowania powoduje błędy 502. Należy pamiętać, że NSG/przez może znajdować się w podsieci bramy aplikacji lub podsieci wdrożonym maszyn wirtualnych do aplikacji. Podobnie obecności niestandardowe DNS w sieci Wirtualnej również może spowodować problemy, jeśli nazwy FQDN jest używane dla członków puli wewnętrznej bazy danych i nie zostanie rozwiązany poprawnie przez serwer DNS skonfigurowany użytkownik sieci wirtualnej.

### <a name="solution"></a>Rozwiązanie

Sprawdzanie poprawności konfiguracji grupy NSG, przez i DNS za pośrednictwem następujące czynności:
* Sprawdź grupy NSG skojarzony z podsiecią bramy aplikacji. Upewnij się, że komunikacji wewnętrznej bazy danych nie jest blokowany.
* Sprawdź przez skojarzony z podsiecią bramy aplikacji. Upewnij się, że przez nie jest kierowanie ruchu od podsieci wewnętrznej bazy danych — na przykład sprawdzić routingu do sieci wirtualnych urządzeń lub domyślne trasy anonsowane dla podsieci bramy aplikacji za pośrednictwem usługi ExpressRoute lub sieć VPN.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzureRmVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Sprawdź skuteczne NSG i trasy z wewnętrznej bazy danych maszyny Wirtualnej

```powershell
Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzureRmEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Sprawdź obecność niestandardowe DNS w sieci wirtualnej. DNS można sprawdzić, sprawdzając szczegóły właściwości sieci wirtualnej w danych wyjściowych.

```json
Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Jeśli jest obecny, upewnij się, że serwer DNS jest w stanie poprawnie rozpoznać nazwy FQDN członka puli wewnętrznej bazy danych.

## <a name="problems-with-default-health-probe"></a>Problemy z domyślnej funkcji badania kondycji

### <a name="cause"></a>Przyczyna

błędy 502 można także częste wskaźniki domyślnej funkcji badania kondycji nie jest możliwe nawiązanie łączności zaplecza maszyn wirtualnych. Po zainicjowaniu obsługi wystąpienia bramy aplikacji, automatycznie konfiguruje domyślne badanie kondycji do każdego BackendAddressPool przy użyciu właściwości BackendHttpSetting. Dane wejściowe użytkownika nie jest wymagane do ustawienia tej sondy. W szczególności w przypadku skonfigurowania reguły równoważenia obciążenia, skojarzenie jest nawiązywane pomiędzy BackendHttpSetting i BackendAddressPool. Domyślne badanie jest skonfigurowany dla każdego skojarzenia i brama aplikacji w inicjuje połączenie wyboru okresowego do każdego wystąpienia w BackendAddressPool na port określony w elemencie BackendHttpSetting. Poniższa tabela zawiera listę wartości skojarzone z domyślnej funkcji badania kondycji.

| Właściwość sondy | Wartość | Opis |
| --- | --- | --- |
| Adres URL sondy |http://127.0.0.1/ |Ścieżka adresu URL |
| Interwał |30 |Interwał sondowania w sekundach |
| Limit czasu |30 |Sonda limitu czasu w sekundach |
| Próg złej kondycji |3 |Badania liczby ponownych prób. Po kolejnych sondowania liczby awarii osiągnie próg złej kondycji serwera zaplecza jest oznaczony jako w dół. |

### <a name="solution"></a>Rozwiązanie

* Upewnij się, że domyślna witryna jest skonfigurowana i prowadzi nasłuchiwanie na 127.0.0.1.
* Jeśli BackendHttpSetting Określa port inny niż 80, należy skonfigurować domyślną witrynę do nasłuchiwania na tym porcie.
* Wywołanie http://127.0.0.1:port powinien zwrócić 200 kod wyniku HTTP. Powinna to być zwrócona w ciągu 30 sekund limitu czasu.
* Upewnij się, że skonfigurowany port jest otwarty i czy nie ma żadnych reguł zapory lub grup zabezpieczeń sieci Azure, która zablokować ruch przychodzący lub wychodzący na porcie skonfigurowanym.
* Jeśli klasyczne maszyny wirtualne platformy Azure lub usługi w chmurze jest używana nazwa FQDN lub publicznego adresu IP, upewnij się, że odpowiednie [punktu końcowego](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) jest otwarty.
* Jeśli maszyna wirtualna jest skonfigurowana za pośrednictwem usługi Azure Resource Manager i jest spoza sieci wirtualnej, w której wdrażana jest aplikacja bramy, [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md) musi być skonfigurowane i umożliwiają dostęp do wybranego portu.

## <a name="problems-with-custom-health-probe"></a>Problemy z sondy kondycji niestandardowych

### <a name="cause"></a>Przyczyna

Sondy kondycji niestandardowe umożliwiają dodatkowa elastyczność sondowanie zachowanie domyślne. Podczas korzystania z niestandardowego sond, użytkownicy mogą skonfigurować interwał sondowania, adres URL i ścieżki do testowania i ile odpowiedzi nie powiodło się do akceptowania przed oznaczeniem wystąpienia puli zaplecza swój stan jako niezdrowy. Zostaną dodane następujące dodatkowe właściwości.

| Właściwość sondy | Opis |
| --- | --- |
| Name (Nazwa) |Nazwa sondy. Ta nazwa jest używana do odwoływania się do sondowania w ustawieniach protokołu HTTP zaplecza. |
| Protokół |Protokół używany do wysyłania sondy. Sonda korzysta z protokołu zdefiniowanego w ustawieniach protokołu HTTP zaplecza |
| Host |Nazwa hosta, aby wysłać sondy. Dotyczy tylko wtedy, gdy obejmujący wiele lokacji jest skonfigurowany dla bramy aplikacji. To jest inna niż nazwa hosta maszyny Wirtualnej. |
| Ścieżka |Ścieżka względna sondy. Nieprawidłowa ścieżka rozpoczyna się od '/'. Sonda są wysyłane do \<protokołu\>://\<hosta\>:\<portu\>\<ścieżki\> |
| Interwał |Interwał sondy w sekundach. Jest to odstęp czasu między dwa kolejne sondy. |
| Limit czasu |Badania limitu czasu w sekundach. Jeśli w tym czasie limitu czasu nie odebrano prawidłowej odpowiedzi, sondy jest oznaczony jako nie powiodła się. |
| Próg złej kondycji |Badania liczby ponownych prób. Po kolejnych sondowania liczby awarii osiągnie próg złej kondycji serwera zaplecza jest oznaczony jako w dół. |

### <a name="solution"></a>Rozwiązanie

Zweryfikuj, że sondy kondycji niestandardowy jest skonfigurowana jako powyższej tabeli. Oprócz powyższych kroków upewnij się również następujące czynności:

* Upewnij się, że sondy została poprawnie określona w [przewodnik](application-gateway-create-probe-ps.md).
* Jeśli skonfigurowano brama aplikacji w jednej lokacji, domyślnie hosta należy określać nazwy jako "127.0.0.1", chyba że w przeciwnym razie skonfigurowane w niestandardowych sondowania.
* Upewnij się, że wywołanie http://\<hosta\>:\<portu\>\<ścieżki\> zwraca kod wyniku protokołu HTTP 200.
* Upewnij się, że interwału limitu czasu i UnhealtyThreshold są dopuszczalne zakresy.
* Jeśli przy użyciu sondy protokołu HTTPS, upewnij się, że serwer wewnętrznej bazy danych nie wymaga SNI przez skonfigurowanie rezerwowy certyfikatu na serwerze wewnętrznej bazy danych.

## <a name="request-time-out"></a>Limit czasu żądania

### <a name="cause"></a>Przyczyna

Po odebraniu żądania użytkownika bramy aplikacji stosuje skonfigurowanych reguł do żądania i kieruje je do wystąpienia puli zaplecza. Oczekuje na można skonfigurować interwał czasu odpowiedzi z wystąpienia zaplecza. Domyślnie jest to interwał **30 sekund**. Jeśli bramy aplikacji nie otrzymują odpowiedź z zaplecza aplikacji w tym interwał, zobaczyć 502 Błąd żądania użytkownika.

### <a name="solution"></a>Rozwiązanie

Brama aplikacji umożliwia użytkownikom tego ustawienia za pomocą BackendHttpSetting, które mogą być następnie stosowane do różnych pul. Różnych pul zaplecza może mieć różne BackendHttpSetting i limit czasu żądania dlatego różnych skonfigurowane.

```powershell
    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Pusty BackendAddressPool

### <a name="cause"></a>Przyczyna

Jeśli bramy aplikacji nie ma maszyn wirtualnych ani zestaw skalowania maszyny wirtualnej skonfigurowanych w puli adresów zaplecza go nie może kierować wszystkie żądania klienta i zgłasza błąd zły bramy.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że pula adresów zaplecza nie jest pusty. Można to zrobić za pośrednictwem programu PowerShell, interfejsu wiersza polecenia lub portalu.

```powershell
Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Dane wyjściowe z poprzedniego polecenia cmdlet powinny zawierać puli adresów zaplecza niepusta. Poniżej przedstawiono przykładowy, w których dwie pule są zwracane które są skonfigurowane przy użyciu nazwy FQDN lub adresów IP dla maszyn wirtualnych w wewnętrznej bazie danych. Stan inicjowania obsługi administracyjnej BackendAddressPool musi mieć wartość "Succeeded".

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Zła wystąpień w BackendAddressPool

### <a name="cause"></a>Przyczyna

Jeśli wszystkie wystąpienia BackendAddressPool jest zła, następnie bramy aplikacji nie będzie zawierało dowolnego zaplecza na żądanie użytkownika trasy. Przyczyną może być również wielkość liter podczas wystąpień zaplecza są w dobrej kondycji, ale nie ma wymaganej aplikacji, które są wdrożone.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że wystąpienia są w dobrej kondycji i aplikacja jest poprawnie skonfigurowana. Sprawdź, czy wystąpienia zaplecza są odpowiada na polecenia ping z innej maszyny Wirtualnej w tej samej sieci wirtualnej. Jeśli skonfigurowano publiczny punkt końcowy, upewnij się, że żądanie przeglądarki do aplikacji sieci web jest obsługiwanych.

## <a name="next-steps"></a>Kolejne kroki

Jeśli powyższe czynności nie rozwiązać ten problem, otwórz [obsługuje biletu](https://azure.microsoft.com/support/options/).

