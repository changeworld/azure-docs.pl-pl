---
title: Rozwiązywanie błędów nieprawidłowej bramy bramy aplikacji platformy Azure (502) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z błędami 502 bramy aplikacji
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
ms.openlocfilehash: 1db16f203755f9afc265495daba056313138a5dc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819454"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Rozwiązywanie problemów z błędami nieprawidłowej bramy w usłudze Application Gateway

Dowiedz się, jak rozwiązywać problemy z nieprawidłową bramą (502) błędy odebrane podczas korzystania z bramy aplikacji.

## <a name="overview"></a>Przegląd

Po skonfigurowaniu bramy aplikacji, jednym z błędów, które mogą napotkać użytkownicy jest "Błąd serwera: 502 — Serwer sieci Web odebrał nieprawidłową odpowiedź, działając jako brama lub serwer proxy”. Ten błąd może się zdarzyć z następujących głównych przyczyn:

* Sieciowa grupa zabezpieczeń, Routing zdefiniowany przez użytkownika lub niestandardowych serwerów DNS blokuje dostęp do składowych puli zaplecza.
* Maszyny wirtualne lub wystąpienia zestawu skalowania maszyn wirtualnych zaplecza nie odpowiada na domyślnej funkcji badania kondycji.
* Nieprawidłowy lub nieprawidłowej konfiguracji niestandardowe sondy kondycji.
* Application Gateway platformy Azure [puli zaplecza nie jest skonfigurowany lub jest pusty](#empty-backendaddresspool).
* Żadna z maszyn wirtualnych lub wystąpień w [zestawu skalowania maszyn wirtualnych są w dobrej kondycji](#unhealthy-instances-in-backendaddresspool).
* [Żądanie limitu czasu lub łącznością problemów](#request-time-out) z żądaniami użytkowników.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problem z sieciową grupą zabezpieczeń, trasa zdefiniowana przez użytkownika lub niestandardowych serwerów DNS

### <a name="cause"></a>Przyczyna

Jeśli dostęp do wewnętrznej bazy danych jest zablokowany z powodu obecności sieciowej grupy zabezpieczeń, Routing zdefiniowany przez użytkownika lub niestandardowe DNS, wystąpień bramy Application Gateway nie będzie można dotrzeć do puli zaplecza i mogłoby spowodować niepowodzeń sondy, powodując błędy 502. Należy pamiętać, że sieciowa grupa zabezpieczeń/trasy zdefiniowanej przez użytkownika może być obecny w podsieci bramy aplikacji lub podsieci wdrożonym maszyn wirtualnych aplikacji. Podobnie obecności niestandardowe DNS w sieci Wirtualnej również spowodować problemy związane z Jeśli nazwy FQDN jest używane dla składowych puli zaplecza, a nie uda się rozwiązać poprawnie przez serwer DNS skonfigurowane przez użytkownika dla sieci Wirtualnej.

### <a name="solution"></a>Rozwiązanie

Sprawdzanie poprawności konfiguracji sieciowej grupy zabezpieczeń, Routing zdefiniowany przez użytkownika i DNS, przechodząc przez następujące kroki:
* Sprawdź grup zabezpieczeń sieci skojarzona z podsiecią bramy aplikacji. Upewnij się, czy komunikacji wewnętrznej bazy danych nie jest zablokowany.
* Sprawdź Routing zdefiniowany przez użytkownika skojarzona z podsiecią bramy aplikacji. Upewnij się, że trasa zdefiniowana przez użytkownika nie jest kierowanie ruchu od podsieci wewnętrznej bazy danych — na przykład wyszukać routingu do sieci wirtualnych urządzeń sieciowych lub domyślne trasy anonsowane do podsieci bramy aplikacji za pośrednictwem usługi ExpressRoute/sieci VPN.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzureRmVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Sprawdź skuteczne sieciowej grupy zabezpieczeń i tras za pomocą wewnętrznej bazy danych maszyny Wirtualnej

```powershell
Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzureRmEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Sprawdź występowanie niestandardowe DNS w sieci wirtualnej. DNS można sprawdzić, patrząc na szczegółowe informacje o właściwościach sieci wirtualnej w danych wyjściowych.

```json
Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Jeśli jest obecny, upewnij się, że serwer DNS jest w stanie poprawnie rozpoznać nazwy FQDN składowej puli zaplecza.

## <a name="problems-with-default-health-probe"></a>Problemy z domyślnej funkcji badania kondycji

### <a name="cause"></a>Przyczyna

502 błędy mogą być także wskaźniki częste, że domyślnej funkcji badania kondycji nie jest w stanie nawiązać połączenie maszyny wirtualne zaplecza. Gdy zostanie zainicjowane wystąpienie bramy Application Gateway, automatycznie konfiguruje domyślne sondy kondycji do każdego BackendAddressPool przy użyciu właściwości parametr BackendHttpSetting. Dane wejściowe użytkownika nie jest wymagane do ustawienia tej sondy. W szczególności w przypadku skonfigurowania reguły równoważenia obciążenia, tworzone jest skojarzenie między parametr BackendHttpSetting i BackendAddressPool. Domyślnej funkcji badania jest skonfigurowany dla każdego z tych skojarzeń i Application Gateway inicjuje połączenie okresowego sprawdzania każdego wystąpienia w BackendAddressPool na port określony w elemencie parametr BackendHttpSetting. Poniższa lista zawiera wartości skojarzone z domyślnej funkcji badania kondycji.

| Właściwość sondy | Wartość | Opis |
| --- | --- | --- |
| Adres URL sondy |http://127.0.0.1/ |Ścieżka adresu URL |
| Interval |30 |Interwał sondy w sekundach |
| Limit czasu |30 |Sondy czasu w sekundach |
| Próg złej kondycji |3 |Sonda liczbę ponownych prób. Serwer zaplecza jest oznaczony w dół po liczba niepowodzeń sondy kolejnych osiąga próg złej kondycji. |

### <a name="solution"></a>Rozwiązanie

* Upewnij się, że domyślna witryna jest skonfigurowana i nasłuchuje na 127.0.0.1.
* Jeśli parametr BackendHttpSetting określa portu innego niż 80, domyślnej witryny, należy skonfigurować do nasłuchiwania na tym porcie.
* Wywołanie http://127.0.0.1:port powinien zostać zwrócony kod wyniku protokołu HTTP 200. Powinna to być zwrócona przed upływem limitu czasu 30 sekund.
* Upewnij się, że skonfigurowany port jest otwarty i że nie istnieją żadne reguły zapory lub grupy zabezpieczeń sieci platformy Azure, które blokować ruch przychodzący lub wychodzący na porcie skonfigurowanym.
* Jeśli klasyczne maszyny wirtualne platformy Azure lub usługi w chmurze jest używana w pełni kwalifikowaną nazwę domeny lub publiczny adres IP, upewnij się, że odpowiednie [punktu końcowego](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) jest otwarty.
* Jeśli maszyna wirtualna jest skonfigurowana za pomocą usługi Azure Resource Manager i znajduje się poza siecią wirtualną, gdzie usługa Application Gateway jest wdrażana, [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md) musi być skonfigurowana do Zezwalaj na dostęp na odpowiedni port.

## <a name="problems-with-custom-health-probe"></a>Problemy z sondy kondycji niestandardowe

### <a name="cause"></a>Przyczyna

Niestandardowe sondy kondycji zezwolić na dodatkową elastyczność na potrzeby domyślnego badania zachowanie. Korzystając z niestandardowych sond, użytkownicy mogą skonfigurować interwał sondowania, adres URL i ścieżki do testowania i ile odpowiedzi nie powiodło się, aby zaakceptować przed oznaczeniem wystąpienia pulę zaplecza o złej kondycji. Zostaną dodane następujące dodatkowe właściwości.

| Właściwość sondy | Opis |
| --- | --- |
| Name (Nazwa) |Nazwa sondy. Ta nazwa jest używana do odwoływania się do sondowania w ustawieniach protokołu HTTP zaplecza. |
| Protokół |Protokół używany do wysyłania sondy. Sonda korzysta z protokołu, zdefiniowane w ustawieniach HTTP zaplecza |
| Host |Nazwa hosta, aby wysłać sondy. Dotyczy tylko wtedy, gdy połączenia obejmujące wiele lokacji jest skonfigurowana w usłudze Application Gateway. To różni się od nazwy hosta maszyny Wirtualnej. |
| Ścieżka |Ścieżka względna sondy. Nieprawidłowa ścieżka zaczyna się od "/". Sonda jest wysyłana do \<protokołu\>://\<hosta\>:\<portu\>\<ścieżki\> |
| Interval |Interwał sondy w sekundach. Jest to odstęp czasu między dwóch następujących po sobie sondy. |
| Limit czasu |Sonda limitu czasu w sekundach. Jeśli prawidłowe odpowiedzi nie zostanie odebrany w ramach tego limitu czasu, sondy jest oznaczony jako zakończony niepowodzeniem. |
| Próg złej kondycji |Sonda liczbę ponownych prób. Serwer zaplecza jest oznaczony w dół po liczba niepowodzeń sondy kolejnych osiąga próg złej kondycji. |

### <a name="solution"></a>Rozwiązanie

Sprawdź, czy niestandardowa sonda kondycji jest skonfigurowane poprawnie jako zgodnie z poprzednią tabelą. Oprócz powyższych kroków rozwiązywania problemów również zapewnić następujące okoliczności:

* Upewnij się, że sonda poprawnie określono zgodnie [przewodnik](application-gateway-create-probe-ps.md).
* Jeśli usługa Application Gateway jest skonfigurowana dla jednej witryny, domyślnie hosta należy podać nazwę jako "127.0.0.1", chyba że inaczej skonfigurowane w niestandardowej sondy.
* Upewnij się, że wywołanie http://\<hosta\>:\<portu\>\<ścieżki\> zwraca kod wyniku protokołu HTTP 200.
* Upewnij się, że interwał, limit czasu i UnhealtyThreshold znajdują się w dopuszczalnym zakresie.
* Jeśli za pomocą sondy protokołu HTTPS, upewnij się, że serwera wewnętrznej bazy danych nie wymaga SNI, konfigurując certyfikat fallback na serwerze wewnętrznej bazy danych.

## <a name="request-time-out"></a>Limit czasu żądania

### <a name="cause"></a>Przyczyna

Po odebraniu żądania użytkownika usługa Application Gateway stosuje skonfigurowane reguły na żądanie i kieruje je do wystąpienia puli zaplecza. Czeka konfigurowalnym interwale czasu odpowiedzi z wystąpienia zaplecza. Domyślnie jest to interwał **30 sekund**. Jeśli usługa Application Gateway nie otrzymasz odpowiedzi od aplikacji zaplecza, w tym interwał, żądanie użytkownika widział 502 błąd.

### <a name="solution"></a>Rozwiązanie

Usługa Application Gateway umożliwia użytkownikom do skonfigurowania tego ustawienia za pośrednictwem parametr BackendHttpSetting, który można następnie zastosować do różnych pul. Różnych pul zaplecza może mieć inny parametr BackendHttpSetting i dlatego innego żądania limitu czasu skonfigurowane.

```powershell
    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Empty BackendAddressPool

### <a name="cause"></a>Przyczyna

Jeśli Application Gateway nie ma maszyn wirtualnych lub zestawu skalowania maszyn wirtualnych skonfigurowanych w puli adresów zaplecza go nie można skierować każde żądanie klienta i zgłasza błąd Zła brama.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że pula adresów zaplecza nie jest pusta. Można to zrobić za pośrednictwem programu PowerShell, interfejsu wiersza polecenia lub portalu.

```powershell
Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Dane wyjściowe z poprzedniego polecenia cmdlet powinna zawierać puli adresów zaplecza niepusta. Poniżej znajduje się przykład gdzie dwie pule powrócisz, które są skonfigurowane przy użyciu nazwy FQDN lub adresów IP dla maszyn wirtualnych w wewnętrznej bazie danych. Stan aprowizacji BackendAddressPool musi mieć wartość "Succeeded".

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

Jeśli wszystkie wystąpienia elementu BackendAddressPool jest w złej kondycji, następnie usługa Application Gateway nie musiałby dowolnego zaplecza na żądanie użytkownika trasy. Przyczyną może być również wymagane w przypadku wystąpień zaplecza są w dobrej kondycji, ale nie ma wymaganej aplikacji, które są wdrożone.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że wystąpienia są w dobrej kondycji, a aplikacja jest poprawnie skonfigurowana. Sprawdź, czy wystąpień zaplecza może odpowiadać na polecenia ping z inną maszyną Wirtualną w tej samej sieci wirtualnej. Jeśli skonfigurowano publiczny punkt końcowy, upewnij się, że żądanie przeglądarki do aplikacji sieci web zdatne do użytku.

## <a name="next-steps"></a>Kolejne kroki

Jeśli poprzednie kroki nie rozwiązują ten problem, otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).

