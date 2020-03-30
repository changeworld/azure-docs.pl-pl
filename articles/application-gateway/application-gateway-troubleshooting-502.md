---
title: Rozwiązywanie problemów z błędami nieprawidłowej bramy — brama aplikacji platformy Azure
description: 'Dowiedz się, jak rozwiązywać problemy z błędem serwera bramy aplikacji: 502 — serwer sieci Web otrzymał nieprawidłową odpowiedź, działając jako brama lub serwer proxy.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130474"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Rozwiązywanie problemów z nieprawidłową bramą w usłudze Application Gateway

Dowiedz się, jak rozwiązywać problemy z błędami nieprawidłowej bramy (502) odebranych podczas korzystania z usługi Azure Application Gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

Po skonfigurowaniu bramy aplikacji jednym z błędów, które mogą być widoczne, jest "Błąd serwera: 502 — serwer sieci Web odebrał nieprawidłową odpowiedź, działając jako brama lub serwer proxy". Ten błąd może wystąpić z następujących głównych powodów:

* NSG, UDR lub Niestandardowy DNS blokuje dostęp do członków puli wewnętrznej bazy danych.
* Maszyny wirtualne zaplecza lub wystąpienia zestawu skalowania maszyny wirtualnej nie odpowiadają na domyślną sondę kondycji.
* Nieprawidłowa lub niewłaściwa konfiguracja niestandardowych sond kondycji.
* Pula zaplecza bramy aplikacji platformy Azure [nie jest skonfigurowana ani pusta.](#empty-backendaddresspool)
* Żadna z maszyn wirtualnych lub wystąpień w [zestawie skalowania maszyny wirtualnej nie jest w dobrej kondycji](#unhealthy-instances-in-backendaddresspool).
* [Żądanie limit czasu lub problemy](#request-time-out) z łącznością z żądaniami użytkowników.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Sieciowa grupa zabezpieczeń, trasa zdefiniowana przez użytkownika lub niestandardowy problem z systemem DNS

### <a name="cause"></a>Przyczyna

Jeśli dostęp do wewnętrznej bazy danych jest zablokowany z powodu sieciowej grupy sieciowej, UDR lub niestandardowego systemu DNS, wystąpienia bramy aplikacji nie mogą dotrzeć do puli wewnętrznej bazy danych. Powoduje to błędy sondy, co powoduje 502 błędy.

NSG/UDR może być obecny w podsieci bramy aplikacji lub podsieci, w której są wdrażane maszyny wirtualne aplikacji.

Podobnie obecność niestandardowego dns w sieci wirtualnej może również powodować problemy. Nazwa FQDN używana dla członków puli wewnętrznej bazy danych może nie zostać poprawnie rozwiązana przez serwer DNS skonfigurowany przez użytkownika dla sieci wirtualnej.

### <a name="solution"></a>Rozwiązanie

Sprawdź poprawność konfiguracji sieciowej grupy zabezpieczeń, UDR i DNS, wykonując następujące kroki:

* Sprawdź sieci zabezpieczeń skojarzone z podsiecią bramy aplikacji. Upewnij się, że komunikacja do wewnętrznej bazy danych nie jest zablokowana.
* Sprawdź UDR skojarzone z podsiecią bramy aplikacji. Upewnij się, że UDR nie kieruje ruchu z podsieci wewnętrznej bazy danych. Na przykład sprawdź, czy routing do sieciowych urządzeń wirtualnych lub tras domyślnych jest anonsowany do podsieci bramy aplikacji za pośrednictwem usługi ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Sprawdź skuteczne sieciowej sieciowej sieciowej sieciowej i trasy z wewnętrznej bazy wirtualnej

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Sprawdź obecność niestandardowego systemu DNS w sieci wirtualnej. Dns można sprawdzić, patrząc na szczegóły właściwości sieci wirtualnej w danych wyjściowych.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Jeśli jest obecny, upewnij się, że serwer DNS może poprawnie rozpoznać nazwy FQDN członka puli wewnętrznej bazy danych.

## <a name="problems-with-default-health-probe"></a>Problemy z domyślną sondą kondycji

### <a name="cause"></a>Przyczyna

502 błędy mogą być również częstymi wskaźnikami, których domyślna sonda kondycji nie może dotrzeć do maszyn wirtualnych zaplecza.

Gdy wystąpienie bramy aplikacji jest aprowizowana, automatycznie konfiguruje domyślną sondę kondycji do każdej pomocy wewnętrznejKnobranie adresów przy użyciu właściwości wewnętrznej bazy danychHttpSetting. Do ustawienia tej sondy nie jest wymagane żadne dane wejściowe użytkownika. W szczególności po skonfigurowaniu reguły równoważenia obciążenia, skojarzenie jest dokonywane między zapleczaHttpSetting i BackendAddressPool. Domyślna sonda jest skonfigurowana dla każdego z tych skojarzeń, a brama aplikacji uruchamia okresowe połączenie sprawdzania kondycji z każdym wystąpieniem w obszarze BackendAddressPool w porcie określonym w elemencie Wewnętrznej bazyhttpsetting. 

W poniższej tabeli wymieniono wartości skojarzone z domyślną sondą kondycji:

| Probe, właściwość | Wartość | Opis |
| --- | --- | --- |
| Adres URL sondy |`http://127.0.0.1/` |Ścieżka adresu URL |
| Interval |30 |Interwał sondy w sekundach |
| Limit czasu |30 |Limit czasu sondy w sekundach |
| Niezdrowy próg |3 |Liczba ponownych prób sondy. Serwer zaplecza jest oznaczony w dół po kolejnych liczba awarii sondy osiągnie próg złej kondycji. |

### <a name="solution"></a>Rozwiązanie

* Upewnij się, że domyślna witryna jest skonfigurowana i nasłuchuje w 127.0.0.1.
* Jeśli funkcja Wewnętrznej bazy danychHttpSetting określa port inny niż 80, domyślna witryna powinna być skonfigurowana do nasłuchiwania w tym porcie.
* Wywołanie `http://127.0.0.1:port` należy zwrócić kod wyniku HTTP 200. To powinno zostać zwrócone w ciągu 30-sekundowego limitu czasu.
* Upewnij się, że skonfigurowany port jest otwarty i że nie ma reguł zapory ani grup zabezpieczeń sieci platformy Azure, które blokują ruch przychodzący lub wychodzący na skonfigurowanym porcie.
* Jeśli klasyczne maszyny wirtualne platformy Azure lub usługa w chmurze jest używana z fqdn lub publiczny adres IP, upewnij się, że odpowiedni [punkt końcowy](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) jest otwarty.
* Jeśli maszyna wirtualna jest skonfigurowana za pośrednictwem usługi Azure Resource Manager i znajduje się poza siecią wirtualną, w której wdrożono bramę aplikacji, należy skonfigurować [sieciową grupę zabezpieczeń,](../virtual-network/security-overview.md) aby zezwolić na dostęp na żądanym porcie.

## <a name="problems-with-custom-health-probe"></a>Problemy z niestandardową sondą kondycji

### <a name="cause"></a>Przyczyna

Niestandardowe sondy kondycji umożliwiają dodatkową elastyczność domyślnego zachowania sondowania. Korzystając z niestandardowych sond, można skonfigurować interwał sondy, adres URL, ścieżkę do testowania i liczbę odpowiedzi nie powiodło się do zaakceptowania przed oznaczeniem wystąpienia puli zaplecza jako złej kondycji.

Dodaje się następujące dodatkowe właściwości:

| Probe, właściwość | Opis |
| --- | --- |
| Nazwa |Nazwa sondy. Ta nazwa jest używana do odwoływania się do sondy w ustawieniach HTTP zaplecza. |
| Protocol (Protokół) |Protokół używany do wysyłania sondy. Sonda używa protokołu zdefiniowanego w ustawieniach HTTP zaplecza |
| Host |Nazwa hosta do wysłania sondy. Ma zastosowanie tylko wtedy, gdy w bramie aplikacji skonfigurowano wiele lokacji. Różni się to od nazwy hosta maszyny Wirtualnej. |
| Ścieżka |Ścieżka względna sondy. Prawidłowa ścieżka zaczyna się od '/'. Sonda jest \<wysyłana do\<\>\<\>protokołu ://\<host\>: ścieżka portu\> |
| Interval |Interwał sondowania w sekundach. Jest to przedział czasu między dwoma kolejnymi sondami. |
| Limit czasu |Limit czasu sondy w sekundach. Jeśli prawidłowa odpowiedź nie zostanie odebrana w tym okresie, sonda jest oznaczona jako nie powiodła się. |
| Niezdrowy próg |Liczba ponownych prób sondy. Serwer zaplecza jest oznaczony w dół po kolejnych liczba awarii sondy osiągnie próg złej kondycji. |

### <a name="solution"></a>Rozwiązanie

Sprawdź, czy niestandardowa sonda kondycji jest poprawnie skonfigurowana jako poprzednia tabela. Oprócz powyższych kroków rozwiązywania problemów należy również zapewnić następujące kwestie:

* Upewnij się, że sonda jest poprawnie określona zgodnie z [prowadnicą](application-gateway-create-probe-ps.md).
* Jeśli brama aplikacji jest skonfigurowana dla pojedynczej lokacji, `127.0.0.1`domyślnie nazwa hosta powinna być określona jako , chyba że skonfigurowano inaczej w sondzie niestandardowej.
* Upewnij się, że wywołanie\<\>http://\<\>\<hosta: ścieżka\> portu zwraca kod wyniku HTTP 200.
* Upewnij się, że Interval, Timeout i UnhealtyThreshold znajdują się w dopuszczalnych zakresach.
* Jeśli używasz sondy HTTPS, upewnij się, że serwer wewnętrznej bazy danych nie wymaga SNI, konfigurując rezerwowy certyfikat na samym serwerze wewnętrznej bazy danych.

## <a name="request-time-out"></a>Limit czasu żądania

### <a name="cause"></a>Przyczyna

Po odebraniu żądania użytkownika brama aplikacji stosuje skonfigurowane reguły do żądania i kieruje je do wystąpienia puli zaplecza. Czeka na konfigurowalny interwał czasu dla odpowiedzi z wystąpienia zaplecza. Domyślnie ten interwał wynosi **20** sekund. Jeśli brama aplikacji nie otrzyma odpowiedzi z aplikacji zaplecza w tym interwale, żądanie użytkownika otrzymuje błąd 502.

### <a name="solution"></a>Rozwiązanie

Brama aplikacji umożliwia skonfigurowanie tego ustawienia za pośrednictwem wewnętrznej bazy danychHttpSetting, które można następnie zastosować do różnych pul. Różne pule zaplecza mogą mieć różne zapleczehttpsetting i skonfigurowany inny limit czasu żądania.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Pusta backendAddressPool

### <a name="cause"></a>Przyczyna

Jeśli brama aplikacji nie ma żadnych maszyn wirtualnych lub zestawu skalowania maszyny wirtualnej skonfigurowanego w puli adresów zaplecza, nie może kierować żadnych żądań klienta i wysyła błąd nieprawidłowej bramy.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że pula adresów zaplecza nie jest pusta. Można to zrobić za pośrednictwem programu PowerShell, interfejsu wiersza polecenia lub portalu.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Dane wyjściowe z poprzedniego polecenia cmdlet powinny zawierać niepustą pulę adresów zaplecza. W poniższym przykładzie przedstawiono dwie zwrócone pule, które są skonfigurowane z numerem FQDN lub adresami IP dla maszyn wirtualnych wewnętrznej bazy danych. Stan inicjowania obsługi administracyjnej backendAddressPool musi być "Powiódł się".

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

## <a name="unhealthy-instances-in-backendaddresspool"></a>Wystąpienia w złej kondycji w backendAddressPool

### <a name="cause"></a>Przyczyna

Jeśli wszystkie wystąpienia BackendAddressPool są w złej kondycji, brama aplikacji nie ma żadnych zaplecza do trasy żądania użytkownika do. Może to być również w przypadku, gdy wystąpienia zaplecza są w dobrej kondycji, ale nie mają wymaganej aplikacji wdrożone.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że wystąpienia są w dobrej kondycji, a aplikacja jest poprawnie skonfigurowana. Sprawdź, czy wystąpienia zaplecza mogą odpowiadać na ping z innej maszyny Wirtualnej w tej samej sieci wirtualnej. Jeśli jest skonfigurowany z publicznym punktem końcowym, upewnij się, że żądanie przeglądarki do aplikacji sieci web jest zbywalne.

## <a name="next-steps"></a>Następne kroki

Jeśli poprzednie kroki nie rozwiążą problemu, otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).

