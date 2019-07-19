---
title: Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia platformy Azure
titlesuffix: Azure Load Balancer
description: Jak Konfigurowanie trybu dystrybucji dla modułu równoważenia obciążenia platformy Azure do obsługi koligacji IP źródła.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 98fdf76dc2e1cb8171e7b0b37216d5f5405a1e6a
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275437"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Tryb dystrybucji skrótów

Domyślny tryb dystrybucji dla modułu równoważenia obciążenia Azure jest skrótem 5-elementowej spójnej kolekcji. Spójna kolekcja znajdująca się składa się ze źródłowego adresu IP, port źródłowy, docelowy adres IP, port docelowy i typu protokołu. Wartość skrótu służy do mapowania ruchu do dostępnych serwerów i algorytm zapewnia lepkości tylko w sesji transportu. Pakiety, które znajdują się w tej samej sesji są kierowane do tego samego wystąpienia adres IP (DIP) centrum danych za punkt końcowy z równoważeniem obciążenia. Gdy klient uruchamia nową sesję z tego samego źródłowego adresu IP, port źródłowy zmiany i powoduje, że ruchu przejść do innego punktu końcowego adresu DIP.

![Tryb dystrybucji bazujących na skrótach 5-elementowe spójne kolekcje](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Tryb koligacji IP źródła

Moduł równoważenia obciążenia można skonfigurować w taki sposób, przy użyciu trybu dystrybucji koligacji IP źródła. W tym trybie dystrybucji jest również nazywany koligacja sesji lub koligacja adresu IP klienta. Tryb używa 2-krotka (źródłowy adres IP i docelowy adres IP) lub 3-krotka (źródłowy adres IP, docelowy adres IP i protokołu typ) wyznaczania wartości skrótu być mapowany ruchu do dostępnych serwerów. Za pomocą koligacji IP źródła, połączeń, które są inicjowane z komputera klienta, przejdź do tego samego punktu końcowego adresu DIP.

Na poniższym rysunku przedstawiono konfigurację 2-elementowej spójnej kolekcji. Zwróć uwagę, jak 2-Krotka jest wykonywany przez moduł równoważenia obciążenia do maszyny wirtualnej 1 (MW1). Maszyna VM1 następnie kopia zapasowa jest tworzona maszyna VM2 i VM3.

![Tryb dystrybucji koligacji sesji 2-krotka](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Tryb koligacji IP źródła rozwiązuje niezgodności między równoważenia obciążenia platformy Azure i bramy usług pulpitu zdalnego (RD Gateway). Korzystając z tego trybu, można utworzyć farmę serwerów usługi Brama usług pulpitu zdalnego w usłudze w chmurze.

Inny scenariusz przypadków użycia jest przekazywanie nośników. Przekazywanie danych odbywają się za pośrednictwem protokołu UDP, ale na płaszczyźnie kontroli odbywa się za pośrednictwem protokołu TCP:

* Klient inicjuje sesję protokołu TCP do równoważenia obciążenia publiczny adres i zostaje skierowany do określonego adresu DIP. Kanał pozostaje aktywna w celu monitorowania kondycji połączenia.
* Nowa sesja protokołu UDP na tym samym komputerze klienckim jest inicjowane tego samego ze zrównoważonym obciążeniem publicznego punktu końcowego. Połączenie jest kierowany do tego samego punktu końcowego adresu DIP, ponieważ poprzednie połączenie TCP. Przekazywanie nośników mogą być wykonywane przy wysokiej przepływności, przy zachowaniu kanał kontrolny, za pośrednictwem protokołu TCP.

> [!NOTE]
> Zmiana zestawu z równoważeniem obciążenia przez usunięcie lub dodanie maszyny wirtualnej jest ponownie przeliczana dystrybucji żądań klientów. Użytkownik nie może zależeć od nowych połączeń z istniejących klientów do kończy się na tym samym serwerze. Ponadto za pomocą źródłowego adresu IP trybu dystrybucji koligacji może spowodować nierówne dystrybucji ruchu. Klientów z systemami za serwery proxy może być traktowany jako jeden wniosek unikatowych klientów.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurowanie ustawień koligacji IP źródła

Dla maszyn wirtualnych wdrożonych przy użyciu usługi Resource Manager zmień ustawienia dystrybucji modułu równoważenia obciążenia na istniejące reguły równoważenia obciążenia za pomocą programu PowerShell. Spowoduje to zaktualizowanie trybu dystrybucji: 

```powershell
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Dla klasycznych maszyn wirtualnych zmieniać ustawienia dystrybucji za pomocą programu Azure PowerShell. Dodawanie punktu końcowego platformy Azure do maszyny wirtualnej i Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Ustaw wartość `LoadBalancerDistribution` element dla wymaganej ilości równoważenia obciążenia. Określ sourceIP Równoważenie obciążenia sieciowego 2-krotka (źródłowy adres IP i docelowy adres IP). Określ sourceIPProtocol dla 3-krotka (źródłowy adres IP, docelowy adres IP i protokołu typ) załadować równoważenia. Określ przez domyślne zachowanie równoważenia obciążenia w 5-elementowej spójnej kolekcji.

Pobierz konfigurację trybu dystrybucji modułu równoważenia punktu końcowego obciążenia za pomocą tych ustawień:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

Gdy `LoadBalancerDistribution` element nie jest obecny, usługa Azure Load Balancer używa domyślny algorytm 5-elementowej spójnej kolekcji.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurowanie trybu dystrybucji na zestaw punktów końcowych z równoważeniem obciążenia

Punkty końcowe są częścią zestawu końcowy z równoważeniem obciążenia, tryb dystrybucji muszą zostać skonfigurowane w zestawie końcowy z równoważeniem obciążenia:

```azurepowershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurowanie trybu dystrybucji dla punktów końcowych usługi w chmurze

Aby zaktualizować usługę w chmurze, należy użyć zestawu Azure SDK dla platformy .NET 2.5. Ustawienia punktu końcowego usług w chmurze są dokonywane w pliku csdef. Aby zaktualizować trybu dystrybucji modułu równoważenia obciążenia dla wdrożenia usług Cloud Services, uaktualnianie wdrożenia jest wymagany.

Oto przykład .csdef zmiany ustawień punktu końcowego:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>Przykład interfejsu API

Poniższy przykład pokazuje, jak zmienić konfigurację trybu dystrybucji modułu równoważenia obciążenia dla określonego zestawu z równoważeniem obciążenia w danym wdrożeniu. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Zmień tryb dystrybucji dla wdrożonej zrównoważonym obciążeniu

Aby zmienić istniejącą konfigurację wdrożenia przy użyciu modelu klasycznym wdrożeniu platformy Azure. Dodaj `x-ms-version` nagłówek i ustawia wartość do wersji 2014-09-01 lub nowszej.

#### <a name="request"></a>Żądanie

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Wcześniej opisany, ustaw `LoadBalancerDistribution` elementu sourceIP 2-krotka koligacji, sourceIPProtocol 3-krotka koligacji lub brak nie koligacji (5-elementowe spójne kolekcje koligacja).

#### <a name="response"></a>Odpowiedź

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie wewnętrznego modułu równoważenia obciążenia na platformie Azure](load-balancer-internal-overview.md)
* [Wprowadzenie do konfigurowania modułu równoważenia obciążenia dostępnego z Internetu](load-balancer-get-started-internet-arm-ps.md)
* [Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
