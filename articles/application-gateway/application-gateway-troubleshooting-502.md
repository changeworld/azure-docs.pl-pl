---
title: Rozwiązywanie błędów nieprawidłowej bramy bramy aplikacji platformy Azure (502)
description: Dowiedz się, jak rozwiązywać problemy z błędami 502 bramy aplikacji
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: amsriva
ms.openlocfilehash: 2a1c7e480e896da6852949c9d765d17290e4e9ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64697160"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Rozwiązywanie problemów z błędami nieprawidłowej bramy w usłudze Application Gateway

Dowiedz się, jak rozwiązywać problemy z nieprawidłową bramą (502) błędy odebrane podczas korzystania z usługi Azure Application Gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

Po skonfigurowaniu bramy aplikacji, jednym z błędów, które może zostać wyświetlony jest "Błąd serwera: 502 — Serwer sieci Web odebrał nieprawidłową odpowiedź, działając jako brama lub serwer proxy”. Ten błąd może się zdarzyć z następujących głównych przyczyn:

* Sieciowa grupa zabezpieczeń, Routing zdefiniowany przez użytkownika lub niestandardowych serwerów DNS blokuje dostęp do składowych puli zaplecza.
* Maszyny wirtualne lub wystąpienia zestawu skalowania maszyn wirtualnych zaplecza nie odpowiada na domyślnej funkcji badania kondycji.
* Nieprawidłowy lub nieprawidłowej konfiguracji niestandardowe sondy kondycji.
* Application Gateway platformy Azure [puli zaplecza nie jest skonfigurowany lub jest pusty](#empty-backendaddresspool).
* Żadna z maszyn wirtualnych lub wystąpień w [zestawu skalowania maszyn wirtualnych są w dobrej kondycji](#unhealthy-instances-in-backendaddresspool).
* [Żądanie limitu czasu lub łącznością problemów](#request-time-out) z żądaniami użytkowników.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problem z sieciową grupą zabezpieczeń, trasa zdefiniowana przez użytkownika lub niestandardowych serwerów DNS

### <a name="cause"></a>Przyczyna

Jeśli dostęp do wewnętrznej bazy danych jest zablokowany z powodu sieciowej grupy zabezpieczeń, Routing zdefiniowany przez użytkownika lub niestandardowe DNS, wystąpienia bramy aplikacji nie można nawiązać połączenia z puli zaplecza. Powoduje to niepowodzeń sondy, wynikające z błędami 502.

Sieciowa grupa zabezpieczeń/trasy zdefiniowanej przez użytkownika może być obecny w podsieci bramy aplikacji lub podsieci, wdrożonym maszyn wirtualnych aplikacji.

Podobnie obecności niestandardowego systemu DNS w sieci wirtualnej można również powodować problemy. Nazwy FQDN używane dla składowych puli zaplecza może nie być rozpoznawane poprawnie przez serwer DNS skonfigurowane przez użytkownika dla sieci wirtualnej.

### <a name="solution"></a>Rozwiązanie

Sprawdzanie poprawności konfiguracji sieciowej grupy zabezpieczeń, Routing zdefiniowany przez użytkownika i DNS, przechodząc przez następujące kroki:

* Sprawdź, czy sieciowe grupy zabezpieczeń skojarzone z podsiecią bramy aplikacji. Upewnij się, że komunikacja z wewnętrznej bazy danych nie jest zablokowany.
* Sprawdź, czy trasa zdefiniowana przez użytkownika skojarzoną z podsiecią bramy aplikacji. Upewnij się, że zdefiniowanej przez użytkownika nie jest kierowanie ruchu od podsieci wewnętrznej bazy danych. Na przykład Sprawdź, czy routing do sieci wirtualnych urządzeń sieciowych lub domyślne trasy anonsowane do podsieci bramy aplikacji za pośrednictwem usługi ExpressRoute/sieci VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Sprawdź skuteczne sieciowej grupy zabezpieczeń i tras za pomocą wewnętrznej bazy danych maszyny Wirtualnej

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Sprawdź występowanie niestandardowe DNS w sieci wirtualnej. DNS można sprawdzić, patrząc na szczegółowe informacje o właściwościach sieci wirtualnej w danych wyjściowych.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Jeśli jest obecny, upewnij się, że serwer DNS może poprawnie rozpoznać nazwy FQDN składowej puli zaplecza.

## <a name="problems-with-default-health-probe"></a>Problemy z domyślnej funkcji badania kondycji

### <a name="cause"></a>Przyczyna

502 błędy mogą być także wskaźniki częste, że domyślnej funkcji badania kondycji nie może połączyć maszyny wirtualne zaplecza.

Gdy zostanie zainicjowane wystąpienie bramy aplikacji, automatycznie konfiguruje domyślne sondy kondycji do każdego BackendAddressPool przy użyciu właściwości parametr BackendHttpSetting. Dane wejściowe użytkownika nie jest wymagane do ustawienia tej sondy. W szczególności skonfigurowano regułę równoważenia obciążenia, tworzone jest skojarzenie między parametr BackendHttpSetting i BackendAddressPool. Domyślnej funkcji badania jest skonfigurowany dla każdego z tych skojarzeń i uruchomiona usługa application gateway okresowego sprawdzania połączenia do każdego wystąpienia w BackendAddressPool na port określony w elemencie parametr BackendHttpSetting. 

W poniższej tabeli wymieniono wartości skojarzone z domyślnej funkcji badania kondycji:

| Właściwość sondy | Wartość | Opis |
| --- | --- | --- |
| Adres URL sondy |`http://127.0.0.1/` |Ścieżka adresu URL |
| Interval |30 |Interwał sondy w sekundach |
| Limit czasu |30 |Sondy czasu w sekundach |
| Próg złej kondycji |3 |Sonda liczbę ponownych prób. Serwer zaplecza jest oznaczony w dół po liczba niepowodzeń sondy kolejnych osiąga próg złej kondycji. |

### <a name="solution"></a>Rozwiązanie

* Upewnij się, że domyślna witryna jest skonfigurowana i nasłuchuje na 127.0.0.1.
* Jeśli parametr BackendHttpSetting określa portu innego niż 80, domyślnej witryny, należy skonfigurować do nasłuchiwania na tym porcie.
* Wywołanie `http://127.0.0.1:port` powinien zostać zwrócony kod wyniku protokołu HTTP 200. Powinna to być zwrócona przed upływem limitu czasu 30 sekund.
* Upewnij się, że skonfigurowany port jest otwarty i że nie istnieją żadne reguły zapory lub grupy zabezpieczeń sieci platformy Azure, które blokować ruch przychodzący lub wychodzący na porcie skonfigurowanym.
* Jeśli klasyczne maszyny wirtualne platformy Azure lub usługi w chmurze jest używany z nazwą FQDN lub publiczny adres IP, upewnij się, że odpowiednie [punktu końcowego](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) jest otwarty.
* Jeśli maszyna wirtualna jest skonfigurowana za pomocą usługi Azure Resource Manager i spoza sieci wirtualnej, w której wdrożono bramy application gateway [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md) musi być skonfigurowana do Zezwalaj na dostęp na odpowiedni port.

## <a name="problems-with-custom-health-probe"></a>Problemy z sondy kondycji niestandardowe

### <a name="cause"></a>Przyczyna

Niestandardowe sondy kondycji zezwolić na dodatkową elastyczność na potrzeby domyślnego badania zachowanie. Użycie sond niestandardowych, można skonfigurować interwał sondowania, adres URL, ścieżkę do testowania i jak wiele odpowiedzi nie powiodło się, aby zaakceptować przed oznaczeniem wystąpienia pulę zaplecza o złej kondycji.

Dodano następujące dodatkowe właściwości:

| Właściwość sondy | Opis |
| --- | --- |
| Name (Nazwa) |Nazwa sondy. Ta nazwa jest używana do odwoływania się do sondowania w ustawieniach protokołu HTTP zaplecza. |
| Protocol |Protokół używany do wysyłania sondy. Sonda korzysta z protokołu, zdefiniowane w ustawieniach HTTP zaplecza |
| Host |Nazwa hosta, aby wysłać sondy. Dotyczy tylko wtedy, gdy połączenia obejmujące wiele lokacji jest skonfigurowana w usłudze application gateway. To różni się od nazwy hosta maszyny Wirtualnej. |
| Ścieżka |Ścieżka względna sondy. Nieprawidłowa ścieżka zaczyna się od "/". Sonda jest wysyłana do \<protokołu\>://\<hosta\>:\<portu\>\<ścieżki\> |
| Interval |Interwał sondy w sekundach. Jest to odstęp czasu między dwóch następujących po sobie sondy. |
| Limit czasu |Sonda limitu czasu w sekundach. Odebranie prawidłowej odpowiedzi nie jest to przed upływem limitu czasu, sondy jest oznaczony jako zakończony niepowodzeniem. |
| Próg złej kondycji |Sonda liczbę ponownych prób. Serwer zaplecza jest oznaczony w dół po liczba niepowodzeń sondy kolejnych osiąga próg złej kondycji. |

### <a name="solution"></a>Rozwiązanie

Sprawdź, czy niestandardowa sonda kondycji jest skonfigurowane poprawnie jako zgodnie z poprzednią tabelą. Oprócz powyższych kroków rozwiązywania problemów również zapewnić następujące okoliczności:

* Upewnij się, że sonda poprawnie określono zgodnie [przewodnik](application-gateway-create-probe-ps.md).
* Jeśli brama aplikacji jest skonfigurowana dla jednej lokacji, domyślnie hosta należy określić nazwę jako `127.0.0.1`, chyba że inaczej skonfigurowana w niestandardowej sondy.
* Upewnij się, że wywołanie http://\<hosta\>:\<portu\>\<ścieżki\> zwraca kod wyniku protokołu HTTP 200.
* Upewnij się, że interwał, limit czasu i UnhealtyThreshold znajdują się w dopuszczalnym zakresie.
* Jeśli za pomocą sondy protokołu HTTPS, upewnij się, że serwera wewnętrznej bazy danych nie wymaga SNI, konfigurując certyfikat fallback na serwerze wewnętrznej bazy danych.

## <a name="request-time-out"></a>Limit czasu żądania

### <a name="cause"></a>Przyczyna

Po odebraniu żądania użytkownika bramy application gateway stosuje skonfigurowane reguły na żądanie i kieruje je do wystąpienia puli zaplecza. Czeka konfigurowalnym interwale czasu odpowiedzi z wystąpienia zaplecza. Domyślnie jest to interwał **20** sekund. Jeśli bramy application gateway nie otrzymasz odpowiedzi od aplikacji zaplecza, w tym interwał, żądanie użytkownika pobiera 502 błąd.

### <a name="solution"></a>Rozwiązanie

Usługa Application Gateway umożliwia skonfigurowanie tego ustawienia za pośrednictwem parametr BackendHttpSetting, który można następnie zastosować do różnych pul. Różnych pul zaplecza może mieć inny parametr BackendHttpSetting i skonfigurowany limit czasu żądania innego.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Empty BackendAddressPool

### <a name="cause"></a>Przyczyna

Jeśli bramy application gateway nie ma maszyn wirtualnych lub zestawu skalowania maszyn wirtualnych skonfigurowanych w puli adresów zaplecza, go nie można skierować wszelkie żądania klienta i wysyła błędów nieprawidłowej bramy.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że pula adresów zaplecza nie jest pusty. Można to zrobić za pośrednictwem programu PowerShell, interfejsu wiersza polecenia lub portalu.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Dane wyjściowe z poprzedniego polecenia cmdlet powinna zawierać puli adresów zaplecza niepusta. Poniższy przykład pokazuje, że dwie pule zwrócone, które są skonfigurowane za pomocą nazwy FQDN lub adresów IP dla maszyn wirtualnych wewnętrznej bazy danych. Stan aprowizacji BackendAddressPool musi mieć wartość "Succeeded".

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

## <a name="unhealthy-instances-in-backendaddresspool"></a>Wystąpienia w BackendAddressPool

### <a name="cause"></a>Przyczyna

Jeśli wszystkie wystąpienia elementu BackendAddressPool jest w złej kondycji bramy application gateway nie ma dowolnego zaplecza na żądanie użytkownika trasy. Może to być również wymagane w przypadku wystąpień zaplecza są w dobrej kondycji, ale nie ma wymaganej aplikacji, które są wdrożone.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że wystąpienia są w dobrej kondycji, a aplikacja jest poprawnie skonfigurowana. Sprawdź, jeśli wystąpień zaplecza mogą odpowiadać na polecenia ping z inną maszyną Wirtualną w tej samej sieci wirtualnej. Jeśli skonfigurowano publiczny punkt końcowy, upewnij się, że żądanie przeglądarki do aplikacji sieci web jest zdatne do użytku.

## <a name="next-steps"></a>Kolejne kroki

Jeśli powyższe czynności nie rozwiąże problemu, otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).

